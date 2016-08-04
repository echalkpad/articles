# Tyblog

[Original URL](http://blog.tjll.net/ssh-kung-fu/)

> 27 April 2014 OpenSSH is an incredible tool. Though primarily relied upon as a secure alternative to plaintext remote tools like telnet or rsh, OpenSSH (hereafter referred to as plain old ssh) has...

27 April 2014

[OpenSSH](http://www.openssh.com/) is an incredible tool. Though primarily relied upon as a secure alternative to plaintext remote tools like telnet or rsh, OpenSSH (hereafter referred to as plain old ssh) has become a swiss army knife of functionality for far more than just remote logins.

I rely on ssh every day for multiple purposes and feel the need to share the love for this excellent tool. What follows is a list for some of my use cases that leverage the power of ssh.

## Public-Key Cryptography

This is kind of a prerequisite for supercharging ssh usage. It's a pretty straightforward concept:

- Generate a public key and private key. The private key can prove ownership of a public key
- Place the public key on any servers you need to log in to
- No more password prompts

Sound good? Let's do it:

```
$ ssh-keygen -t rsa
$ cat ~/.ssh/id_rsa.pub
... your public key here ...
```

Paste that into the `~/.ssh/authorized_keys` file of any server account you need to log in to, then any subsequent

Will be automatic. Now you're prepared to really make ssh useful.

### Bonus: ssh-copy-id

As this process of copying public keys is a fairly common task, you can usually get the `ssh-copy-id` command on most platforms do to this automagically for you. For example, if you've generated your keys and want to set up key login for the user bob at the host fortknox:

```
$ brew install ssh-copy-id # (if needed)
$ ssh-copy-id bob@fortknox
```

Done!

### Trivia: ecdsa keys

While rsa keys are the traditional standard for ssh keys, ssh supports several types of asymmetric key types. For example, an Elliptic Curve Data Signature Algorithm (ecdsa) key provides roughly the same strength as rsa with dramatically fewer bits. Take an example rsa private key:

```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA5i0picr3EuP/MLLYjFn930uw5PXEfVSRAyp4B7wFFLX8gwfo
T1JQvElFSlkM160u7XOdocdcQ85FLC7ABYWEe6OaUqSmBLoYzoKVE72lyKHjXeve
zjPgAHIcZ6xpi1ua5RfeE+L/yAxx7NNK2n8IFKPQWbHOH20I6aTjVPH810DKybAK
geDdzdIdo3COp4yUotYmL3WI5oH3LZ+0QMJsyQtXWj56K2sX6Bi9dzQbID+jMRRC
ctnKE3Z+S24GdZTlslL/6fzhzVi2JSBOAdwh7s7PC7b2PJL6BlOkTBpOVNv1Ttzg
VrpybqN2emNhmi/lIKS9bTgN7ZD1ndSImVKAYwIDAQABAoIBAAL22e4YWw43OXYb
F4bXMdnKU8DfGWSzzhpIVbtjxHz7ywC0/VzoJnoGR4opk2zDojMUphcLRjjpUyK6
h2aKzaX5+WbPEARHkUI3lEvoyVXIH/F5tCjbqirXTV5YbhOJXnlM0WNYLQsafe0a
23/s2uHJKkm9bHYjJVY89WCGrUboXHIfq1zsSvunCkD8mJxP8tXZhtqn7KlHK4w+
X8t259hOGPboGcZ48I6TZ1o5GzoUIpJdVuhxvK1+RGOiE4RPgdDyC3dQQV3hc4Iq
2k/EOnDqOFtHMW1uCgnz7+sDLaEhuG823MnhRBhZEDKSQECUdfRHsX2JYC6F3fnC
1pMZtSECgYEA/Wo8RKMtNrovTruJemTjMAIe06H1UjsC46ppYEiWDTlZZsbPJ3AH
l5+9ER2FAReU+Op5UoUgmxLg7kFJFudib2cieLYKVODnIdvw8yGx895qA32xP16e
C0HRexDhzW2LBIgmSSz0+QEaYbuiQBWfq02EQ9yUbmcePX9eyY8bZwcCgYEA6IY9
8CeaM9UlQ18m5eFuM40xpjaED3sESRMCJU61rqnMPyJHFBMiR2fqQRbQoirU7qyL
/fJhfOKSkK0wHO+OBokbxxggcswLPkJnBSb6MVtqec+s7n0Oko+9Czfa4xWeHWI5
n25N+YuEEAC92RODUxQkynsk6/3wv4LqHkzcCMUCgYAOlN1Q6b7BRmdQdXQMqd90
tLqHXOtbxu98oCKeGq8fpawiQNBMqaKWM+fSI0uy62N0CzHd8LEWmzh8HR+ShM/i
LyIJphfkGGjURu6PXuH5cVPSTZo0VkJrzWa7WRZVFreIFDl3vnF+HnUhKIXGgDgG
yFgS+49C5wYTbc/Lc0OVYwKBgQDJA8Rn6NSWGp2sMIYgFVJ/noBdgKOJ/n8l7Rjd
x72o0YGQ0sE/yYrI0DzjKCYVC5IpA2HCl9dPb0/lYtNFMJNHcyBgbasfkuXlXOJS
we9o2+6gf7iwM8x1R23WVOMVjYqzPEc0XNdr9ACnFP0KvKO7Hp7vrKWunkmSRkq/
BxLxQQKBgFYi4PKlqWG9tkXoqh9Hzjn29VfUcu5QVfNGpTN4ohr1j1TFaJgBF6bJ
TSXXUErOtPIQREVOCe9OJqzT4SPVm0OZoI7qTOVp5b6tbXjY29oUcNSszAjmEySo
fMtmbBWirnFRCuZVIylokl/3uviNbIUNkNOFZnhiVy0CviDG05F/
-----END RSA PRIVATE KEY-----
```

And compare it to an ecdsa private key:

```
-----BEGIN EC PRIVATE KEY-----
MHcCAQEEIOSxjv3N24PiYulQcC7sr3RhfAtEXcnf4uRicHiFO5LhoAoGCCqGSM49
AwEHoUQDQgAE66S7FaV6O+oWuRBurSxmPzKy/gSoCqJr48IsacXpDvhNKBiJprdj
ahrB5Tk74YUTlQvZgdZSlGPVnn6OF1MI6Q==
-----END EC PRIVATE KEY-----
```

No, these are not my private keys. ☺ They're one-shot example keys.

Note that although they're cool, I wouldn't recommend trying ecdsa out in a production environment

<sup>
  <a href="http://blog.tjll.net/ssh-kung-fu/#fn:ecdsa" class="footnote">1</a>
</sup>

 – I've run into a couple of daemons during my day-to-day that wouldn't accept this key type.

## Tunnelling

Need access to a port behind a firewall? ssh has got you covered. If you need to access the remote endpoint <http://no-public-access:80> but can reach that host from another host that you can log in to (let's call it ssh-host), just try this:

```
$ ssh -L 8080:no-public-access:80 user@ssh-host
```

Then browse to <http://localhost:8080>. Your requests are being routed through `ssh-host` and hit `no-public-access:80` and are routed back to you. _Tremendously_ useful.

## Mounting Filesystems

NFS, while venerable, is pretty shoddy when it comes to securely sharing directories. You can share to an entire subnet, but anything beyond that gets tricky. Enter sshfs, the ssh _filesystem_.

I've got a machine called `wonka` trying to share the directory `/srv/factory` with the host `bucket`. Assuming that the user `charlie` on `bucket` has keys set up on `wonka`, and assuming that I've got sshfs available (install it via your package manager of choice if `which sshfs` returns nothing), try this out:

```
$ sshfs wonka:/srv/factory /mnt -o idmap=user
```

Everything is pretty self-explanatory except for the filesystem mount option `idmap`. That just tries to map the unix numeric IDs from the server to the user you're mounting the filesystem as (it's the least troublesome option.)

And presto, you're sharing a filesystem quickly, easily, and securely. The caveat here is that filesystem changes can take a little longer to propagate than with similar file-sharing schemes like SMB or NFS.

## Remote File Editing

This trick relies on the netrw capabilities of vim (a directory browsing function) and scp (a sister program of ssh.)

Netrw allows vim to browse the filesystem on the local filesystem easily (try it in a terminal with `$ vim [path]`, such as `$ vim .`. However, we can pass vim a _remote path_ using `scp://` to achieve remote file editing:

You're now browsing your home directory's contents on `wonka` within vim. Any file edits you make will be synced over on write via scp.

## Tab-Completion

Not a trick, but really useful.

Have you ssh-ed into a machine like this in the past?

```
$ ssh supercalifragilisticexpialidocious
```

Good news, you can probably do this:

and ~~ssh~~

<sup>
  <a href="http://blog.tjll.net/ssh-kung-fu/#fn:ssh-auto" class="footnote">2</a>
</sup>

 the shell will auto-complete hostnames drawn from the `~/.ssh/known_hosts` file.

## Lightweight Proxy

This is probably one of my favorite tricks, and is very useful when the need for it arises.

The previous tunnelling example was simple: we're forwarding a local port such as `8080` to a remote endpoint, and ssh handles passing packets through your intermediate ssh host. However, what if you want to place a program entirely behind an ssh host? Doing so enables you to essentially create a very lightweight proxy, independent of some sort of VPN solution like PPTP or OpenVPN.

ssh has an option for this, the `-D` flag. Invoking ssh with the following options

exposes the local port `9090` as a [SOCKS](https://en.wikipedia.org/wiki/SOCKS_proxy) proxy. You can then alter your browser settings to use your local SOCKS proxy to route browsing traffic. My configuration in Firefox for the previous example is show here:

![ssh SOCKS proxy settings in Firefox](http://blog.tjll.net/generated/socks_proxy-507x493-ec3013.png) ssh SOCKS proxy settings in Firefox Note that for Firefox at least, I had to set the following flag in my <http://about:config> in order for everything to work correctly:

![Firefox SOCKS dns settings](http://blog.tjll.net/generated/firefox_socks_dns-495x55-c560e8.png) Firefox SOCKS dns settings Try browsing to a site like [geoiptool](http://geoiptool.com/) and you should find that your IP is now originating from your ssh host.

This may initially seem a little useless, but when you consider that you can also use a cheap (or free) EC2 instance anywhere in the world... there's a lot of possibilities. For example, I've used this trick to access US-only services when traveling abroad and it has worked very well.

## Accessing NAT-ed Hosts Directly

Before diving into this one, I want to emphasize that if you aren't taking full advantage of your `~/.ssh/config`, you really should. Stop using `alias` and starting using shorthand hostnames in the ssh config file. Doing so allows you do use the same shortname in any ssh-friendly application (scp, rsync, vim, etc.) and lends itself well to providing a unified environment if you distribute your dotfiles across machines.

Moving on. ssh config files follow this syntax:

For example, you can reduce the command

```
$ ssh -p12345 foo@bar.baz.edu -i ~/.ssh/customkey
```

by adding the following to `~/.ssh/config`:

```
Host bar
 User foo
 Port 12345
 IdentityFile ~/.ssh/customkey
 HostName bar.baz.edu
```

Now that we've dabbled in the config, look at this configuration stanza:

```
Host behind.bar
 # ProxyCommand ssh bar exec nc behind %p
 # I've since been educated that invoking netcat for forwarding
 # is deprecated, use the -W flag instead:
 ProxyCommand ssh -q -W %h:%p bar
```

`ProxyCommand` directs ssh how to connect to `behind.bar`: connect to `bar` (previously defined) and ~~use netcat to~~ connect to the port that ssh would normally connect to. ssh's `-W` flag intelligently forwards traffic to the interpolated %h (hostname) and %p (port) variable placeholders. The following diagram demonstrates.

![ssh ProxyCommand diagram](http://blog.tjll.net/generated/ssh_proxycommand-600x301-0610a0.png) ssh ProxyCommand diagram I've you've ever tried to copy a file from a NAT-ed machine, you'll see the usefulness in this: you can essentially treat the NAT-ed host as if there were no intermediate hosts between the ssh client and daemon.

## Sharing Connections

I often find that when I'm working on a remote host, there's a pretty good chance I'll need to scp a file over or log in again over another ssh session. While you _could_ negotiate another asymmetric ssh handshake, why not use your pre-existing connection to make speedier file copies or logins?

Use `Controlpath`:

```
Host busyserver
 Controlmaster auto
 Controlpath ~/.ssh/ssh-%r@%h:%p.sock
```

This means upon first connection to `busyserver`, ssh will create a socket in `~/.ssh/` which can be shared by other commands. If you're using commands like rsync or scp, this can make repetitive copy tasks much quicker.

## Conclusion

These are just a few uses for ssh that I've come across. If anyone has other creative uses for it, I'd love to hear them.

## Edit: Bonus Round!

There's been great reception for this post both on reddit and twitter, so I couldn't help but add some of the great tips that people have been throwing at me.

Credit goes to the ssh savants commenting on this blog post and over at the [reddit thread](https://pay.reddit.com/r/linux/comments/245jt9/ssh_kung_fu/).

### Remote File Editing in Emacs

I'm a vim man, so I hadn't the slightest clue that you could leverage ssh equally well using emacs by opening a file path of the format `//user@host:/file` in order to remotely edit files.

### The Secure Shell... Shell

This one was new to me: try the key combination `~C` while in an ssh session and you'll get a prompt that looks like this:

```
ssh>
```

(I have to hit return before the `~C` key combo in zsh, but your shell's behavior may vary.)

Try entering `help` at this prompt to get a summary of the commands you can enter at the prompt. Essentially you can dynamically allocate forwarded ports from within your active ssh session. Who knew?

I've actually used the key combination `~.` many types to kill a hung ssh session but had no idea that I was using this feature of ssh.

### Agent Forwarding

I actually use this pretty heavily but failed to mention it because I got lazy.

When you negotiate pubkey authentication with ssh, you're just validating that your key gives you the rights to log in the the remote server. What if you want to get from that remote server to _another_ server?

You could use password authentication to get to the other machine (ugly), place your private on the intermediate host (not a good idea to spread your private key around), **or** you could use agent forwarding.

Agent forwarding allows you to validate against that second machine by verifying that you're the owner of the permissioned private key somewhere down the chain. I don't want to make more diagrams so I'll make some ASCII art. Here are your hostnames:

```
sol ----------------------> terra ----------------------> luna
```

Your pubkey from sol is listed in terra:~/.ssh/authorized_keys. Great! You ssh into terra:

```
sol ======================> terra ----------------------> luna
```

Now you want to get to luna. You can get there without your private key on terra by using a plain old

Nice! With any key credentials stored on terra, you've authenticated to luna using the private key stored on sol. The only requirements for this feature are:

- On the client (sol in this example)

  - Make sure you have the `ssh-agent` program running (check if it's running with `ssh-add -L` to list cached private keys)
  - Cache your key with `ssh-add`
  - Prepend the line `ForwardAgent yes` to your `~/.ssh/config`

- Each intermediate server

  - Ensure the line `AllowAgentForwarding yes` is in `/etc/ssh/sshd_config`

That's all there is. If this fails to work for you for some reason, the most common problem is that your key isn't cached in `ssh-agent` on your local machine (again, confirm it's cached with `ssh-add -L`.)

Note that this technique of caching your key in ssh-agent also alleviates the annoyance of having to unlock a password-protected private key every time it's used by caching it for an extended period (with the associated security/convenience tradeoff of keeping your private key cached in memory.)
