# letsencrypt/letsencrypt

[Original URL](https://github.com/letsencrypt/letsencrypt)

> This is a DEVELOPER PREVIEW intended for developers and testers only. DO NOT RUN THIS CODE ON A PRODUCTION SERVER. IT WILL INSTALL CERTIFICATES SIGNED BY A TEST CA, AND WILL CAUSE CERT WARNINGS FOR...

[]() This is a **DEVELOPER PREVIEW** intended for developers and testers only.

**DO NOT RUN THIS CODE ON A PRODUCTION SERVER. IT WILL INSTALL CERTIFICATES SIGNED BY A TEST CA, AND WILL CAUSE CERT WARNINGS FOR USERS.**

Browser-trusted certificates will be available in the coming months.

For more information regarding the status of the project, please see <https://letsencrypt.org>. Be sure to checkout the [Frequently Asked Questions (FAQ)](https://community.letsencrypt.org/t/frequently-asked-questions-faq/26#topic-title).

[]()

## [](https://github.com/letsencrypt/letsencrypt#about-the-lets-encrypt-client)About the Let's Encrypt Client

[![Travis CI status](https://camo.githubusercontent.com/6771ad0a6b2ce5f2a9fecd78319bb7fa34d62837/68747470733a2f2f7472617669732d63692e6f72672f6c657473656e63727970742f6c657473656e63727970742e7376673f6272616e63683d6d6173746572)](https://travis-ci.org/letsencrypt/letsencrypt) [![Coverage status](https://camo.githubusercontent.com/596e32dbeeb6fc20f2a9ec4de0d9ca254b70be1c/68747470733a2f2f636f766572616c6c732e696f2f7265706f732f6c657473656e63727970742f6c657473656e63727970742f62616467652e7376673f6272616e63683d6d6173746572)](https://coveralls.io/r/letsencrypt/letsencrypt) [![Documentation status](https://camo.githubusercontent.com/795fd1c8e01fcec7e5c6a917fe1f853096c19fed/68747470733a2f2f72656164746865646f63732e6f72672f70726f6a656374732f6c657473656e63727970742f62616467652f)](https://readthedocs.org/projects/letsencrypt/) [![Docker Repository on Quay.io](https://camo.githubusercontent.com/f753b02ce59b5d146a195a1ba2303a11d81a9721/68747470733a2f2f717561792e696f2f7265706f7369746f72792f6c657473656e63727970742f6c657473656e63727970742f737461747573)](https://quay.io/repository/letsencrypt/letsencrypt)

In short: getting and installing SSL/TLS certificates made easy ([watch demo video](https://www.youtube.com/watch?v=Gas_sSB-5SU)).

The Let's Encrypt Client is a tool to automatically receive and install X.509 certificates to enable TLS on servers. The client will interoperate with the Let's Encrypt CA which will be issuing browser-trusted certificates for free.

It's all automated:

- The tool will prove domain control to the CA and submit a CSR (Certificate Signing Request).
- If domain control has been proven, a certificate will get issued and the tool will automatically install it.

All you need to do to sign a single domain is:

```
user@www:~$ sudo letsencrypt -d www.example.org auth
```

For multiple domains (SAN) use:

```
user@www:~$ sudo letsencrypt -d www.example.org -d example.org auth
```

and if you have a compatible web server (Apache or Nginx), Let's Encrypt can not only get a new certificate, but also deploy it and configure your server automatically!:

```
user@www:~$ sudo letsencrypt -d www.example.org run
```

**Encrypt ALL the things!**

[]()

### [](https://github.com/letsencrypt/letsencrypt#current-features)Current Features

- web servers supported:

  - apache/2.x (tested and working on Ubuntu Linux)
  - nginx/0.8.48+ (tested and mostly working on Ubuntu Linux)
  - standalone (runs its own webserver to prove you control the domain)

- the private key is generated locally on your system
- can talk to the Let's Encrypt (demo) CA or optionally to other ACME compliant services
- can get domain-validated (DV) certificates
- can revoke certificates
- adjustable RSA key bitlength (2048 (default), 4096, ...)
- optionally can install a http->https redirect, so your site effectively runs https only (Apache only)
- fully automated
- configuration changes are logged and can be reverted using the CLI
- text and ncurses UI
- Free and Open Source Software, made with Python.

[]()

### [](https://github.com/letsencrypt/letsencrypt#installation-instructions)Installation Instructions

Official **documentation**, including [installation instructions](https://letsencrypt.readthedocs.org/en/latest/using.html), is available at <https://letsencrypt.readthedocs.org>.

[]()

### [](https://github.com/letsencrypt/letsencrypt#links)Links

Documentation: <https://letsencrypt.readthedocs.org>

Software project: <https://github.com/letsencrypt/letsencrypt>

Notes for developers: [CONTRIBUTING.md](https://github.com/letsencrypt/letsencrypt/blob/master/CONTRIBUTING.md)

Main Website: <https://letsencrypt.org/>

IRC Channel: #letsencrypt on [Freenode](https://freenode.net)

Community: <https://community.letsencrypt.org>

Mailing list: [client-dev](https://groups.google.com/a/letsencrypt.org/forum/#!forum/client-dev) (to subscribe without a Google account, send an email to [client-dev+subscribe@letsencrypt.org](mailto:client-dev+subscribe@letsencrypt.org))
