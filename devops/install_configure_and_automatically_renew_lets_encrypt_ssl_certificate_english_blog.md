# Install, configure and automatically renew Let's Encrypt SSL certificate (English) - Blog

[Original URL](https://vincent.composieux.fr/article/install-configure-and-automatically-renew-let-s-encrypt-ssl-certificate)

> I've recentelly passed <https://vincent.composieux.fr> over HTTPS using a Let's Encrypt free SSL certificate so this blog articles will explain you how I've done it, how I also renew the certificate...

I've recentelly passed <https://vincent.composieux.fr> over HTTPS using a [Let's Encrypt](https://letsencrypt.org) free SSL certificate so this blog articles will explain you how I've done it, how I also renew the certificate automatically every 60 days and how I've configured my Varnish and Nginx stack.

## Let's introduce Let's Encrypt

This initiative is sponsored by the biggest Internet companies and browsers: Google (Chrome), Mozilla (Firefox), Akamai, Facebook, ... so this is widely supported.

Since 3th December 2015, [Let's Encrypt has entered in public beta](https://letsencrypt.org/2015/12/03/entering-public-beta.html) so everybody can generate a free SSL certificate for its website. It's also automated and open. A true revolution. Let's start installing Let's Encrypt and create your certificate!

## Installation of Let's Encrypt and certificate creation

Firstly, clone the Let's Encrypt repository and move it to /opt:

```
$ git clone https://github.com/letsencrypt/letsencrypt
$ mv letsencrypt /opt/letsencrypt
```

We will now create a configuration file that determines how our domain certificate will be generated.

Create a file located in 

<span class="red">/usr/local/etc/le-yourdomain-webroot.ini</span>

:

```
# We use a 4096 bit RSA key instead of 2048
rsa-key-size = 4096

email = email@example.org
domains = yourdomain.tld, subdomain.yourdomain.tld

authenticator = webroot

# This is the webroot directory of your domain in which
# letsencrypt will write a hash in /.well-known/acme-challenge directory.
webroot-path = /var/www/yourdomain.tld/
```

I think this configuration file is pretty easy to understand so I will not explained it so much, you just have to specify your domains, an email address and the webroot authentication method will be used to authenticate your website.

In order to make it works properly, depending on your Nginx virtual host configuration, you should add the following lines:

```
 location '/.well-known/acme-challenge' {
 root /var/www/yourdomain.tld/;
 try_files $uri /$1;
 }
```

Now that your domain configuration file is created, you can generate your SSL certificate by using the following command:

```
$ sudo /opt/letsencrypt/letsencrypt-auto certonly --config /usr/local/etc/le-yourdomain-webroot.ini
```

If the command below has successfully ran, Let's Encrypt should have created your 

<span class="red">.pem</span>

 file into the following directory:

```
$ ls /etc/letsencrypt/live/yourdomain.tld/
cert.pem chain.pem fullchain.pem privkey.pem
```

Time has come to update our Nginx and Varnish configuration to take it into account.

## Update of Nginx and Varnish configuration

Let's start by updating your Nginx virtual host to listen on the 443 (HTTPS) port. Simply add a new server section which will make a proxy_pass call to your HTTP standard application:

```
server {
 listen 443 ssl;
 server_name yourdomain.tld;

 ssl_certificate /etc/letsencrypt/live/yourdomain.tld/fullchain.pem;
 ssl_certificate_key /etc/letsencrypt/live/yourdomain.tld/privkey.pem;

 ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
 ssl_prefer_server_ciphers on;
 ssl_ciphers AES256+EECDH:AES256+EDH:!aNULL;

 location / {
 proxy_pass http://127.0.0.1:80;

 proxy_set_header X-Real-IP $remote_addr;
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header X-Forwarded-Proto https;
 proxy_set_header X-Forwarded-Port 443;
 proxy_set_header Host $host;
 }
}
```

After restarting Nginx, you should be able to navigate on 

<span class="red">
  <a href="https://yourdomain.tld">https://yourdomain.tld</a>
</span>

 and your certificate status should be ok.

If you are using Varnish as me and want to redirect all your trafic from HTTP to HTTPS, here is the code I wrote in the Varnish (v4) configuration:

```
sub vcl_recv {
 if (req.http.X-Forwarded-Proto !~ "(?i)https") {
 return (synth(750, ""));
 }

 ...

 return (hash);
}

sub vcl_synth {
 if (resp.status == 750) {
 set resp.status = 301;
 set resp.http.Location = "https://yourdomain.tld" + req.url;

 return(deliver);
 }
}
```

So now, when browsing 

<span class="red">
  <a href="http://yourdomain.tld">http://yourdomain.tld</a>
</span>

, you will be redirected on 

<span class="red">
  <a href="https://yourdomain.tld">https://yourdomain.tld</a>
</span>

 automatically.

You're almost done! Your certificate is now ready but for a period of 90 days only. You have to set up a small automated renew script to avoid having an expired certificate in some days.

## Automated SSL certificate renewal script

In order to renew the certificate, I've made a script (I've found the base on Internet) and added it into a cron job which runs every day.

It checks the expiration date of the certificate against the current date and renew it if the certificate will expire in less than 30 days (this value can be changed).

Please note that Nginx is reloaded to take into account the renewed certificate.

Here is the script, I've placed it under 

<span class="red">/usr/local/etc/renew-certificates.sh</span>

:

```
#!/bin/bash

WEB_SERVICE='nginx'
CONFIG_FILE='/usr/local/etc/le-yourdomain-webroot.ini'
LE_PATH='/opt/letsencrypt'
EXP_LIMIT=30;

if [ ! -f $CONFIG_FILE ]; then
 echo "[ERROR] config file does not exist: $CONFIG_FILE"
 exit 1;
fi

DOMAIN=`grep "^\s*domains" $CONFIG_FILE | sed "s/^\s*domains\s*=\s*//" | sed 's/(\s*)\|,.*$//'`
CERT_FILE="/etc/letsencrypt/live/$DOMAIN/fullchain.pem"

if [ ! -f $CERT_FILE ]; then
    echo "[ERROR] certificate file not found for domain $DOMAIN."
fi

DATE_NOW=$(date -d "now" +%s)
EXP_DATE=$(date -d "`openssl x509 -in $CERT_FILE -text -noout | grep "Not After" | cut -c 25-`" +%s)
EXP_DAYS=$(echo \( $EXP_DATE - $DATE_NOW \) / 86400 |bc)

echo "Checking expiration date for $DOMAIN..."

if [ "$EXP_DAYS" -gt "$EXP_LIMIT" ] ; then
    echo "The certificate is up to date, no need for renewal ($EXP_LIMIT days left)."
    exit 0;
else
    echo "The certificate for $DOMAIN is about to expire soon. Starting webroot renewal script..."
 $LE_PATH/letsencrypt-auto certonly --renew-by-default --config $CONFIG_FILE
    echo "Reloading $WEB_SERVICE"
    /usr/sbin/service $WEB_SERVICE reload
    echo "Renewal process finished for domain $DOMAIN"
    exit 0;
fi
```

You can try it by changing the EXP_LIMIT value from 30 to 90 and you should have the following output when successful:

```
Checking expiration date for yourdomain.tld...
The certificate for yourdomain.tld is about to expire soon. Starting webroot renewal script...
Updating letsencrypt and virtual environment dependencies......
Requesting root privileges to run with virtualenv: /root/.local/share/letsencrypt/bin/letsencrypt certonly --renew-by-default --config /usr/local/etc/letsencrypt/le-yourdomain-webroot.ini

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
 /etc/letsencrypt/live/yourdomain.tld/fullchain.pem. Your cert will
 expire on 2016-03-31\. To obtain a new version of the certificate in
 the future, simply run Let's Encrypt again.
 - If you like Let's Encrypt, please consider supporting our work by:

 Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
 Donating to EFF: https://eff.org/donate-le

Reloading nginx
[ ok ] Reloading nginx configuration: nginx.
Renewal process finished for domain yourdomain.tld
```

You can now set up the cron job to run every day at 02:00am for instance:

```
0 2 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii /bin/bash /usr/local/etc/renew-certificates.sh
```

You're done! Your SSL certificate is now fully set up and automatically renewed.
