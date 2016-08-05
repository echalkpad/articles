# How to access ssh terminal in web browser on Linux

[Original URL](http://xmodulo.com/access-ssh-terminal-web-browser-linux.html)

> Running "everything" in a web browser used to be a bold statement. Due to the powerful HTML5/JavaScript stack, however, a web browser increasingly becomes a dominant application delivery platform....

Running "everything" in a web browser used to be a bold statement. Due to the powerful HTML5/JavaScript stack, however, a web browser increasingly becomes a dominant application delivery platform. Even [the Linux kernel sandboxed in a web browser](http://bellard.org/jslinux/tech.html) no longer sounds so crazy these days.

In this tutorial, I describe **how to access an SSH terminal in a web browser on Linux**. **Web-based SSH** is useful when the firewall you are behind is so restrictive that only HTTP(s) traffic can get through.

[Shell In A Box](http://list.xmodulo.com/shellinabox.html) (or `shellinabox`) is a web-based terminal emulator which can run as a web-based SSH client. It comes with its own web server (`shellinaboxd`) which exports a command line shell to a web-based terminal emulator via AJAX interface. Shell In a Box only needs JavaScript/CSS support from a web browser, and does not require any additional browser plugin.

## Install Shell In A Box on Linux

To install `shellinabox` on Debian, Ubuntu or Linux Mint:

$ sudo apt-get install openssl shellinabox

To install `shellinabox` on Fedora:

$ sudo yum install openssl shellinabox

To install `shellinabox` on CentOS or RHEL, first [enable EPEL repository](http://xmodulo.com/how-to-set-up-epel-repository-on-centos.html), and then run:

$ sudo yum install openssl shellinabox

## Configure Shellinaboxd Web Server

By default `shellinaboxd` web server listens on 4200 TCP port on localhost. In this tutorial, I change the default port to 443 for HTTPS. For that, modify `shellinabox` configuration as follows.

Configure `shellinaboxd` On Debian, Ubuntu or Linux Mint:

$ sudo vi /etc/default/shellinabox

```
# TCP port that shellinboxd's webserver listens on
SHELLINABOX_PORT=443

# specify the IP address of a destination SSH server
SHELLINABOX_ARGS="--o-beep -s /:SSH:192.168.1.7"

# if you want to restrict access to shellinaboxd from localhost only
SHELLINABOX_ARGS="--o-beep -s /:SSH:192.168.1.7 --localhost-only"
```

Configure `shellinaboxd` On Fedora, CentOS or RHEL:

$ sudo vi /etc/sysconfig/shellinaboxd

```
# TCP port that shellinboxd's webserver listens on
PORT=443

# specify the IP address of a destination SSH server
OPTS="-s /:SSH:192.168.1.7"

# if you want to restrict access to shellinaboxd from localhost only
OPTS="-s /:SSH:192.168.1.7 --localhost-only"
```

Heads-up for Fedora users: According to the [official document](http://code.google.com/p/shellinabox/source/browse/README.Fedora), some operations may not work out of the box when you run `shellinaboxd` in SELinux mode on Fedora. Refer to the document if you have any issue.

## Provision a Self-Signed Certificate

During the installation of Shell In A Box, `shellinaboxd` attempts to create a new self-signed certificate (certificate.pem) by using `/usr/bin/openssl` if no suitable certificate is found on your Linux. The created certificate is then placed in /var/lib/shellinabox.

If no certificate is found in the directory for some reason, you can create one yourself as follows.

$ su (change to the root)<br>
# cd /var/lib/shellinabox<br>
# openssl genrsa -des3 -out server.key 1024<br>
# openssl req -new -key server.key -out server.csr<br>
# cp server.key server.key.org<br>
# openssl rsa -in server.key.org -out server.key<br>
# openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt<br>
# cat server.crt server.key > certificate.pem

## Run Shellinaboxd Web Server

On Debian, Ubuntu or Linux Mint:

$ sudo service shellinabox start

On Fedora, CentOS or RHEL:

$ sudo systemctl enable shellinaboxd.service<br>
$ sudo systemctl start shellinaboxd.service

To verify if `shellinaboxd` is running:

$ sudo netstat -nap | grep shellinabox

```
tcp 0 0 0.0.0.0:443 0.0.0.0:* LISTEN 4787/shellinaboxd
```

Now open up your web browser, and navigate to [https://<IP\_address\_of\_SSH\_server>](https://<IP\_address\_of\_SSH\_server&gt);. You should be able to see a web-based SSH console, and log in to the remote SSH server via web browser interface.

[![](http://farm3.staticflickr.com/2869/9960158224_6392b74041_z.jpg)](http://www.flickr.com/photos/xmodulo/9960158224/)

<span>Download this article as ad-free PDF (made possible by <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&amp;hosted_button_id=PBHS9R4MB9RX4">your kind donation</a>): </span>

 [![Download PDF](http://d2j4ekq3nza4ef.cloudfront.net/wp-content/plugins/wp-post-to-pdf-enhanced/asset/images/pdf.png)](http://xmodulo.com/access-ssh-terminal-web-browser-linux.html?format=pdf "Download PDF")

### ![](http://xmodulo.com/images/rss_small.png) Subscribe to Xmodulo

Do you want to receive **Linux FAQs, detailed tutorials and tips** published at Xmodulo? Enter your email address below, and we will deliver our Linux posts straight to your email box, for free. Delivery powered by Google Feedburner.

### ![](http://xmodulo.com/images/heart_small.jpg) Support Xmodulo

Did you find this tutorial helpful? Then please be generous and [support Xmodulo!](http://xmodulo.com/about)
