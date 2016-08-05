# How to secure a mail server using encryption

[Original URL](http://xmodulo.com/secure-mail-server-using-encryption.html)

> SSL (Secure Sockets Layer) and its descendant TLS (Transport Layer Security) are the most widely used protocols for encrypting data that is exchanged between a server and a client. These protocols...

SSL (Secure Sockets Layer) and its descendant TLS (Transport Layer Security) are the most widely used protocols for encrypting data that is exchanged between a server and a client. These protocols often use X.509 certificates and asymmetric cryptography.

STARTTTLS is another method of securing plain-text communication. This protocol also encrypts data with SSL or TLS, but with the same port as the plain-text protocols, instead of using separate ports for SSL/TLS-encrypted communications. For example, IMAP over STARTTLS uses the same port as IMAP (143), while IMAPS (IMAP over SSL) uses a separate port 993.

[The previous tutorial](http://xmodulo.com/2014/01/mail-server-ubuntu-debian.html) describes how to set up a mail server running on Postfix and Dovecot, but the [security](http://xmodulo.com/recommend/firewallbook) aspect was not covered. In this tutorial, we demonstrate **how to add security to a mail server through TLS/SSL-based encryption**.

Certificates needed for TLS/SSL can be self-signed, signed by a free certification authority (e.g., CAcert) or signed by a commercial authority (e.g., VeriSign), and can be generated with utilities like [OpenSSL](http://security.ncsa.illinois.edu/research/grid-howtos/usefulopenssl.html). We are going to use a self-signed certificate in this tutorial.

## Enable TLS Encryption for Postfix

A self-signed certificate can be created with the following command.

# openssl req -new -x509 -days 365 -nodes -out /etc/ssl/certs/postfixcert.pem -keyout /etc/ssl/private/postfixkey.pem

The above command requests a new certificate which is of type X.509, and remains valid for 365 days. The optional `-nodes` parameter specifies that the private key should not be encrypted. An output certificate file is saved as `postfixcert.pem`, and an output key file as `postfixkey.pem`.

All necessary values for the certificate can be given:

```
Country Name (2 letter code) [AU]:BD 
State or Province Name (full name) [Some-State]:Dhaka 
Locality Name (eg, city) []:Dhaka 
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:Example.tst 
Common Name (e.g. server FQDN or YOUR name) []:mail.example.tst 
Email Address []:sarmed@example.tst 
```

Now that the certificate is ready, necessary parameters are adjusted in `postfix` configuration file.

root@mail:~# vim /etc/postfix/main.cf

```
### STARTTLS is enabled ###
smtpd_tls_security_level = may 

smtpd_tls_received_header = yes 
smtpd_tls_auth_only = yes 

### loglevel 3 should be used while troubleshooting ###
smtpd_tls_loglevel = 1

### path to certificate and key file
smtpd_tls_cert_file = /etc/ssl/certs/postfixcert.pem 
smtpd_tls_key_file = /etc/ssl/private/postfixkey.pem 
smtpd_use_tls=yes 
```

Restart `postfix` to enable TLS.

root@mail:~# service postfix restart

At this point, `postfix` is ready to encrypt data to and from the server. More details about Postfix TLS support can be found in their [official README](http://www.postfix.org/TLS_README.html).

## Enable SSL Encryption for Dovecot

Configuring `dovecot` for encryption is similar to `postfix`.

First of all, a self-signed certificate is created with `openssl`:

# openssl req -new -x509 -days 365 -nodes -out /etc/ssl/certs/dovecotcert.pem -keyout /etc/ssl/private/dovecotkey.pem

The above command requests a new X.509 certificate which is valid for 365 days. `-nodes` is an optional parameter which specifies that the stored private key should not be encrypted. An output certificate file will be `dovecotcert.pem`, and an output key file will be `dovecotkey.pem`.

All necessary parameters need to be specified in the certificate:

```
Country Name (2 letter code) [AU]:BD
State or Province Name (full name) [Some-State]:Dhaka
Locality Name (eg, city) []:Dhaka
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:Example.tst
Common Name (e.g. server FQDN or YOUR name) []:mail.example.tst
Email Address []:sarmed@example.tst
```

Next, the path to the certificate is added in `dovecot` configuration.

root@mail:~# vim /etc/dovecot/conf.d/10-ssl.conf

```
ssl_cert = </etc/ssl/certs/dovecotcert.pem
ssl_key = </etc/ssl/private/dovecotkey.pem
```

Finally, `dovecot` is restarted to enable SSL with the new certificate.

root@mail:~# service dovecot restart

## Thunderbird Mail Client Configuration

The following is a snapshot on how to configure the account in Mozilla Thunderbird.

[![](http://farm6.staticflickr.com/5477/12039520633_2d5ab27274_z.jpg)](http://www.flickr.com/photos/xmodulo/12039520633/)

## Troubleshooting

First of all, make sure that all necessary ports are allowed in the firewall.

Second, try `telnet` to a mail server. You should be able to get through. Some examples are given below for reference.

### Connect to IMAPS

$ telnet mail.example.tst 993

```
Trying mail.example.tst... 
Connected to mail.example.tst. 
Escape character is '^]'. 
exit 
exit 
Connection closed by foreign host. 
```

### Connect to POP3S

$ telnet mail.example.tst 995

```
Trying mail.example.tst... 
Connected to mail.example.tst. 
Escape character is '^]'. 
exit 
exit 
Connection closed by foreign host. 
```

### Connect to SMTP

$ telnet mail.example.tst 25

```
Trying mail.example.tst... 
Connected to mail.example.tst. 
Escape character is '^]'. 
220 mail.example.tst ESMTP Postfix (Ubuntu) 

### Command ###
ehlo mail.example.tst 
250-mail.example.tst 
250-PIPELINING 
250-SIZE 10240000 
250-VRFY 
250-ETRN 
250-STARTTLS 
250-ENHANCEDSTATUSCODES 
250-8BITMIME 
250 DSN
```

### ![](http://xmodulo.com/images/rss_small.png) Subscribe to Xmodulo

Do you want to receive **Linux FAQs, detailed tutorials and tips** published at Xmodulo? Enter your email address below, and we will deliver our Linux posts straight to your email box, for free. Delivery powered by Google Feedburner.
