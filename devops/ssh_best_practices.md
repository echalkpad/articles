# SSH: Best practices

[Original URL](https://blog.0xbadc0de.be/archives/300)

> 17/01/16 - 12:45am The comments around the last OpenSSH issue (CVE 2016-0777, you must read excellent Qualys' analysis if you're interested with the details), I noticed that many people...

17/01/16 - 12:45am

The comments around the last OpenSSH issue ([CVE 2016-0777](https://access.redhat.com/articles/2123781), you must read excellent [Qualys' analysis](https://www.qualys.com/2016/01/14/cve-2016-0777-cve-2016-0778/openssh-cve-2016-0777-cve-2016-0778.txt) if you're interested with the details), I noticed that many people were not aware of some basic features of OpenSSH. I will attempt to give a few advises, prioritized in feasibility order, and with graphical annotations:

![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png) Very easy to set up.

![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png) Requires a lot of work to set up.

![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png) Will provide you some protections against difficult attacks.

![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png) Will protect against very simple or effective attacks.

Most of the advises here also apply to other SSH clients, like Putty, with slight variations in the procedure. This small guide can be considered as complementary to other guides on the net, such as [Mozilla's one](https://wiki.mozilla.org/Security/Guidelines/OpenSSH).

## Use public key authentication ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

This is the recommendation #1\. Passwords suck. They are a constant source of security problems, and most intrusions can, at some point, be linked to the cracking or stealing of a password. Passwords are:

- Crackable: when a host is compromised, password hashes can be dumped, then cracked with GPGPU tools like oclHashCat or JtR. It means every computer you have a password for can potentially be used to guess your password.
- Sniffable: you're using a protocol that is mostly safe against passive eavesdropping thanks to encryption. Congratulations for not using Telnet! But experience shows that hackers still steal SSH passwords, either with Man-in-the-middle attacks (you didn't check host keys correctly), or by installing a backdoor in the SSH server, that logs passwords as users try to authenticate (very effective trick once you got root on a server).
- Reused: I don't know a single place that enforces a different password per server policy. Worse, users tend to reuse passwords between external services (facebook, etc.) and work.
- Poorly managed: Throw the first stone if you have never seen a spreadsheet full of administrative passwords. Password managers integrate well with web browsers but almost never with SSH clients.
- Something you know. And write down everywhere because it's not possible to remember.

On the other side, private/public keys have many advantages. Keys are:

- Uncrackable. Let's avoid the nitpick about the crypto shortcomings (think Debian PRNG debacle), in principle nobody should be able to get your private key with the public key only.
- Unsniffable. During an authentication attempt, the SSH server verifies that you know the private key by checking a signature. This signature will not help anyone in figuring out what your private key is, so it's safe to connect to untrusted hosts, that may have backdoors. Moreover, private key authentication is not replayable, as parts of the key exchange (the session ID) is used in the signed payload. A computer that makes a man-in-the-middle attack between you and the real server will not be able to authenticate to the remote host, even if you override the host key mismatch warning message.
- Can be properly managed, with an authentication agent and passphrase encryption.
- Easy to communicate safely: if you need someone to give access to a service, just mail him a copy of your public key. No more default passwords or cleartext passwords exchanged by email, and forgotten to be reset.
- Something you have. And when properly managed, it's encrypted with something you know.

### Set up public key authentication

The first step is to create a key pair and choosing a passphrase to protect it:

```
aris@korell:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/aris/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/aris/.ssh/id_rsa.
Your public key has been saved in /home/aris/.ssh/id_rsa.pub.
The key fingerprint is:
11:ad:87:e3:fc:68:86:df:12:ca:89:00:22:8b:94:48 aris@korell
The key's randomart image is:
+--[ RSA 2048]----+
|        ..       |
| E       ..      |
|o .     .o       |
|=o      +..      |
|=o     oSo       |
|o .     +        |
|   . o + +       |
|    . = =..      |
|       +...      |
+-----------------+
```

If you are using recent OpenSSH versions (>= 6.5) on all your systems, choose the ed25519 key type:

```
aris@korell:~$ ssh-keygen -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/aris/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/aris/.ssh/id_ed25519.
Your public key has been saved in /home/aris/.ssh/id_ed25519.pub.
The key fingerprint is:
26:54:08:bf:3b:12:e1:f5:fb:77:44:d9:dd:fe:eb:1b aris@korell
The key's randomart image is:
+--[ED25519  256--+
|    .. ..        |
|     ...         |
|    . +        oo|
|   . + o      o +|
|    o o S    . . |
|     . + .    . .|
|    . o .    . E.|
|     . . .  . . o|
|          .. ..+o|
+-----------------+
```

Do not create DSA keys, this key type is insecure and obsolete.

Public keys have comments, that can be changed to reflect the intended use of that key or the computer that generated it:

```
aris@korell:~$ cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/NDvxlETUXAi/pEKRlq6ehVXwj2vwrGso2AJ6imjA
TAFFns4dhIzn/sfnBpAcSwEjcoP6ZG6w4YW5rgd+sHAKag0+gT8CgLNj71BjFlwi+ZgtUqCJaLX8Tds/
GIlPBPrYlrsegLvKdMTvXordymcQAHTft1doKgnzUgVGC+sk4N4ZTFJXEb78gqILb6Sw1PedKR9iRbf8
rEuRGMKLfqNnbuDxCgHGUeyoqXLd8cKJq0e0KyHwYbsBTJcEI4PNWVpUc62b1Do/1gdYKCOodAYSHWmW
hoY1YdYPpo8I2Wv9lugQG1Erzo7EuyLZ1c1cKyhAbrC5LxM07r4bxpk5VDdP aris@korell
```

This is also the content you will need to copy in the ~/.ssh/authorized_keys file to be authenticated with the key pair.

```
aris@korell:~$ cat .ssh/id_rsa.pub >> .ssh/authorized_keys
```

You can now use your key to authenticate on that computer. Be careful that the .ssh directory must be owned by your user and not be world writable, and the authorized_keys file be owned by your user with permissions 0600.

```
aris@korell:~$ ssh localhost
Enter passphrase for key '/home/aris/.ssh/id_rsa':
aris@korell:~$
```

For your convenience, the ssh-copy-id tool can be used to avoid having to manually edit the authorized_keys file.

## Encrypt your private keys ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

It is unfortunately too easy to create keys without passphrases. While this may be needed in a few limited occasions (automated backup script for instance), this is never a good idea. The most common excuse against passphrase is that full disk encryption is enabled, or that an attacker that has access to the computer already has everything. This is not right for the following reasons:

- Full disk encryption protects your computer when it's powered off. When the computer is powered on, it's working exactly the same as unencrypted computers. Believe it or not, most online computers attacks target powered on computers (mind blown !).
- A successful computer attack does not always target remote code execution and is sometimes more limited. This [Firefox vulnerability](https://blog.mozilla.org/security/2015/08/06/firefox-exploit-found-in-the-wild/) was exploited in the wild to steal SSH keys. Common vulnerabilities like XXE (which may be very common on desktop applications, as much as server apps) are being used to leak file contents, sometimes leading to system compromise thanks to unprotected keys and passwords. The recent OpenSSH issue can be abused to retrieve parts of the private key files, as stored on disk, in memory.

### So your key has no passphrase?

Bummer. Let's fix this:

```
aris@korell:~$ ssh-keygen -p -f ~/.ssh/id_rsa
Enter old passphrase:
Key has comment '/home/aris/.ssh/id_rsa'
Enter new passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved with the new passphrase.
```

You can of course create a new key and remove the old key from systems.

## Check the Host keys ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

The host key is the server's public key, that is used to authenticate the server and ensure you are well communicating with the target server. Unlike SSL/TLS that uses an authority-based model, SSH uses a public key pinning at first use model. This is what happens when you connect somewhere for the first time:

```
aris@korell:~$ ssh localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is 17:ed:bf:78:78:da:45:b6:51:25:39:7d:35:db:47:69.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
```

The fingerprint is unique for every SSH server. If that server is on an untrusted network, always verify that the fingerprint is right through a second channel, less prone to interception (e.g. an email from the sysadmin who gave you shell access). OpenSSH will not ask to to confirm the fingerprint anymore, and will store it in the known_hosts file. However, there's a chance that you will receive this message:

```
aris@korell:~$ ssh localhost
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
17:ed:bf:78:78:da:45:b6:51:25:39:7d:35:db:47:69.
Please contact your system administrator.
Add correct host key in /home/aris/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/aris/.ssh/known_hosts:3
  remove with: ssh-keygen -f "/home/aris/.ssh/known_hosts" -R localhost
ECDSA host key for localhost has changed and you have requested strict checking.
Host key verification failed.
```

**Do not remove the key from known hosts** until you have discovered why the key changed: reinstallation, new server, IP address recycled, etc. Ask for your sysadmin to give you a fresh fingerprint that you can trust. To recover the fingerprint of the local server, use ssh-keyscan:

```
aris@korell:~$ ssh-keyscan korell
# korell SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.4
korell ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8I/hLGhvz0+QdNLZB6cTYZMAdAkTNPyra0
qgnK9TxERbfV/Jakh1x+P9qeIv+yX/zr/PdETAwNQq3gywu/E6QAHk9Yj4c6Av4TZKHwasbRgkKkJuSD
Wk2UG8Zp+9PNb5xH8QyKKdGGMzgH1RLUgWbrfV5Kg1nhhGCNpz/zfqNrcpGuRnDwDeFCCS0AIBZhIaw9
s+HH8keGaKZDcvmNYqrMz0uoWjg0rs2Pf9+nxmaBjjOz6zgRj1S5G+mSPsbDp18kpWSiX/6cU4mAbUH/
UPgYGV1ZdR2YHRpAvtd4SGmMCuEQmEtD3TAGpJWM15zspBojfLPKunrVhJBxZwU40MT
# korell SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.4
korell ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBB
I4UG0+B0QUtM9ItGcFNmCmoe3CIo2oAeSo0gyIyu+Q994t8cGk6kD+mlKmmy0w65h4DC9PzfQlNL09Ar
7YxPqs=
```

This line can be directly copy and pasted in the known_hosts file. Or, if you prefer you can retrieve the hashed fingerprints using this command line:

```
aris@korell:~$ f=$(mktemp) && ssh-keyscan korell > $f && ssh-keygen -l -f $f && rm $f
# korell SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.4
# korell SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.4
2048 fe:70:a1:43:70:71:55:37:28:23:16:61:2e:83:ef:73 korell (RSA)
256 17:ed:bf:78:78:da:45:b6:51:25:39:7d:35:db:47:69 korell (ECDSA)
```

Unfortunately ssh-keygen does not support input from stdin, so this example is slightly more complicated than it should.

## Use SSH Agent ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

Did you notice in our public key authentication example that ssh will ask for the passphrase every time we log in. This is because OpenSSH will open the private key file every time we call ssh, and that's not just annoying, but not extremely secure.

An SSH Agent is a program that runs in the background, whose role is to keep private keys in memory and issue signing operations to the programs in need. There are a few different agents: OpenSSH agent, Gnome SSH agent, Pageant (putty), etc. but they work more or less the same: the agent starts with the session, sets an environment variable with the UNIX socket to the agent daemon. You can then add, list or remove keys from the agent. OpenSSH will automatically use the agent if there is one:

```
aris@korell:~$ ssh localhost id
Enter passphrase for key '/home/aris/.ssh/id_rsa':
uid=1000(aris) gid=100(users) groups=100(users)
aris@korell:~$ eval $(ssh-agent)
Agent pid 12165
aris@korell:~$ ssh-add -l
The agent has no identities.
aris@korell:~$ ssh-add .ssh/id_rsa
Enter passphrase for .ssh/id_rsa:
Identity added: .ssh/id_rsa (.ssh/id_rsa)
aris@korell:~$ ssh-add -l
2048 11:ad:87:e3:fc:68:86:df:12:ca:89:00:22:8b:94:48 .ssh/id_rsa (RSA)
aris@korell:~$ ssh localhost id
uid=1000(aris) gid=100(users) groups=100(users
```

It is not usual to manually start the SSH agent, but in this case it's needed for the demonstration. The ssh-agent command will output a small shell script that will update the SSH_AUTH_SOCK and SSH_AGENT_PID environment variables. We then evaluate that shell script, to be able to use the agent in the subsequent ssh-add and ssh commands.

The main advantage of the ssh-agent over the standard public key authentication is that all the magic happens in the ssh-agent process. The ssh process never has access to the key material, which makes it **less vulnerable to memory content leaks** (does that ring a bell ?). If you were using the SSH agent till today (**with** a passphrase), the OpenSSH client roaming bug did not impact your private keys.

The OpenSSH ssh-agent also has protection against tampering, making it hard for a hacker (without root access) to extract private keys from the cache, as most debugging interfaces will not be available.

There are third-party scripts around ssh-agent such as [Keychain](http://www.funtoo.org/Keychain), with added features for easy management of keys. I haven't tried it.

## Use a different key for every computer ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

The security of public keys answer to a slightly different threat model than passwords. With passwords, the compromise of a server means that the password being used on that server must be revoked, and changed on other servers that shared that password. Private keys cannot be compromised with a server, because they never leave the workstation. Endpoint computers can be hacked too, and if it's the case, then the particular private keys that were on that computer must be revoked and changed.

It means that for security, every computer you SSH from should have a different private key. This will help the day one of your computers is hacked: you'll be able to figure out which one was hacked and keep access from safe (not compromised) endpoints. Not all destinations require to have all of your public keys. For instance, your "play" servers don't have to allow your "work laptop" private key. It also helps a lot in restricting access from virtual machines, when you're using VMs extensively in your workflow.

It is possible to use a different key per service per device (e.g. one key for github, one key for server admin, one key for server root, etc.) but I find no security advantage in this practice.

## Disable password authentication ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

Edit your /etc/ssh/sshd_config file and add these lines:

```
PasswordAuthentication no
ChallengeResponseAuthentication no
```

Not only you won't be vulnerable to weak passwords anymore, but you won't have to deal with megabytes of garbage in the logs from SSH scanners.

Should you permit root login? It's a question of policy, not security. In places where several system administrators could log in and do some work, it's better to keep the root account closed and only work with sudo. This way, you keep track of who connected and touched the server last. If you are alone, there's no point in adding PermitRootLogin no, logging in as root may be the right thing to do, depending on the task.

```
PermitRootLogin without-password
```

is a safe configuration setting.

## Do not SSH cross-server ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

You're sitting on server A, and need to do some work on server B. You're tempted to type in "ssh B" from your shell in A. This is bad practice, because in order to reach B, you'll either need to type a password (bad, can be sniffed with a tty sniffer or patched ssh client), use a private key (on a server) and type the passphrase, or use agent forwarding (any system administrator could abuse your agent to login elsewhere – let's discuss this in a later section). Most of the time, you don't really need to connect from A to B, you just need a connection to B. File transfers being an exception.

### Hey, but I have to connect through a 'gateway' host!

Many networks don't have exposed SSH servers for every host, but a centralized host that acts as a gateway to SSH into the internal network. It's not so bad, if only all users were not expected to store their private keys there! It's the kind of server, that when owned, marks the end of a pentest. The appropriate way of using "bouncer" hosts is ProxyCommand. ProxyCommand is an OpenSSH option that tells SSH to not use a TCP/IP transport for the SSH session, but execute a command instead, and use the standard input and output of the command. You can then execute an other ssh command inside the ProxyCommand, in order to create a tunnel.

Example:

```
ssh -o "ProxyCommand=ssh A nc B 22" B
```

This command will set up a connection to B through the server A. The "ssh A nc B 22" Proxycommand will execute netcat on host A and ask it to connect to B port 22\. The outer ssh command will then use that tunnel through A to speak securely with B. Alternatively, one can use this command:

```
ssh -o "ProxyCommand=ssh -W B:22 A" B
```

This command will open a TCP forwarding channel from A to B:22 and will make it available to the outer SSH command. In order to make this setup permanent, you can add this to your ~/.ssh/config file:

```
Host B
    ProxyCommand ssh -W B:22 A
```

The advantage of this setup is that, even if the host A is compromised, no cleartext credential or key can be stolen. This is the safest way of using gateway hosts.

## Use safe algorithms ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

The are [very good articles](https://stribika.github.io/2015/01/04/secure-secure-shell.html) available online on the list of ciphers to enable and disable, with great discussions on which ones are good and which ones are less good. To be honest, not everyone in the community agrees on which ciphers are to be trusted and distrusted. This is the list of ciphers I think should be disabled because they are badly broken or weak:

- DH Key exchange: diffie-hellman-group1-sha1, diffie-hellman-group14-sha1, diffie-hellman-group-exchange-sha1
- Hostkey: ssh-dss
- Authentication: ssh-dss, ssh-dss-cert-v01@openssh.com, ssh-dss-cert-v00@openssh.com
- Ciphers: 3des-cbc, aes128-cbc, aes192-cbc, aes256-cbc, rijndael-cbc@lysator.liu.se, blowfish-cbc, cast128-cbc, arcfour, arcfour128, arcfour256 (CBC modes are [broken in SSH2](http://www.openssh.com/txt/cbc.adv))
- Mac: hmac-md5, hmac-md5-96, umac-64, hmac-md5-etm@openssh.com, umac-64-etm@openssh.com

Some people may not agree with my blacklist, consider it too small or too big, or prefer that I'd give a whitelist. Make your choice. Most of these discussions, that are very important in TLS/SSL-land, are irrelevant with SSH because **SSH is safe against downgrade attacks**. This means that whatever cipher is in front of your cipherlist is going to be used by default, and the bad ciphers are in the end of that default list already. Any attempt by an attacker to alter the cipher list exchanged by both parties will immediately be detected.

## Use agent forwarding at appropriate times ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

The SSH agent forwarding is a functionality of the SSH protocol that allows an SSH client to share its SSH agent with applications on the server. One of the biggest (mis)use of that function is to connect to a destination server through a gateway host. The problem with that setup is that agent forwarding is very dangerous. Someone having access to the agent forwarding socket may use it to authenticate with your private key to arbitrary systems.

Agent forwarding may be useful in a few situations:

- You need to transfer a big amount of data between servers, and relaying that data through your workstation makes no sense in a networking point of view (would be a bottleneck).
- A service requires a temporary connection to a third party service (e.g. a git server). I use this functionality a lot on virtual machines when I need to access git repositories.
- The server is passwordless and requires an agent to use sudo.

In these cases, it may be better to use agent forwarding, especially since it will stop you from dropping private keys on servers (bad). Protect yourself. Several options to the ssh agent exist to make it safer:

- _ssh-agent -t timeout_ allows for a timeout to be set on the validity of keys. After a certain amount of time, you'll need to type your passphrase again to unlock it.
- _ssh-add -t timeout key_ has the same effect on a particular key as starting _ssh-agent -t_.
- _ssh-add -x_ locks the agent. A password will be needed to unlock the agent with _ssh-add -X_
- _ssh-add -c key_ add a key and request that every access to that identity be confirmed (with a popup)

I strongly recommend to use ssh-add -c when using agent forwarding.

## Use SSH certificates ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

Because _not everything_ in X.509 is bad, and individual keys are difficult to manage on big installations, OpenSSH supports its own certificate format. It can be used to replace the key-pinning model of SSH with a more classical CA-based model. Explaining how to set this up could take another thousand pages and good [documents](https://ef.gy/hardening-ssh) already exist.

## Use a smartcard ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

Smartcards are the cherry on the cake when dealing with secure authentication. Smartcards are basically the hardware equivalent of SSH agents, which can do signing operations, but whose private key can (supposedly) not be extracted. I have a smartcard that I use in situations where I cannot be in full control of the computer and must get access to some resource. While some improvement could be made in the smartcard stacks in major operating systems (let's face it, it sucks everywhere), once the card has been properly [initialized](http://blog.philippklaus.de/2012/01/feitian-pki-smartcard-ftcos-pk-01c/), authenticating with smartcard is not very hard:

```
ssh -I /path/to/opensc-pkcs11.so host
```

You will then have to type the PIN to the smartcard. SSH agent also supports PKCS11 (the standard interface to smartcards), but in my limited experience, it's working for a few minutes before having to restart the agent.

Tell me if you'd like more information on how to set up SSH with smartcards or tokens.

## Remove all system passwords ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

It is possible to run systems in which no user account has a password. The /etc/shadow file doesn't contain a single hashed entry. This is possible in this way: have a key preinstalled in the root's .ssh in the VM Image that is deployed everywhere. New accounts don't receive passwords and it's only possible to log in with public key.

This works very well with single-administrator systems where nobody has to really use sudo. Unfortunately, the most common way of setting up sudo is having sudo request for a password. I will of course not tell you to use NOPASSWD, since we're talking best practice. One solution is [pam-ssh-agent-auth](https://github.com/cpick/pam-ssh-agent-auth). This [document](http://www.evans.io/posts/ssh-agent-for-sudo-authentication/) explains quite well how to set up pam-ssh-agent-auth. My biggest regret is that few distributions (at least not Ubuntu) provide it by default. In order to use it, use ssh -A to connect to the server, then type sudo -s. Your agent will pop up a warning for you to authorize the signature.

One aspect to be careful: never configure pam-ssh-agent-auth to use the identities in the home directories, and use identities in a system directory, writable by root only. Otherwise, users would be able to add their own keys and authenticate with arbitrary ssh keys, bypassing the whole authentication setup.

## Use Two-factors Authentication ![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Keyboard-Filled-100.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)![](https://blog.0xbadc0de.be/wp-content/uploads/2016/01/Security-Checked-104.png)

2FA adds a required authentication medium when connecting to the server. It can be added to either public key or password authentication. You will receive a one-time password to enter when logging in. 2FA is one of the most difficult systems to crack without social engineering.

[This guide](http://guides.webbynode.com/articles/security/ubuntu-google-authenticator.html) gives steps to set up 2FA with google authenticator.

## Closing words

Thanks Michal, pouette and wildcat for the review of this blogpost.
