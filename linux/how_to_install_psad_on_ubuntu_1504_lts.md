# How to Install PSAD on Ubuntu 15.04 LTS

[Original URL](http://linoxide.com/ubuntu-how-to/install-psad-ubuntu-15-04-lts/)

> As name indicate, Port Scan Attack Detection (PSAD) tool is used to block port scanning on the server. Nmap, a well-known tool is mostly used for launching port scan to detect open/close ports. PSAD...

As name indicate, Port Scan Attack Detection (PSAD) tool is used to block port scanning on the server. Nmap, a well-known tool is mostly used for launching port scan to detect open/close ports. PSAD tool continuously monitors firewall (iptables in case of linux platform) logs to determine port scan or any other attack occurred. In case of successful attack on the server, PSAD also takes action to deter the threat.

In this article, we will install and configure PSAD on Ubuntu 15.04 VM. Currently we are running following services on VM.

1. Web server (port 80)
2. FTP server (port 21)
3. ssh server (port 22)

Netstat and telnet commands are used to check ports status against above mention services.

Web service (Apache)

[![apache](http://blog.linoxide.com/wp-content/uploads/2016/02/apache.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/apache.png)

Ftp service (vsftpd)

[![ftp](http://blog.linoxide.com/wp-content/uploads/2016/02/ftp.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/ftp.png)

Ssh service (Secure shell)

[![sshd](http://blog.linoxide.com/wp-content/uploads/2016/02/sshd.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/sshd.png)

## Installation

The detail of VM setting are given in figure 1 and the ip address is 192.168.1.111.

[![vm setting](http://blog.linoxide.com/wp-content/uploads/2016/02/vm-setting.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/vm-setting.png)

IPtables (known as linux firewall) package is pre-installed on all linux platforms. Therefore, install PSAD package on VM. It can be installed from source code or binary package. In this article, we will install PSAD package from Ubuntu repository. First of all, run package update command then install PSAD package using following command.

> $sudo apt-get update

[![apt-get update](http://blog.linoxide.com/wp-content/uploads/2016/02/apt-get-update.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/apt-get-update.png)

> $sudo apt-get install psad

[![psad instalation](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-instalation.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-instalation.png)

PSAD package required many dependencies which are resolved automatically in case of installation from Ubuntu repository. However, PSAD installation from source cod required several perl packages.

During installation of package, PSAD prompts for mail server configuration which is shown below. In this article, we have not setup any SMTP server for emails alert.

[![email](http://blog.linoxide.com/wp-content/uploads/2016/02/email.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/email.png)

## Configuration

PSAD uses firewall (iptables) logs to detect any malicious activity on the machine. Following commands will enable logging of packets on input & forward chains of iptables.

> $sudo iptables -A INPUT -j LOG

> $sudo iptables -A FORWARD -j LOG

After enabling logs for both chains, run following command to list the current configuration of iptables.

> $sudo iptables -L

Output of the above command will be similar shown below.

> ```
> Chain INPUT (policy ACCEPT)
> target     prot opt source               destination
> LOG        all  --  anywhere             anywhere             LOG level warning

> Chain FORWARD (policy ACCEPT)
> target     prot opt source               destination
> LOG        all  --  anywhere             anywhere             LOG level warning
> Chain OUTPUT (policy ACCEPT)
> target     prot opt source               destination
> ```

[![logs](http://blog.linoxide.com/wp-content/uploads/2016/02/logs.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/logs.png)

PSAD configuration file, snort rules & signatures are under **/etc/psad** directory on Ubuntu platform. PSAD configuration file is **/etc/psad/psad.conf** and it has many parameter to change while deploying on production server. However, in this article, we will change few settings of PSAD so that it detects iptables logs and takes necessary action to deter attack.

[![configuration psad](http://blog.linoxide.com/wp-content/uploads/2016/02/configuration-psad.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/configuration-psad.png)

> <span>EMAIL_ADDRESSES   root@localhost; ##change it to get psad alerts </span>

> <span>HOSTNAME          test-machine; #set host name</span>

> <span>HOME_NET          192.168.1.0/24; # Set LAN network </span>

> <span>EXTERNAL_NET      any; # Set Wan network </span>

> <span>ENABLE_SYSLOG_FILE      Y; #by default set yes</span>

One of the important configuration of PSAD is the setting **`IPT_SYSLOG_FILE`** parameter.By default,it search for logs in **/var/log/messages.** However, syslog on Ubuntu is **/var/log/syslog** therefore change path so that PSAD detects malicious activity .

> <span>IPT_SYSLOG_FILE             /var/log/syslog;</span>

We are using PSAD as IDS/IPS therefore we have to enable **ENABLE_AUTO_IDS ``** parameter in **psad.conf** file. It will automatically change the iptables rules to block scan from attacker.

> <span>ENABLE_AUTO_IDS Y;         # disable by default</span>

> <span>AUTO_IDS_DANGER_LEVEL 1;  #By default level is 5</span>

Basic setting of PSAD is complete, now update the signatures so that it can correctly recognize known attack types.

> `sudo psad --sig-update`

[![psad update](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-update.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-update.png)

Before starting PSAD on the machine, scan server using Nmap tool. Output of scanner shows following port are open on the machine.

[![nmap logs](http://blog.linoxide.com/wp-content/uploads/2016/02/nmap-logs.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/nmap-logs.png)

Now start PSAD tool using following command and also check status which is shown in following figure.

> # /etc/init.d/psad start

> #psad -S

[![psad start](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-start.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-start.png)

After enabling PSAD, no open port detected by Nmap scanner which is also shown in following figure.

[![blocked by psad](http://blog.linoxide.com/wp-content/uploads/2016/02/blocked-by-psad.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/blocked-by-psad.png)

Attacker (ip address is 192.168.1.102) is blocked by PSAD by adding drop rule in the iptables.

[![no more pin](http://blog.linoxide.com/wp-content/uploads/2016/02/no-more-pin.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/no-more-pin.png)

Iptables rule against 192.168.1.102 address is shown in following figure.

[![attacker ip blocked](http://blog.linoxide.com/wp-content/uploads/2016/02/attacker-ip-blocked.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/attacker-ip-blocked.png)

Detailed output of PSAD is shown in following figures.

[![psad-1](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-1.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-1.png)

Port scan by the attacker is shown in PSAD output.

[![psad-2](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-2.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-2.png)

Attacker IP is shown in the following output of PSAD.

## [![psad-3](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-3.png)](http://blog.linoxide.com/wp-content/uploads/2016/02/psad-3.png)

## Conclusion

In this article, we explored a well-known open source tool PSAD for blocking of port scan on servers. PSAD is complete IDS/IPS which automatically adds attacker ip address in the firewall rules. Attacker uses port scanner to get preliminary information about the server to launch further attacks therefore it is very important to block port scan automatically.

**Category**: [SECURITY](http://linoxide.com/category/security/), [UBUNTU HOWTO](http://linoxide.com/category/ubuntu-how-to/)
