# Safe Connection

[Original URL](http://www.admin-magazine.com/Articles/SSH-on-Windows)

> Thomas Drilling This article presents advanced Secure Shell (SSH) functionality and SSH-based tools for use on Windows. It does not question the fact that both Linux (vsftpd, proftpd) and Windows...

<span class="author">Thomas Drilling</span>

This article presents advanced Secure Shell (SSH) functionality and SSH-based tools for use on Windows. It does not question the fact that both Linux (_vsftpd_ , _proftpd_ ) and Windows (IIS) let you operate a secure FTP server with standard tools and that there are countless secure and powerful FTP clients. The charm of SSH consists above all in being able to use remote logins, file transfers, port forwarding, VPN, and other functions ad hoc without extensive preparatory measures. For SSH, you only need to run the SSH daemon, _sshd_ , on the (Linux) server.

SSH offers maximum security thanks to these three properties:

- Encryption of transferred data, including login information.
- Protection against manipulation of the transferred data (data integrity).
- Authentication of the communication partner.

SSH always uses transmission encryption; it can use the default host key to ensure the integrity of the connection or the hosts, and it supports peer authentication. Although SSH "only" works by default with usernames and passwords, it also optionally supports asymmetric DSA and RSA key pairs.

**SSH Basics**

The free Linux SSH Version, OpenSSH, is a spin-off of the classic SSH from 1999\. The popular Windows SSH client PuTTY, developed by Simon Tatham, is also a free SSH implementation. It is subject to the MIT license and is currently at version 0.62 from 2011\. All versions of SSH are largely compatible.

The term Secure Shell is the umbrella for a bunch of remote access tools. The entire SSH suite contains the following tools:

- _ssh_ – Replaces _rsh_ , _rlogin_ , and _telnet_ .
- _scp_ – Replaces _rcp_ .
- _sftp_ – Replaces _ftp_ .
- _sshd_ – The SSH server daemon.
- _ssh-keygen_ – Generates asymmetric public keys for authentication or manages or converts keys.
- _ssh keyscan_ – Searches for existing SSH public keys.
- _ssh-agent_ – Keeps the private key in memory until the user logs out.
- _ssh-add_ – Loads a private key into _ssh-agent_ .

The [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) Suite offers the same variety of tools. To begin, admins must download the complete zip archive with all the PuTTY SSH tools and not just run the _.exe_ installer, which only downloads and installs the PuTTY SSH client. However, the respective tools have slightly different names in PuTTY, such as _puttygen_ or _psftp_ . In addition to the popular PuTTY, Windows admins have options for using SSH on Windows, such as the Cygwin Windows X server-based [MobaXterm](http://www.admin-magazine.com/Articles/MobaXterm-Unix-for-Windows) or direct use of the [Cygwin](http://www.cygwin.com/) API compatibility layer.

**Enabling SSH**

Setting up the SSH server on the Linux side – the SSH client generally is installed by default – is not the subject of this article, but you can do this by installing the _openssh-server_ (RHEL, Fedora) or _openssh_ packages (the SUSE package contains the server and client) with the preferred package management tool.

To enable the service, you need to start the _sshd_ daemon and possibly add it permanently to the system boot configuration. You can do this, depending on your choice of distribution, with _/etc/init.d/sshd start_ (SysV), _service sshd start_ (Upstart), or _systemctl start sshd.service_ .

Additionally, the service – if so desired – must be permanently added to the system boot configuration. For this step, you could use systemd (openSUSE, Fedora) by typing _systemctl enable sshd.service_ or _chkconfig sshd on_ (RHEL/SysVinit). On Fedora and Red Hat, you need to stop the firewall, which runs by default, by typing _systemctl stop iptables.service_ , or create a rule for port 22 (SSH).

Administrators typically do not need to make any adjustments to the main configuration file – usually _/etc/ssh/sshd_config_ – with one exception. The exception is that many OpenSSH implementations prevent root access by default; uncommenting the following line:

```
PermitRootLogin yes
```

lets you enable it.

**Dealing with Host Keys**

For Linux admins, dealing with the SSH host key is part of everyday life. Because a successful SSH connection requires a user account on the remote host, all SSH tools perform an authentication process. On Linux, typing

```
ssh -l <username> <remote IP address>
```

is sufficient to open a connection to the remote server, which then asks for the password to match the specified username and transfers the usernames and passwords in encrypted format. The process is the same for PuTTY, but you might not immediately notice, because the login takes place behind the graphical interface (Figure 1).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f01/79838-1-eng-US/sshWin-F01.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f01/79838-1-eng-US/sshWin-F01_reference.jpg "Figure 1: The login process with PuTTY.") 

<span class="attribute-caption"> Figure 1: The login process with PuTTY. </span>

For PuTTY, you only need to enter the desired target host in the _Host Name_ field of the default _Session_ tab.

**Host Keys**

SSH works with host keys by default to ensure integrity. For this reason, any SSH client will show its automatically transferred host key the first time it opens a connection (or the fingerprint belonging to the key); it then uses _permanently add 'IP address' (RSA) to the list of known hosts_ to indicate that it did not know this key, and thus wants to add the key to its local _$HOME/.ssh/known_hosts_ file. You must then decide whether to trust this server, that is, determine whether it is the server it claims to be. After all, an attacker could theoretically have hijacked an Internet connection.

Integrity could be ensured, for example, by retrieving the fingerprint of the remote server, which you could then compare with the key sent to you. The process is no different when using PuTTY as the SSH client, but PuTTY shows the dialog with the RSA fingerprint graphically (Figure 2) and offers to save the host key in the Windows registry format. At the same time PuTTY, keeps the host key in its cache until the next connection with the same server is opened.

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f02/79842-1-eng-US/sshWin-F02.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f02/79842-1-eng-US/sshWin-F02_reference.jpg "Figure 2: Like any SSH client, PuTTY shows the RSA host key of the remote host.") 

<span class="attribute-caption"> Figure 2: Like any SSH client, PuTTY shows the RSA host key of the remote host. </span>

The PuTTY developers provide a script (_kh2reg_ ) on the project site with which the admin can convert existing host keys from the classical (Linux) known hosts format to a Windows _\_.reg _file, if required. On the server side, the host key is stored in the_ /etc/ssh _directory and is named_ ssh_host_dsa_key* by default. Once connected, PuTTY also shows a classic login window for the server, and the admin can log in with a username and password. The server host key's fingerprint can be found by entering

```
ssh-keygen -f /etc/ssh/ssh_host_rsa_key.pub -l
```

in the SSH remote shell.

**Key Issues**

Windows admins and users can use secure public key authentication with PuTTY. The password prompt, which is enabled by default for SSH, is just one of the ways you can handle authentication with SSH. The function is no different from that on Linux, but PuTTY uses different tools and configuration files. Just to remind you: On Linux, the admin uses _ssh-keygen -t rsa_ (optionally, _ssh-keygen -t dsa_ ) to generate a new RSA or DSA key pair of public and private keys (Figure 3).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f03/79846-1-eng-US/sshWin-F03_reference.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f03/79846-1-eng-US/sshWin-F03_reference.jpg "Figure 3: Generating a RSA key pair on Linux.") 

<span class="attribute-caption"> Figure 3: Generating a RSA key pair on Linux. </span>

SSH saves the key files under the name _id_rsa/id_dsa_ and _id_rsa.pub_ in the _.ssh_ subdirectory in the user's home directory. In contrast, the public key is appended to the user's _$HOME/.ssh/authorized_keys_ file. In the opposite direction, you need to copy your own public key to the appropriate user directory on the server, as follows:

```
ssh-copy-id -i ~/.ssh/id_rsa.pub <remote username> @ <Remote IP Address>
```

PuTTY uses the _puttygen.exe_ (PuTTY Key Generator) tool (instead of _ssh-keygen_ ) to generate RSA (default) or DSA key pairs (Figure 4).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f04/79850-1-eng-US/sshWin-F04.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f04/79850-1-eng-US/sshWin-F04_reference.jpg "Figure 4: An RSA key pair in PuTTY.") 

<span class="attribute-caption"> Figure 4: An RSA key pair in PuTTY. </span>

Although you will find this tool on the PuTTY download page, make sure you have previously downloaded the full install zip file; _puttygen_ should also be available below _Putty_ in the Windows Start menu.

To generate an RSA key pair, press the _Generate_ button. Admins who need SSH1 RSA or DSA keys can select the appropriate type directly below the dialog. You can ensure the necessary randomness yourself here, by moving the mouse back and forth in the free area of the dialog box while generating the key. Then, you enter the desired key passphrase for _Key passphrase_ , as with _ssh-keygen_ in Linux.

You can use the appropriate buttons for saving the public and private keys on the client, and you can freely choose the name and path in the file browser. The typical PuTTY key extension is _\_.ppk* , which indicates the deviation from the standard Linux format. PuTTY RSA keys are therefore not compatible with OpenSSH keys. However, you can use Conversions on the menubar to import existing OpenSSH keys or export PuTTY keys.

To transfer your own public key to the server, you can theoretically use WinSCP or _psftp_ (PuTTY SFTP), but it is easier and faster to select the entire key shown in the key field and press Ctrl+C to copy it to the clipboard. Then, you can log in again normally on the remote server using PuTTY, launch any Linux editor (e.g., nano) against the _/$HOME/.ssh/authorized_keys_ file, and paste your key from the clipboard.

**Public Key Authentication with PuTTY**

If you want to change to public key authentication the next time you log in, enter the address of the desired host on the client-side PuTTY _Session_ page, then – under the Category menu – click on _SSH/Auth_ and enter the file name of your authentication key below _Private key file for authentication_ . You can press _Browse_ to select the file in the file browser (Figure 5).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f05/79854-1-eng-US/sshWin-F05_reference.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f05/79854-1-eng-US/sshWin-F05_reference.jpg "Figure 5: PuTTY keys have slightly different names.") 

<span class="attribute-caption"> Figure 5: PuTTY keys have slightly different names. </span>

Optionally, you can store the credentials for each session in a profile. The authentication sequence is identical on Linux and Unix. When you connect to the remote server, it asks for proof that the user has one of the public keys stored in the _authorized_keys_ file for this user on the server. SSH thus prompts you for the passphrase for enabling the private key stored in the local _id_rsa.ppk_ file. The connection will work only if the two keys match.

**SSH Agent**

The public-key authentication shown here may be more secure, but admins have not gained much if they need to enter a passphrase now, instead of the original password. To automate this process, Linux comes with the _ssh-agent_ tool, in which you can store your private key. The SSH agent autonomously takes care of the authentication process, with the advantage that you only need to enter your passphrase once. The agent retains the key in memory until the user logs out. SSH-Agent is automatically started in the background on most Linux distributions.

Linux has the _ssh-add_ command for interacting with _ssh-agent_ ; the _-l_ option lists the currently stored keys. With the [Windows MobaXterm SSH client](http://mobaxterm.mobatek.net/), you can easily enable the SSH agent, for example, in the program settings (Figure 6). The PuTTY Suite has the _pagent_ (putty agent) tool for this.

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f06/79858-1-eng-US/sshWin-F06.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f06/79858-1-eng-US/sshWin-F06_reference.jpg "Figure 6: The SSH agent makes life easier.") 

<span class="attribute-caption"> Figure 6: The SSH agent makes life easier. </span>

The MobaXterm SSH client is an interesting alternative to the popular PuTTY. Because MobaXterm is based on the Cygwin compatibility layer, the tool thinks it is running in a real Linux environment on the client side. This approach means that use of the Linux tools _ssh-keygen_ and _ssh-copy-id_ is possible, including the typical Linux configuration files in the _.ssh_ subdirectory.

A pleasant side effect is that the key files are compatible with standard OpenSSH. You only have to know that the relative home directory of the Linux environment corresponds to _C:\Users\User\Documents\MobaXterm_ _\home_ . Windows physical drives can be found below _/drives_ in the MobaXterm environment.

**WinSCP**

SSH is useful not only for secure remote logins but also for secure file transfers. This task will also be the most common use for an SSH client for Windows. Although PuTTY is the most popular Windows SSH client, it is not very convenient in terms of file transfer. For example, PuTTY's Secure FTP client _psftp_ is a classic CLI client; in principle, it behaves no differently from a Linux CLI client. In MobaXterm, however, you can enable a graphical SFTP browser in the settings, and it automatically pops up in the sidebar when a SSH connection is established (Figure 7).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f07/79862-1-eng-US/sshWin-F07.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f07/79862-1-eng-US/sshWin-F07_reference.jpg "Figure 7: MobaXterm has a graphical SFTP browser.") 

<span class="attribute-caption"> Figure 7: MobaXterm has a graphical SFTP browser. </span>

Appropriate Windows alternatives such as [WinSCP](http://winscp.net/eng/index.php) also are available for the classic SSH tools _sftp_ and _scp_ , as cryptographically secure alternatives to _ftp_ and _rcp_ . The current version 5.1.5 of the GPL tool has just been released, and it is available on the [project website](http://winscp.net/eng/download.php). Installing the executable file is trivial. However, administrators should choose the custom installation, because then the _pagent_ and _puttygen_ tools and the new drag-and-drop shell extension are also conveniently installed.

WinSCP also offers direct downloads via drag and drop and supports the standard SSH protocols _sftp_ and _scp_ via SSH1 and SSH2\. Additionally, it has a GUI interface as well as a command-line interface and can process [batch scripts](http://winscp.net/eng/docs/scripting). WinSCP can also optionally handle authentication via the SSH password or public key method, and it supports [Kerberos (GSS)](http://winscp.net/eng/docs/ui_login_authentication) identification.

To open the connection, enter the desired host in the _Session_ section of the _Host name_ field, select the desired _Protocol_ , and enter the user credentials and password. Assuming that you ran _puttygen_ previously to create a public/private key pair and copied the public key to the server, you can optionally specify a password instead of the file containing the private RSA key.

**Trust Me**

Because _scp_ and _sftp_ are based on SSH, WinSCP, like PuTTY, points out the unknown host key the first time you connect to a new server. If you trust the target host, you are taken to a Windows Explorer-like interface; its use is self-explanatory.

Copying files to the destination host, or in the opposite direction, is an easy process using either by drag and drop, function keys, or Norton Commander-like menus (Figure 8).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f08/79866-1-eng-US/sshWin-F08_reference.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f08/79866-1-eng-US/sshWin-F08_reference.jpg "Figure 8: Copying with WinSCP.") 

<span class="attribute-caption"> Figure 8: Copying with WinSCP. </span>

In the confirmation dialog, admins can change the transmission options or run a transfer in the background for large amounts of data. Additionally, you can also use the integrated editor to edit text files directly on the server.

**Swish**

The most elegant method of accessing remote files via SSH on Linux is undoubtedly the use of _[sshfs](http://fuse.sourceforge.net/sshfs.html)_ . Sshfs is a [FUSE (Filesystem in Userspace)](http://fuse.sourceforge.net/) module that lets admins bind remote Linux machines to the local filesystem of the Linux client via SSH. The use of _sshfs_ does not question the usefulness and functionality of Samba, but using Samba involves some installation and configuration overhead on the server and the client, whereas _sshfs_ can be leveraged with little effort.

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-winssh/79878-1-eng-US/sshWin-WinSSH_issue_medium.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-winssh/79878-1-eng-US/sshWin-WinSSH_reference.jpg "sshWin-WinSSH")

On the server, you need only a SFTP program, which is ensured by OpenSSH. To bind remote filesystems via SSH, you also need the small GPL'd SFTP tool [Swish](http://www.swish-sftp.org/) on Windows. This open source tool is available from [SourceForge](http://sourceforge.net/projects/swish/files/swish/swish-0.7.3/swish-0.7.3.exe/download), and the current version is 0.74 (see also the "WinSSH" box). Installing the executable binary needs no explanation; no launcher is needed because Swish simply appears as a new network drive called Swish.

When you click, you first see an empty drive with two buttons, _Add SFTP Connection_ and _Launch key agent_ , at the top. Clicking on the former displays a dialog where you can specify the connection settings. You again need to enter the destination host, the desired username, and the directory path that is a relative root for the new connection. Also, the connection still needs a name under which to _Create_ the configuration as a profile or network drive.

Additionally, with Swish, you may need to confirm the RSA key for the unknown host the first time you connect to it (Figure 9).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f09/79870-1-eng-US/sshWin-F09_reference.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f09/79870-1-eng-US/sshWin-F09_reference.jpg "Figure 9: As a SSH front end, Swish also wants to add an RSA host key to its key management.") 

<span class="attribute-caption"> Figure 9: As a SSH front end, Swish also wants to add an RSA host key to its key management. </span>

If you trust the fingerprint displayed, you can press _I trust this key_ , enter your password and, depending on the chosen path, start work directly in the filesystem of the remote host as a normal network drive, that is, use drag and drop to copy data, or use _Copy_ and _Send to_ in the context menu (Figure 10).

[![](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f10/79874-1-eng-US/sshWin-F10.jpg)](http://www.admin-magazine.com/var/ezflow_site/storage/images/media/images/sshwin-f10/79874-1-eng-US/sshWin-F10_reference.jpg "Figure 10: Copying with Swish is achieved by drag and drop or with the context menu (Copy or Send to).") 

<span class="attribute-caption"> Figure 10: Copying with Swish is achieved by drag and drop or with the context menu (Copy or Send to). </span>

Key management is the front end for pagent; it displays the cached key or helps admins add new keys.

**Port Forwarding**

SSH can also secure any other protocol, such as FTP, with its port forwarding function. SSH port forwarding redirects the specified ports via a secure SSH connection, and SSH itself takes the role of a proxy. It accepts connections on one side of the tunnel and connects them at the other end with the specified server as the connection endpoint.

SSH tunnel has two operating modes: Local port forwarding and remote port forwarding (outbound/inbound tunnel), where typically local port forwarding is used. The parameters _-L_ or _-R_ indicate the direction. In local port forwarding, SSH routes an inbound port on a local client through a secure SSH channel to a port on the remote SSH server. The SSH syntax looks like this on a Linux client, for example:

```
ssh root@www.thomas-drilling.de -L 555:www.thomas-drilling.de:21
```

The command redirects the insecure FTP connection via a secure SSH connection to the server _www.thomas-drilling.de_ with a default port of 21\. If an SSH server is running on the server and the client, the _ws1-fed_ client opens a secure SSH connection. The local SSH server then listens to all requests received on the local port 555 of _ws1-fed_ , and forwards them to port 21 on the remote host, _www.thomas-drilling.de_ .

Any further communication then takes place exclusively via the previously established SSH connection. The admin can now enter

```
ftp localhost 555
```

on the local host _ws1-fed_ to start the FTP client in a second terminal session (basically opening a session to itself) to access the FTP server automatically at the other end. The difference now is that the channel is secure. The best thing about using the Cygwin-based tool, MobaXterm, is that you can point and click in the _Tools | MobaSSHTunnel_ menu to put together the whole port forwarding configuration graphically.

Although FTP is not the best example, given the SFTP alternatives shown, this approach works with any other port or service as well. Regardless of whether the particular service is regarded to be inherently secure or insecure, admins will always benefit from the encrypted connection in SSH port forwarding.

**Conclusions**

In 14 years of its existence, SSH has not lost any of its usefulness, particularly for managing hosts or user interfaces. The word has spread, and Windows administrators can now turn to many graphical tools, such as PuTTY and MobaXterm, as clients.

**Info**

**[1]** WinSSH: [[http://www.ssh.com](http://www.ssh.com/)]<br>
**[2]** Win-SSHD: [<http://www.bitvise.com/winsshd.html%5D><br>
**[3]** WinSSH download: [<http://www.wm.edu/offices/it/services/software/licensedsoftware/webeditingsftp/sshsecureshell/index.php>]