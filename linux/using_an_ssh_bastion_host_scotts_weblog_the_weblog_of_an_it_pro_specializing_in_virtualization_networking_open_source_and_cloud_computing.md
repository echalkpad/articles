# Using an SSH Bastion Host - Scott's Weblog - The weblog of an IT pro specializing in virtualization, networking, open source, and cloud computing

[Original URL](http://blog.scottlowe.org/2015/11/21/using-ssh-bastion-host/)

> 21 November 2015 Secure Shell, or SSH, is something of a "Swiss Army knife" when it comes to administering and managing Linux (and other UNIX-like) workloads. In this post, I'm...

<span class="post-date">21 November 2015</span>

 Secure Shell, or SSH, is something of a "Swiss Army knife" when it comes to administering and managing Linux (and other UNIX-like) workloads. In this post, I'm going to explore a very specific use of SSH: the _SSH bastion host_. In this sort of arrangement, SSH traffic to servers that are not directly accessible via SSH is instead directed through a bastion host, which proxies the connection between the SSH client and the remote servers.

At first, it may sound like the use of an SSH bastion host is a pretty specialized use case. In reality, though, I believe this is a design pattern that can actually be useful in a variety of situations. I plan to explore the use cases for an SSH bastion host in a future blog post.

This diagram illustrates the concept of using an SSH bastion host to provide access to Linux instances running inside some sort of cloud network (like an OpenStack Neutron tenant network or an AWS VPC):

![SSH bastion host diagram](http://blog.scottlowe.org/public/img/ssh-bastion-host.png)

Let's take a closer look at the nuts and bolts of actually setting up an SSH bastion host.

First, you'll want to ensure you have public key authentication properly configured, both on the bastion host as well as the remote instances. This is a topic that has been discussed extensively in other locations ([here](https://kb.iu.edu/d/aews), for example), so I won't cover it here in any great detail. You can use the same key for both the bastion host and the remote instances, or different keys; you'll just need to ensure that the keys are loaded by `ssh-agent` appropriately so they can be used as needed. (Note that the use of public key authentication isn't strictly _required_, but it's something you really should do.)

Second, you'll want to ensure that SSH agent forwarding is enabled when you connect from the SSH client to the SSH bastion host. This can be done a couple of different ways. You can do it via the `ssh` command line, like this:

```
ssh -A user@bastion
```

Or you can do it in an SSH config file, like this:

```
Host bastion
 ForwardAgent yes
```

If you choose to do it via an SSH config file, be cautious not to use a wildcard host (keeping in mind that SSH config files allow you to do things like `Host *.cloud.domain.net`), thus forwarding unlocked SSH keys to more hosts than expected/anticipated/desired.

Third, you'll want to ensure that DNS resolution is working--both from the client to the bastion as well as from the bastion to the remote instances. The bastion host is going to use the hostname specified on the `ssh` command line, so if it can't resolve the name the connection will fail.

Finally, you'll want to configure the `ProxyCommand` setting for the remote instances in your SSH configuration file. For example, let's say that you had a remote instance named "private1" and you wanted to run SSH connections through a bastion host called "bastion". The appropriate SSH configuration might look something like this:

```
Host private1
 IdentityFile ~/.ssh/rsa_private_key
 ProxyCommand ssh user@bastion -W %h:%p

Host bastion
 IdentityFile ~/.ssh/bastion_rsa_key
 ForwardAgent yes
```

With this configuration in place, when you type `ssh user@private1` SSH will establish a connection to the bastion host and then _from the bastion host_ connect to "private1", using the specified keys at each step along the way. The user won't see any of this; he or she will just see a shell for "private1" appear. If you dig a bit further, though (try running `who` on the remote node), you'll see the connections are coming from the bastion host, not the original SSH client. Handy, eh?

In a future blog post, look for me to explore one or more examples of where using an SSH bastion host might be very useful (or even necessary).

## Additional Resources

I found [this article](https://10mi2.wordpress.com/2015/01/14/using-ssh-through-a-bastion-host-transparently/) to be extraordinarily helpful while working through the details of an SSH bastion host configuration.

<span class="post-date">Tags: <a href="http://blog.scottlowe.org/tags/#CLI">CLI</a> · <a href="http://blog.scottlowe.org/tags/#Linux">Linux</a> · <a href="http://blog.scottlowe.org/tags/#Networking">Networking</a> · <a href="http://blog.scottlowe.org/tags/#SSH">SSH</a> · <a href="http://blog.scottlowe.org/tags/#Security">Security</a></span>

 

<span class="post-date">
  <strong> Previous Post: <a href="http://blog.scottlowe.org/2015/11/17/technology-short-take-56/">Technology Short Take #56</a></strong>
</span> 

**Be social and share this post!<br>
[**](<https://www.facebook.com/sharer/sharer.php?u=http://blog.scottlowe.org/2015/11/21/using-ssh-bastion-host/> "Share on Facebook") [**](https://twitter.com/intent/tweet?url=http://blog.scottlowe.org/2015/11/21/using-ssh-bastion-host/&text=Using%20an%20SSH%20Bastion%20Host "Share on Twitter") [**](https://plus.google.com/share?url=http://blog.scottlowe.org/2015/11/21/using-ssh-bastion-host/ "Share on Google Plus")
