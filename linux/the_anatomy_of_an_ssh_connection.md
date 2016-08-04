# The Anatomy of an SSH Connection

[Original URL](http://necrux.com/the-anatomy-of-an-ssh-connection/)

> Over the last couple of days I have become increasingly interested in unraveling the mysteries behind establishing an SSH connection. The process is very intricate and some of the documentation seems...

Over the last couple of days I have become increasingly interested in unraveling the mysteries behind establishing an SSH connection. The process is very intricate and some of the documentation seems scattered, so I decided to write a bit about it in the hopes that someone else may benefit.

Prior to writing this article I believed the SSH process to resemble something like this:

1) Verify the host.<br>
2) ?<br>
3) Magic?<br>
4) ??<br>
5) Authenticate the user.<br>
6) Profit.

Shockingly, there is no real magic involved! The actual process looks something like this:

1) Key Exchange.<br>
2) Cipher Negotiation.<br>
3) Message Authentication Code Negotiation.<br>
4) Verify the host.<br>
5) Authenticate the user.<br>
6) Profit.

**Key Exchange, Cipher Negotiation, & Message Authentication Code Negotiation**

Once the host has been verified, the client and server must agree on a key to use to encrypt the session. This is accomplished using a "key exchange algorithm," or KexAlgorithm; the KexAlgorithm uses asymmetric encryption.

Armed with a common key, the client and server must agree on the cipher that will be used, in conjunction with the key, to encrypt the session. It is important to note that once the actual session is established it will be using a shared key, i.e. symmetric encryption.

Lastly the client and server have to agree on a "message authentication code," or MAC. The MAC uses hashing algorithms to verify the integrity of the data that is transferred over the SSH connection.

If at any point the client and server cannot agree on these 3 elements, the connection will fail. You are likely to see one of these errors:

**KexAlgorithm Negotiation Failure**

```
Unable to negotiate a key exchange method
```

**Cipher Negotiation Failure**

```
no matching cipher found: client arcfour256 server aes128-ctr
```

**MAC Negotiation Failure**

```
no matching mac found: client hmac-md5 server hmac-sha2-512
```

As you can see, the cipher and MAC failures are a bit more verbose and allow you to easily see what algorithms the client and server accept, however the KexAlgorithm failure message is not as thorough. The easiest way to determine what the client and server accept is to start the SSH connection with level 2 verbosity debug output, `ssh -vv USER@HOST`. For this discussion we are only concerned about the kexinit portion of the output.

NOTE: For the sake of brevity, the client and server used in my example only support a couple of algorithms. Your output will be quite a bit longer.

```
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug2: kex_parse_kexinit: curve25519-sha256@libssh.org
debug2: kex_parse_kexinit: ssh-rsa
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
debug2: kex_parse_kexinit: none,zlib@openssh.com,zlib
debug2: kex_parse_kexinit: none,zlib@openssh.com,zlib
debug2: kex_parse_kexinit:
debug2: kex_parse_kexinit:
debug2: kex_parse_kexinit: first_kex_follows 0
debug2: kex_parse_kexinit: reserved 0
debug2: kex_parse_kexinit: curve25519-sha256@libssh.org
debug2: kex_parse_kexinit: ssh-rsa,ecdsa-sha2-nistp256
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
debug2: kex_parse_kexinit: none,zlib@openssh.com
debug2: kex_parse_kexinit: none,zlib@openssh.com
debug2: kex_parse_kexinit:
debug2: kex_parse_kexinit:
debug2: kex_parse_kexinit: first_kex_follows 0
debug2: kex_parse_kexinit: reserved 0
```

Now lets dissect that output a bit further:

**KexAlgorithms Accepted by the Client**

```
debug2: kex_parse_kexinit: curve25519-sha256@libssh.org
```

**Host Key Signature Types Accepted by the Client** (see "Host Verification")

```
debug2: kex_parse_kexinit: ssh-rsa
```

**Ciphers Accepted by the Client**

```
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
```

**MACs Accepted by the Client**

```
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
```

**KexAlgorithms Accepted by the Server**

```
debug2: kex_parse_kexinit: curve25519-sha256@libssh.org
```

**Host Key Types the Server Can Provide** (see "Host Verification")****

```
debug2: kex_parse_kexinit: ssh-rsa,ecdsa-sha2-nistp256
```

**Ciphers Accepted by the Server**

```
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
debug2: kex_parse_kexinit: aes128-ctr,aes192-ctr,aes256-ctr
```

**MACs Accepted by the Server**

```
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
debug2: kex_parse_kexinit: hmac-sha1-96,hmac-md5-96
```

You are able to change any of the algorithms accepted by your client or server by using the `KexAlgorithms`, `Ciphers`, and `MACs` options in the client/server configuration files, _/etc/ssh/ssh_config_ and _/etc/ssh/sshd_config_ respectively. There is a forth option, `Cipher` (without the 's'), that affects ciphers accepted for SSH protocol 1; these changes will be ignored as protocol 1 ought to be disabled on all SSH servers. Newer SSH clients also have a built in command to see what algorithms the client can support:

`ssh -Q kex`<br>
`ssh -Q cipher`<br>
`ssh -Q mac`

**Host Verification**

Each SSH server will have a unique "host key" that is randomly generated when the server is set-up; the server stores the key(s) in _/etc/ssh/ssh_host_\__key.pub_. When you attempt to make an SSH connection to the server, the client will use a "host key signature algorithm" to authenticate the host. Newer SSH clients have a simple command you can run to see the list of host key signature algorithms that you support, simply run `ssh -Q key`. You are able to modify which host key signature algorithms that you want to use by specifying `HostKeyAlgorthyms <algorithm>` in_ /etc/ssh/ssh_config*. Doing so does NOT affect the output of `ssh -Q key` as the client still supports the other algorithms, you are just choosing not to use them.

The first time you connect to an SSH server you will be greeted with the following message:

```
The authenticity of host 'localhost (::1)' can't be established.
RSA key fingerprint is xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx.
Are you sure you want to continue connecting (yes/no)?
```

Assuming you say yes, the server identify will be stored in _~/.ssh/known_hosts_. If at any point the "host key" of that server changes you will see something like this:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

This _could_ mean that something unseemly is happening, however in my world it is far more likely that the remote host identification has truly changed. If you decide the change to be legitimate you can easily remove the entry from _~/.ssh/known_hosts_ by running `ssh-keygen -R HOST`; if the host is using a custom port then the syntax is `ssh-keygen -R [HOST]:PORT`. Alternatively you can manually edit _~/.ssh/known_hosts_, or delete the entire file (please don't). The benefit of using ssh-keygen is that the contents are automatically backed up to _~/.ssh/known_hosts.old_.

**User Authentication**

Finally we move on to user authentication. This part tends to be pretty well known and documentation on the matter is abundant, so I will not spend too much time on the subject. The 2 primary mechanisms to authenticate a user are password and public key. Public key authentication is obviously preferred and is a form of asymmetric encryption since it uses public and private keys. When running an SSH connection with level 1 verbosity debug output, `ssh -v USER@HOST`, you can see what user authentication mechanisms are allowed by the server:

```
debug1: Authentications that can continue: publickey,password
```

----**Share this post:**[![facebook](http://necrux.com/wp-content/plugins/social-media-feather/synved-social/image/social/regular/96x96/facebook.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=http%3A%2F%2Fnecrux.com%2Fthe-anatomy-of-an-ssh-connection%2F&t=The%20Anatomy%20of%20an%20SSH%20Connection&s=100&p%5Burl%5D=http%3A%2F%2Fnecrux.com%2Fthe-anatomy-of-an-ssh-connection%2F&p%5Bimages%5D%5B0%5D=&p%5Btitle%5D=The%20Anatomy%20of%20an%20SSH%20Connection "Share on Facebook")[![twitter](http://necrux.com/wp-content/plugins/social-media-feather/synved-social/image/social/regular/96x96/twitter.png "Share on Twitter")](http://twitter.com/share?url=http%3A%2F%2Fnecrux.com%2Fthe-anatomy-of-an-ssh-connection%2F&text=Check%20out%20this%20tutorial "Share on Twitter")[![google_plus](http://necrux.com/wp-content/plugins/social-media-feather/synved-social/image/social/regular/96x96/google_plus.png "Share on Google+")](https://plus.google.com/share?url=http%3A%2F%2Fnecrux.com%2Fthe-anatomy-of-an-ssh-connection%2F "Share on Google+")[![reddit](http://necrux.com/wp-content/plugins/social-media-feather/synved-social/image/social/regular/96x96/reddit.png "Share on Reddit")](http://www.reddit.com/submit?url=http%3A%2F%2Fnecrux.com%2Fthe-anatomy-of-an-ssh-connection%2F&title=The%20Anatomy%20of%20an%20SSH%20Connection "Share on Reddit")[![linkedin](http://necrux.com/wp-content/plugins/social-media-feather/synved-social/image/social/regular/96x96/linkedin.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=http%3A%2F%2Fnecrux.com%2Fthe-anatomy-of-an-ssh-connection%2F&title=The%20Anatomy%20of%20an%20SSH%20Connection "Share on Linkedin")
