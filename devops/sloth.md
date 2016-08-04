# SLOTH

[Original URL](http://www.mitls.org/pages/attacks/SLOTH)

> Security Losses from Obsolete and Truncated Transcript Hashes (CVE-2015-7575) TL;DR If your TLS 1.2 client or server supports RSA-MD5 signatures, then client authentication is broken, and server...

[]()

## Security Losses from Obsolete and Truncated Transcript Hashes

(CVE-2015-7575)

### TL;DR

If your TLS 1.2 client or server supports RSA-MD5 signatures, then client authentication is broken, and server authentication may be breakable by a powerful adversary. (Note the signature algorithm is not related to the chosen ciphersuite; you may use `ECDHE_RSA_AES_128_GCM_SHA256` and still be signing with RSA-MD5.) If your TLS application relies on the `tls-unique` channel binding to prevent credential forwarding, you need to redesign your application. If practical collision attacks on SHA1 appear, then many constructions in TLS, IKE, and SSH will be breakable. So, if you can afford to do so, get rid of MD5 and SHA1 in all your protocol configurations.

## Introduction

In response to recent high-profile attacks that exploit hash function collisions, software vendors have started to phase out the use of MD5 and SHA1 in third-party digital signature applications such as X.509 certificates. However, weak hash functions continue to be used in various cryptographic constructions within mainstream protocols such as TLS, IKE, and SSH, because practitioners argue that their use in these protocols relies only on second preimage resistance, and hence is unaffected by collisions. We systematically investigate and debunk this argument.

We identify a new class of _transcript collision_ attacks on popular cryptographic protocols such as TLS, IKE, and SSH, that significantly reduce their expected security. Our attacks rely on the use of obsolete hash constructions in these protocols. The full details of our attacks are in the [technical paper](http://www.mitls.org/downloads/transcript-collisions.pdf). This page summarizes our results and describes two concrete attacks on TLS: one on [TLS 1.2 client authentication](http://www.mitls.org/pages/attacks/SLOTH#tls-client-auth), and the [other on the `tls-unique` channel binding](http://www.mitls.org/pages/attacks/SLOTH#tls-channel-bindings) used in various TLS-based authentication protocols like [Token Binding](https://datatracker.ietf.org/wg/tokbind/charter/), [FIDO](https://fidoalliance.org/specs/fido-uaf-v1.0-ps-20141208/fido-uaf-protocol-v1.0-ps-20141208.html), and [SCRAM](https://tools.ietf.org/html/rfc5802).

Our main conclusion is that the continued use of MD5 and SHA1 in mainstream cryptographic protocols significantly reduces their security and, in some cases, leads to practical attacks on key protocol mechanisms. Furthermore, the use of truncated hashes and MACs for authenticating key exchange protocol transcripts is dangerous and should be avoided where possible.

Partly as a consequence of this work, the TLS working group has decided to remove RSA-MD5 signatures and truncated handshake hashes from TLS 1.3\. We encourage TLS 1.2 implementations to disable MD5 signatures immediately and SHA1 signatures as soon as practical. We also advocate that `tls-unique` should no longer be used for channel binding in application-layer authentication protocols.

### Why SLOTH?

SLOTH is an acronym for the loss of security due to the use of obsolete and truncated hash constructions in mainstream Internet protocols. SLOTH is also a not-so-subtle reference to laziness in the protocol design community with regard to removing legacy cryptographic constructions. For example, MD5 signatures have been known to be cryptographically broken since at least 2005, but they continue to be used in TLS today, when collision attacks have become significantly more practical, even on standard desktop workstations. Furthermore, SLOTH is part of a series of recent attacks on the use of legacy crypto constructions including [POODLE](https://www.openssl.org/~bodo/ssl-poodle.pdf), [FREAK](http://www.mitls.org/pages/attacks/SMACK) and [Logjam](http://weakdh.org). We hope that these attacks will encourage the protocol community to proactively remove known-weak constructions, rather than waiting for concrete attacks to make it necessary.

## SLOTH in TLS, IKE, and SSH

The following table summarizes the security losses (SLOTH) due to transcript collision attacks on well-known cryptographic protocols. In each protocol, we identify a protocol mechanism and a rough estimate of its expected security. For example, if a TLS server uses a 3072-bit RSA key for signing and supports SHA-256 as a hash algorithm, the expected security of server signatures is 128 bits. Even if the server supports MD5 signatures, since the 2nd preimage attack complexity of MD5 signatures is still 2

<sup>128</sup>

, one may expect 128-bit security for server signatures. However, in our paper, we describe a transcript collision attack on TLS server signatures that takes 2

<sup>64</sup>

 connections, 2

<sup>64</sup>

 storage, and 2

<sup>64</sup>

 hashes per connection. Hence, the effective security is halved to about 64 bits. The security losses for other mechanisms such as TLS client authentication are even more dramatic, leading to practical attacks on real-world clients and servers.

Protocol | Property               | Mechanism                | Attack                | Collision Type | Precomputation                         | Work/connection     | Wall-clock Time   | Preimage Cost     | Security Loss
-------- | ---------------------- | ------------------------ | --------------------- | -------------- | -------------------------------------- | ------------------- | ----------------- | ----------------- | -------------
TLS 1.2  | Client Auth            | RSA-MD5                  | Impersonation         | Chosen Prefix  |                                        | 2

<sup>39</sup>    | 1 hour (48 cores) | 2

<sup>128</sup> | 89 bits
TLS 1.2  | Channel Binding        | Truncated HMAC (96 bits) | Credential Forwarding | Generic        |                                        | 2

<sup>48</sup>    | 20 days (4 GPUs)  | 2

<sup>96</sup>  | 48 bits
TLS 1.2  | Client Auth            | RSA-SHA1 or ECDSA-SHA1   | Impersonation         | Chosen Prefix  |                                        | 2

<sup>77</sup>    |                   | 2

<sup>160</sup> | 83 bits
TLS 1.2  | Server Auth            | RSA-MD5                  | Impersonation         | Generic        | 2

<sup>X</sup>

 connections +storage | 2

<sup>128-X</sup> |                   | 2

<sup>128</sup> | X bits
TLS 1.3  | Server Auth            | RSA-SHA1 or ECDSA-SHA1   | Impersonation         | Chosen Prefix  |                                        | 2

<sup>77</sup>    |                   | 2

<sup>160</sup> | 83 bits
TLS 1.1  | Handshake Integrity    | MD5 | SHA1               | Downgrade             | Chosen Prefix  |                                        | 2

<sup>77</sup>    |                   | 2

<sup>160</sup> | 83 bits
IKEv1    | Initiator Auth         | HMAC-MD5                 | Impersonation         | Generic        |                                        | 2

<sup>64</sup>    |                   | 2

<sup>128</sup> | 64 bits
IKEv2    | Initiator Auth         | RSA-SHA1 or DSA-SHA1     | Impersonation         | Chosen Prefix  | 2

<sup>77</sup>                       |                     |                   | 2

<sup>160</sup> | 93 bits
SSH 2    | Key Exchange Integrity | SHA1                     | Downgrade             | Chosen Prefix  |                                        | 2

<sup>77</sup>    |                   | 2

<sup>160</sup> | 83 bits

[]()

## An attack on TLS 1.2 Client Authentication

In TLS, the client authenticates itself by presenting an X.509 certificate and then signing a hash of the entire handshake transcript (up to that point) with the private key corresponding to the certificate. In TLS versions up to 1.1, the hash algorithm used before signing was a concatenation of MD5 and SHA1\. However, TLS 1.2 allowed clients and servers to negotiate the signature and hash algorithms they support. This enabled the use of newer, stronger hash algorithms such as SHA-256 and SHA-512, but unfortunately it also enabled the use of weaker hash algorithms such as MD5.

Indeed, we find that the TLS libraries in Java (SunJSSE) and on Akamai Servers support RSA-MD5 signatures for both client and server authentication. Even implementations that do not advertise support for RSA-MD5, such as NSS (before version 3.21), BouncyCastle (Java before version 1.54, C# before version 1.8,1), PolarSSL/mbedTLS (before 2.2.1), GnuTLS (before version 3.3.15), and OpenSSL (before version 1.0.1f) surprisingly accept RSA-MD5 signatures.

### Man-in-the-Middle Client Impersonation Attack

Suppose a TLS client C and server S both support RSA-MD5 signatures for client authentication, and suppose that the client is willing to use the same certificate to authenticate at some malicious server M. The figure below shows how M can mount a man-in-the-middle transcript collision attack that allows it to impersonate C at S.

![](http://www.mitls.org/i/tls-client-auth-mitm.png) To accomplish this attack, the attacker M must compute a chosen-prefix MD5 collision between two handshake transcripts, one between C and M, and the other between M and S. The attack complexity depends on the difficulty of finding such collisions. For MD5, such collisions are known to require computing 2

<sup>39</sup>

 hashes, which can be accomplished in[several hours on Amazon EC2 instances](http://natmchugh.blogspot.com/2015/02/create-your-own-md5-collisions.html).

### Proof of Concept Demo

We implemented our man-in-the-middle attack between a standard Java TLS client and a Java TLS server. We captured the handshake traces and modified them as shown in the figure above. We used the publicly available [HashClash](https://marc-stevens.nl/p/hashclash/) software (with some optimizations we implemented) to compute the MD5 collision needed to complete the attack. After our optimizations, computing the collision took only 1 hour on a workstation with 48 cores. We expect that this time can be significantly reduced using a full GPU implementation of HashClash, or custom MD5 hardware.

We present an example pair of traces for which we found transcript collisions. Both traces consist of a sequence of messages from the ClientHello (CH) to the ClientKeyExchange (CKE); these are the traces whose hashes will be signed by the client to authenticate itself.

- C-M handshake trace between Java client and MitM server: [Binary file](http://www.mitls.org/pages/attacks/client-auth-trace-1.bin)
- M-S handshake trace between MitM server and Java server: [Binary file](http://www.mitls.org/pages/attacks/client-auth-trace-2.bin)

You can read the traces by translating them to hexadecimal with xxd or hexdump. Both traces have the same MD5 hash `3fe6c0ead2fd516560e895613ddf08ed`.

[]()

## An attack on TLS Channel Bindings

Application-layer user authentication protocols such as SASL, EAP, and GSSAPI are vulnerable to generic credential forwarding attacks, where an authentication credential sent by a client C to a server M may then be used by M to impersonate C at another server S. (Note that our attack on TLS client authentication has the same flavour.) To prevent such credential forwarding attacks, modern authentication protocols rely on _channel bindings_. For example, protocols like [Token Binding](https://datatracker.ietf.org/wg/tokbind/charter/) and [SCRAM](https://tools.ietf.org/html/rfc5802) (used in [XMPP](https://tools.ietf.org/html/rfc6120)) use the `tls-unique` channel identifier provided by the TLS layer to strongly bind authentication credentials to the underlying channel, so that a credential received on one TLS channel cannot be forwarded on another.

The `tls-unique` channel binding is defined as the first Finished message sent in a TLS handshake. We observe that the TLS Finished message in all versions up to TLS 1.2 is computed as the HMAC, using a session secret, of the hash of the full handshake transcript. Importantly, this HMAC is truncated to 96 bits, even in TLS 1.2 where the transcript hash and the HMAC both use SHA-256\. This use of a truncated hash enables a transcript collision attack as described below.

### Man-in-the-Middle Credential Forwarding Attack

Suppose a client C and a server S use the `tls-unique` channel binding to bind a user authentication credential to the TLS channel. Further assume that the client is willing to use the same authentication credential with a malicious server M. M will then connect to S and try to forwards C's credential to S. The figure below shows how M can synchronize the `tls-unique` on both connections and successfully carry out this credential forwarding attack.

![](http://www.mitls.org/i/tls-unique-mitm.png) To accomplish this attack the attacker M needs to collide two ClientFinished messages, one between C-M and the other between M-S. Two key observations make this attack possible. First, the attacker knows the session master secret on both connections and hence can compute the MAC. Second, the attacker controls large parts of the transcripts in both directions and hence can try many values to obtain the collision. Since the HMAC is only 96 bits long, even a generic collision requires only about 2

<sup>48</sup>

 HMAC computations.

### Proof of Concept Demo

We implemented our man-in-the-middle attack between a Google Chrome client and the Google.com TLS server. We captured the handshake traces and modified them as shown in the figure above. We implemented a TLS 1.1 Finished collision search algorithm for Nvidia GPUs (using the CUDA framework). Computing the collision took about 80 GPU days (20 days on a workstation with with 4 Nvidia Tesla GPUs). This work can easily be parallelized across more GPUs to arbitrarily reduce the computation time, and dedicated hardware would bring a significant speedup.

We present an example pair of traces for which we found transcript collisions. The trace between C-M consists of a sequence of messages from the ClientHello (CH) to the ClientKeyExchange (CKE). The trace between M-S also includes a NextProtocolNegotiation message.

- C-M handshake trace between Java client and MitM server: [Binary file.](http://www.mitls.org/pages/attacks/tls-unique-trace-1.bin)<br>
  Session master secret (C-M):<br>
  `7D255A759CC0C3D5364DB99310918088C011A15CAD786 21D22E6CC91A004B1A81060AA6AC0C1E1FC489069ABBCF3B561`
- M-S handshake trace between MitM server and Java server: [Binary file.](http://www.mitls.org/pages/attacks/tls-unique-trace-2.bin)<br>
  Session master secret (M-S):<br>
  `549C5FC20945453B5ACB7BAFBC274654626986350DF90 A282584C44C9CC294A5ED2CD08E021A88B5765DEEEBD70ABAE8`

You can read the traces by translating them to hexadecimal with xxd or hexdump. Both traces have the ClientFinished value `43dd758aa2195bbc82139c2d` This value is computed as the HMAC, using the corresponding master secret, over the concatenated MD5 and SHA1 hashes of the traces.

## Impact on TLS 1.2 and 1.3 Server Authentication

TLS 1.2 enables RSA-MD5 signatures for both client and server signatures. Indeed, about [30% of HTTPS servers are currently willing to send RSA-MD5 server signatures.](https://securitypitfalls.wordpress.com/2015/12/07/november-2015-scan-results/) However, the server signature in TLS 1.2 is not over the full handshake transcript; it covers only the client and server nonces and the server's Diffie-Hellman key share. This means that, compared to client authentication, a man-in-the-middle attacker is much more limited when trying to exploit a server signature collision.

To mount a transcript collision attack on TLS 1.2 server authentication, an attacker first has to collect a large number, say 2

<sup>X</sup>

, of RSA-MD5 signatures signed by the server. The attacker may do this by passively observing RSA-MD5 connections to the server, but since such connections may be rare, it may have to actively connect to the server to obtain a sufficient number of signatures. Once these signatures (and their hashed contents) have been collected and stored, the attacker can hijack any connection to the server by choosing a server random such that the hash of the attacker's key exchange message collides with one of the server signatures already collected. Finding this collision requires the attacker to compute up to 2

<sup>128-X</sup>

 MD5 hashes and then look them up in the stored signature database.

The complexity of the attack on TLS 1.2 server authentication is therefore 2

<sup>128-X</sup>

 MD5 hashes per connection, in addition to 2

<sup>X</sup>

 connections peformed before-hand, and 2

<sup>X</sup>

 storage. The attacker can trade-off between these costs--the more signatures he can collect, the less he has to compute per connection. For example, if it is feasible to collect 2

<sup>64</sup>

 signatures, then the per-connection cost is 2

<sup>64</sup>

 hashes.

### Removing MD5 from TLS 1.3

In TLS 1.3, the server signature has been modified to cover the full handshake transcript. Consequently, the transcript collision attack becomes as practical as the attack on TLS 1.2 client authentication. That is, if a TLS 1.3 client and server were to support RSA-MD5 signatures, then a server impersonation attack can be mounted with about 2

<sup>39</sup>

 MD5 computations per connection. Considering that server authentication is one of the main goals of TLS, this attack would have been devastating. Fortunately, in response to comments from TLS practitioners and researchers, and partly as a consequence of our work, all MD5 signatures have been disabled from TLS 1.3\. However, in light of previous bugs in TLS libraries that allowed legacy crypto even after it was thought to be disabled, we warn that TLS implementations must take special care to ensure that MD5 signatures are not enabled in TLS 1.3 connections. Furthermore, we warn that the use of SHA1-based signatures in TLS 1.3 also reduces its security significantly, and should be quickly deprecated.

[]()

## Affected Software and Responsible Disclosure

Our attack on MD5-based signatures affects all TLS clients and servers that support RSA-MD5 and ECDSA-MD5 (DSA is typically enabled only with SHA1). This protocol-level flaw in TLS 1.2 is covered by [CVE-2015-7575](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-7575). Here is the list of software known to be affected. This list will evolve as we gather more information.

- OpenSSL clients and servers up to version 1.0.1e advertise, send, and accept RSA-MD5 signatures. Consequently applications that use this version are vulnerable, including those that rely on the default system OpenSSL on Red Hat Enterprise Linux 6 and 7, Debian Wheezy, Android 4.4.2 and 5.0.0, and Akamai GHost servers.<br>
  (Fixed in OpenSSL 1.0.1f. Fixed on all Akamai servers on 17th Dec 2015\. [Fixed in RHEL 6 and 7 on 6th January 2016 via updates to the OpenSSL, NSS, and GnuTLS packages.](https://bugzilla.redhat.com/show_bug.cgi?id=1289841))
- NSS clients (but not servers) up to version 3.20 accept RSA-MD5 server signatures even though clients do not offer RSA-MD5 in the ClientHello. This affects all versions of Firefox up to 42.<br>
  ([Fixed in NSS version 3.21, Firefox 43](https://bugzilla.mozilla.org/show_bug.cgi?id=1158489).)
- Oracle Java clients and servers up to version 8u66 (and 7u79) advertise, send, and accept RSA-MD5 client and server signatures.<br>
  (Fixed in main codeline on 23rd December 2015, scheduled for a future CPU)
- GnuTLS clients and servers up to version 3.3.14 accept RSA-MD5 signatures even if the client or server disables them (using the priority string, for example).<br>
  ([Fixed in 3.3.15 - GNUTLS-SA-2015-2](http://www.gnutls.org/security.html))
- BouncyCastle Java clients up to version 1.53 (C# clients up to 1.8.0) accept RSA-MD5 server signatures even if the client did not offer RSA-MD5 int he ClientHello. BouncyCastle servers up to the same versions may offer and accept RSA-MD5 signatures depending upon application configuration.<br>
  (Fixed in Java version 1.54 and C# version 1.8.1)
- PolarSSL/mbedTLS up to 2.2.0 offers and accepts RSA-MD5 and ECDSA-MD5 signatures for server (but not client) authentication in its default configuration.<br>
  (Fixed in mbedTLS 2.2.1, 2.1.4, 1.3.16.)
- Other TLS libraries are being tested. If you know of any TLS library that supports RSA-MD5, please let us know at the contacts below.
- Internet scans by Hubert Kario show that [32% of TLS servers support RSA-MD5 signatures](https://securitypitfalls.wordpress.com/2015/12/07/november-2015-scan-results/) as of November 2015.

Our attack on the `tls-unique` channel binding affects application-level protocols that rely on this channel binding to prevent credential forwarding attacks. In general, all uses of `tls-unique` are suspect, but the following are known to be specifically affected:

- [SCRAM](https://tools.ietf.org/html/rfc5802) is used in SASL and GSSAPI and relies on `tls-unique` for channel binding. SCRAM is the default authentication protocol for [XMPP](https://tools.ietf.org/html/rfc6120).
- [Token Binding](https://datatracker.ietf.org/wg/tokbind/charter/) is designed to protect HTTP cookies and OAuth tokens. It used to rely on `tls-unique` in [draft-01](https://tools.ietf.org/html/draft-ietf-tokbind-protocol-01) but in response to our findings, it now relies on a different mechanism since [draft-02](https://tools.ietf.org/html/draft-ietf-tokbind-protocol-02).
- [FIDO](https://fidoalliance.org/specs/fido-uaf-v1.0-ps-20141208/fido-uaf-protocol-v1.0-ps-20141208.html) supports `tls-unique` for channel bindings.
- If you know of other protocols that rely on `tls-unique` please inform us on the contacts below.

We are a pair of researchers from [INRIA](http://inria.fr), the French national research institute for computer science. Our research is part of a wider effort on investigating the security of TLS and its implementations, called [miTLS](http://mitls.org). You can contact us at our email addresses: [FirstName].[LastName]@inria.fr (use our names without any accents.)
