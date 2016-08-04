# Introducing Lemur

[Original URL](http://techblog.netflix.com/2015/09/introducing-lemur.html)

> by: Kevin Glisson, Jason Chan and Ben Hagen Netflix is pleased to announce the open source release of our x.509 certificate orchestration framework : Lemur! The Challenge of Certificate...

<span>by: </span>

[Kevin Glisson](https://twitter.com/kevgliss)

<span>, </span>

[Jason Chan](https://twitter.com/chanjbs) and [Ben Hagen](https://twitter.com/benhagen)<br>

<span>
  <br>
</span>

 

<span>Netflix is pleased to announce the open source release of our x.509 certificate orchestration framework : Lemur!</span>

<br>

<span>
  <br>
</span>

 

**<span>The Challenge of Certificate Management</span>**

<br>

<span>
  <a href="https://en.wikipedia.org/wiki/Public_key_infrastructure">Public Key Infrastructure</a>
</span>

<span>is a set of hardware, software, people, policies, and procedures needed to create, manage, distribute, use, store, and revoke digital certificates and manage public-key encryption. PKI allows for secure communication by establishing chains of trust between two entities. </span>

****<br>

<span>There are three main components to PKI that we are attempting to address:</span>

1. #### ![](https://lh5.googleusercontent.com/s2myiuviAbtXDkX8pb4k-ZNNr4QGIbkBQSpQwxE6JOLfjLNKCJiU4BButs_6HKM2nQw1x4h5CFN37BuR3T1xyDKMv5gJJ76j_NhC7uNU04pM57nIPDLj_MHMfd1FdtEFkK4eUMA)

  <span>Public Certificate</span>

  <span> - A cryptographic document that proves the ownership of a public key, which </span>

  <span>can be used for signing, proving identity or encrypting data.</span>

2. <span>Private Key</span>

  <span> - A cryptographic document that is used to decrypt data encrypted by a public key. </span>

3. <span>Certificate Authorities (CAs)</span>

  <span> - Third-party or internal services that validate those they do business with. They provide confirmation that a client is talking to the server it thinks it is. Their public certificates are loaded into major operating systems and provide a basis of trust for others to build on. </span>

****<br>

<span>The management of all the pieces needed for PKI can be a confusing and painful experience. Certificates have expiration dates - if they are allowed to expire without replacing communication can be interrupted, impacting a system’s availability. And, private keys must never be exposed to any untrusted entities - any loss of a private key can impact the confidentiality of communications. There is also increased complexity when creating certificates that support a diverse pool of browsers and devices. It is non-trivial to track which devices and browsers trust which certificate authorities.</span>

****<br>

<span>On top of the management of these sensitive and important pieces of information, the tools used to create manage and interact with PKI have confusing or ambiguous options. This lack of usability can lead to mistakes and undermine the security of PKI. </span>

****<br>

<span>For non-experts the experience of creating certificates can be an intimidating one. </span>

****

## <span>Empowering the Developer</span>

<span>At Netflix developers are responsible for their entire application environment, and we are moving to an environment that requires the use of HTTPS for all web applications. This means developers often have to go through the process of certificate procurement and deployment for their services. Let’s take a look at what a typical procurement process might look like: </span>

<span>Here we see an example workflow that a developer might take when creating a new service that has TLS enabled.</span>

****<br>
[![](https://lh6.googleusercontent.com/MX5e5ySuiiaFrhwFy0J2b2ZcCyS-2QYVY02iBCC1JNDT9OjXx_bI85dOCzLFdJDL6dpPcib1AUTVmjNSKl6OZkyUB3IOtV8wMRN_p-6kqjkxTL5TVFuTcHWh8i0dgbb8PbEdg7Y)](https://lh6.googleusercontent.com/MX5e5ySuiiaFrhwFy0J2b2ZcCyS-2QYVY02iBCC1JNDT9OjXx_bI85dOCzLFdJDL6dpPcib1AUTVmjNSKl6OZkyUB3IOtV8wMRN_p-6kqjkxTL5TVFuTcHWh8i0dgbb8PbEdg7Y)

<span>There are quite a few steps to this process and much of it is typically handled by humans. Let’s enumerate them:</span>

1. <span>Create Certificate Signing Request (CSR)</span>

  <span> - A CSR is a cryptographically signed request that has information such as State/Province, Location, Organization Name and other details about the entity requesting the certificate and what the certificate is for. Creating a CSR typically requires the developer to use OpenSSL commands to generate a private key and enter the correct information. The OpenSSL command line contains hundreds of options and significant flexibility. This flexibility can often intimidate developers or cause them to make mistakes that undermine the security of the certificate. </span>

****

1. <span>Submit CSR</span>

  <span> - The developer then submits the CSR to a CA. Where to submit the CSR can be confusing. Most organizations have internal and external CAs. Internal CAs are used for inter-service or inter-node communication anywhere you have control of both sides of transmission and can thus control who to trust. External CAs are typically used when you don’t have control of both sides of a transmission. Think about your browser communicating with a banking website over HTTPS. It relies on the trust built by third parties (Symantec/Digicert, GeoTrust etc.) in order to ensure that we are talking to who we think we are. External CAs are used for the vast majority of Internet-facing websites. </span>

****

1. <span>Approve CSR</span>

  <span> - Due to the sensitive and error-prone nature of the certificate request process, the choice is often made to inject an approval process into the workflow. In this case, a security engineer would review that a request is valid and correct before issuing the certificate.</span>

****

1. <span>Deploy Certificate</span>

  <span> - Eventually the issued certificate needs to be placed on a server that will handle the request. It’s now up to the developer to ensure that the keys and server certificates are correctly placed and configured on the server and that the keys are kept in a safe location.</span>

****

1. <span>Store Secrets</span>

  <span> - An optional, but important step is to ensure that secrets can be retrieved at a later date. If a server ever needs to be re-deployed these keys will be needed in order to re-use the issued certificate.</span>

****<br>

<span>Each of these steps have the developer moving through various systems and interfaces, potentially copying and pasting sensitive key material from one system to another. This kind of information spread can lead to situations where a developer might not correctly clean up the private keys they have generated or accidently expose the information, which could put their whole service at risk. Ideally a developer would never have to handle key material at all. </span>

****

## <span>Toward Better Certificate Management</span>

<span>Certificate management is not a new challenge, tools like </span>

[<span>EJBCA</span>](http://www.ejbca.org/)

<span>, </span>

[<span>OpenCA</span>](http://www.openca.org/)

<span>, and more recently </span>

[<span>Let’s Encrypt</span>](https://letsencrypt.org/)

<span> are all helping to make certificate management easier. When setting out to make certificate management better we had two main goals: First, increase the usability and convenience of procuring a certificate in such a way that would not be intimidating to users. Second, harden the procurement process by generating high strength keys and handling them with care. </span>

****<br>

<span>Meet Lemur! </span>

****

## <span>Lemur</span>

<span>Lemur is a certificate management framework that acts as a broker between certificate authorities and internal deployment and management tools. This allows us to build in defaults and templates for the most common use cases, reduce the need for a developer to be exposed to sensitive key material, and provides a centralized location from which to manage and monitor all aspects of the certificate lifecycle. </span>

****<br>

<span>We will use the following terminology throughout the rest of the discussion:</span>

****

- <span>Issuers </span>

  <span>are internal or third-party certificate authorities</span>

- <span>Destinations </span>

  <span>are deployment targets, for TLS these would be the servers terminating web requests.</span>

- <span>Sources</span>

  <span> are any certificate store, these can include third party sources such as AWS, GAE, even source code.</span>

- <span>Notifications</span>

  <span> are ways for a subscriber to be notified about a change with their certificate.</span>

****<br>

<span>Unlike many of our tools Lemur is not tightly bound to AWS, in fact Lemur provides several different integration points that allows it to fit into just about any existing environment. </span>

****<br>

<span>Security engineers can leverage Lemur to act as a broker between deployment systems and certificate authorities. It provides a unified view of, and tracks all certificates in an environment regardless of where they were issued.  </span>

****<br>

<span>Let’s take a look at what a developer’s new workflow would look like using Lemur:</span>

[![](http://1.bp.blogspot.com/-ddSboGM8cbI/VgBSAucAoQI/AAAAAAAABQE/PZJdwxtbOrU/s1600/new.png)](http://1.bp.blogspot.com/-ddSboGM8cbI/VgBSAucAoQI/AAAAAAAABQE/PZJdwxtbOrU/s1600/new.png)

****<br>

<span>Some key benefits of the new workflow are:</span>

- <span>Developer no longer needs to know OpenSSL commands</span>

- <span>Developer no longer needs to know how to safely handle sensitive key material</span>

- <span>Certificate is immediately deployed and usable</span>

- <span>Keys are generated with known strength properties</span>

- <span>Centralized tracking and notification</span>

- <span>Common API for internal users</span>

****<br>

<span>
  <img src="https://lh5.googleusercontent.com/QpkVQLBsDtYiaPxuYpXsdIA9KKxAlnV2su-i-PECVgAIGmRGHwwGDCAJkCN6hYzI3mD5Ovs7f6KJvweZECJzLVaO9GQDqv0DhwreYreBRkW2GmeukLtXwod2bstmMsuX=s1600">
</span>

<span>This interface is much more forgiving than that of a command line and allows for helpful suggestions and input validation. </span>

<span>
  <img src="https://lh5.googleusercontent.com/NukE5buAszywR9GzZgRgLCrrwJPZCNQR_MGjPP_3ARALj4vsFBIn-PNute-BwKNBkCQjOeD-NgTt6TTQt37mSXd4mac9McrCsVVskH8nvdh33RWH22uEAak-bc7l8TIOyP0pwxU">
</span>

<span>For advanced users, Lemur supports all certificate options that the target issuer supports. </span>

****<br>

<span>Lemur’s destination plugins allow for a developer to pick an environment to upload a certificate. Having Lemur handle the propagation of sensitive material keeps it off developer’s laptops and ensures secure transmission. Out of the box Lemur supports multi-account AWS deployments. Over time, we hope that others can use the common plugin interface to fit their specific needs.</span>

****<br>

<span>Even with all the things that Lemur does for us we knew there would use cases where certificates are not issued through Lemur. For example, a third party hosting and maintaining a marketing site, or a payment provider generating certificates for secure communication with their service. </span>

****<br>

<span>To help with these use cases and provide the best possible visibility into an organization’s certificate deployment, Lemur has the concept of source plugins and the ability to import certificates. Source plugins allow Lemur to reach out into different environments and discover and catalog existing certificates, making them an easy way to bootstrap Lemur’s certificate management within an organization. </span>

<br>

<span>
  <br>
</span>

<span>Lemur creates, discovers and deploys certificates. It also securely stores the sensitive key material created during the procurement process. Letting Lemur handle key management provides a centralized and known method of encryption and the ability to audit the key’s usage and access. </span>

****

## <span>Architecture</span>

<span>Lemur makes use of the following components :</span>

- <span>Python 2.7, 3.4 with Flask API (including a number of helper packages)</span>

- <span>AngularJS UI</span>

- <span>Postgres</span>

- <span>Optional use of AWS Simple Email Service (SES) for email notifications</span>

<span>We’re shipping Lemur with built-in plugins for that allow you to issue certificates from Verisign/Symantec and allow for the discovery and deployment of certificates into AWS.</span>

****

## <span>Getting Started</span>

****

## <span>Interested in Contributing?</span>

<span>Feel free to reach out or submit pull requests if you have any suggestions. We’re looking forward to seeing what new plugins you create to to make Lemur your own! We hope you’ll find Lemur as useful as we do!</span>

****

## <span>Conclusion</span>

<span>Lemur is helping the Netflix security team manage our PKI infrastructure by empowering developers and creating a paved road to SSL/TLS enabled applications. Lemur is available on our</span>

[<span>GitHub</span>](http://netflix.github.io/#repo)

<span> site now!</span>
