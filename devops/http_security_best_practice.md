# HTTP Security Best Practice

[Original URL](https://httpsecurityreport.com/best_practice.html)

> SSL/TLS Transport Layer Security (TLS) and its predecessor Secure Sockets Layer (SSL), facilitates confidential communications on the web by providing end-to-end encryption between browsers and web...

[]()

## SSL/TLS

Transport Layer Security (TLS) and its predecessor Secure Sockets Layer (SSL), facilitates confidential communications on the web by providing end-to-end encryption between browsers and web servers. Without TLS, other security measures are fragile or ineffective. TLS is the cornerstone of HTTP security.

TLS implementation is not straight-forward. Protocol versions and cipher choice need to be carefully chosen and configured. X509 certificates by valid authorities need provisioning and cryptographic keys have to be securely managed. The excellent [Qualys SSL Labs](https://www.ssllabs.com/ssltest/) tool helps with fine tuning implementation details.

### Recommendation

Properly configure and use TLS for _all_ local and linked resources.

[]()[]()

## HTTP Strict Transport Security (HSTS)

Instructs the browser to connect exclusively to the target server using HTTPS (SSL/TLS). This defends against a range of potential man-in-the-middle attacks, including SSL stripping, session cookie theft (if not [properly protected](https://httpsecurityreport.com/best_practice.html#cookies)). It also prevents the browser from connecting to a website if any certificate-related errors are encountered. HSTS is activated when a browser visits a HTTPS website which sets the corresponding HTTP header.

HSTS has a set duration, controlled by the _max-age_ field value. This value can either be static or relative to a specific date in the future, perhaps to coincide with the expiration of a SSL certificate.

HSTS preference can be hard-coded in browsers by submitting domains to [Chromium's HSTS preload list](https://hstspreload.appspot.com/) which all browsers implementing HSTS use.

Note that HSTS does have it's pitfalls. It has a provision to include subdomains, which may prove too broad in practise. Also, client errors can have grave consequences - an incorrect clock on the client causing it to consider the server's SSL certificate as not yet valid or expired, or a missing root CA certificate - will no longer cause a certificate error in the browser. The browser will completely refuse to access the page, and probably display an error totally incomprehensible to everyone except security professionals.

### Recommendation

Set the HSTS header with a long lifetime, preferably half a year or longer.

`Strict-Transport-Security: max-age=31536000`

### Further reading

[nVisium: Is Your Site HSTS Enabled?](http://blog.nvisium.com/2014/04/is-your-site-hsts-enabled.html) []()

## Public Key Pins

HTTP Public-Key Pinning (HPKP) instructs browsers to only connect to the host if it can present a known SSL/TLS public key - or a certificate signed by a known key. In other words, browsers won't connect to hosts if the SSL/TLS key has changed in an unexpected way. This mainly guards against fraudulent certificates issued by trusted certificate authorities (CAs) or rogue CA certificates which a user might have been tricked to install.

As an example, a browser connects to <https://example.com> which presents this header. The header tells the browser to only connect again in the future if the certificate key matches, or if a key in the issuing certificate chain matches. Other combinations of instructions are possible. They all greatly reduce the possibilities for attackers to impersonate the host or intercept communications between the client and the legitimate host.

Like [HSTS](https://httpsecurityreport.com/best_practice.html), HPKP requires careful thought and planning before implementing. Mistakes can lock users out of your site, with no easy way to fix.

### Recommendation

Determine if public-key pinning is needed for your site. If so, start with a short lifetime and increase it if no problems are encountered after a while. Establish a backup plan if the SSL/TLS key needs renewing. Preferebly create a backup key and store offline.<br>
Example HTTP header:

`Public-Key-Pins: max-age=5184000; pin-sha256="+oZq/vo3Kcv0CQPjpdwyInqVXmLiobmUJ3FaDpD/U6c="; pin-sha256="47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU="`

### Further reading

[MDN Public Key Pinning.](https://developer.mozilla.org/en-US/docs/Web/Security/Public_Key_Pinning) Includes example server configurations.<br>
[HTTP Public-Key-Pinning Explained.](https://timtaubert.de/blog/2014/10/http-public-key-pinning-explained/) [Everything you Need to Know about HTTP Public Key Pinning.](http://blog.rlove.org/2015/01/public-key-pinning-hpkp.html)<br>
[Report-URI as-a-service.](https://report-uri.io/) Excellent tool which takes care of collecting CSP and HPKP violations. []()

## Mixed HTTPS and HTTP Content

The main website is served securely over HTTPS but loads some files (images, js, css) over HTTP. This is a huge security hole and undermines the security provided by HTTPS. Affected sites could be leaking session cookies or user behavior information. They could also be vulnerable to injection and other MITM attacks, which HTTPS normally prevents.

### Recommendation

If HTTPS is deployed for the main website, use it everywhere and for all content.

### Further reading

[Mixed Content - Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Security/MixedContent)

[]()

## Content Security Policy

Provides browsers with clear instructions on type and behavior of content on the website. A good content security policy (CSP) hardens security and can help defend against attacks like cross-site-scripting (XSS) and other injection attacks. [CSP is supported by all major browsers](http://caniuse.com/#feat=contentsecuritypolicy), although only partially in IE 11 and earlier.

A good CSP is based on a white-listing approach, disallowing everything except explicitly allowed content. It also restricts javascript origin and allowed operations.

CSP can be hard to enable for legacy codebases. To ease implementation, CSP provides a "report-only" mode where CSP violations in browsers are sent to a website endpoint, but the policy is not otherwise enforced.<br>
New projects should use CSP from the start.

### Recommendation

Start with a restrictive policy and relax where necessary and appropriate. Example disallowing everything:

`Content-Security-Policy: default-src 'none';` Now let's allow self hosted scripts, images, CSS, fonts and AJAX, along with Google CDN hosted scripts and Google Analytics:

`Content-Security-Policy: default-src 'none'; script-src 'self' https://ajax.googleapis.com https://www.google-analytics.com; img-src 'self' https://www.google-analytics.com; connect-src 'self'; font-src 'self'; style-src 'self';` Be warned that disallowing everything might break your site, e.g. if you use the `child-src` directive and a browser doesn't support it. A less restrictive policy might start with the following:

`Content-Security-Policy: default-src 'self';` A even less restrictive policy could even use `default-src '*'` and then add restrictions. I recommend against this, **unless** you understand the implications fully. Otherwise you might be relying on a CSP which is only providing you with a false sense of security.

### Further reading

[CSP at Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/Security/CSP)<br>
[Report-URI as-a-service.](https://report-uri.io/) Excellent tool which takes care of collecting CSP and HPKP violations. []()

## Frame Options

Controls whether the site can be placed in an `<iframe>`, `<frame>` or `<object>` tag. Disallowing framing can prevent clickjacking attacks, amongst others. For example, [Internet Explorer's universal cross-site-scripting bug](http://thehackernews.com/2015/02/internet-explorer-xss.html) from February 2015 was mitigated by this header.

The _X-Frame-Options_ is a non-standard header, superseded by the _frame ancestor_ directive in Content Security Policy level 2\. However, _frame ancestors_ is not universally supported yet, while _X-Frame-Options_ is widely supported.

### Recommendation

Determine if your website needs to be allow being rendered in a frame. Disallow entirely by setting the option to _deny_ or allow same-origin framing by _sameorigin_. Avoid the _allow-from_ option because of limited or buggy browser support. Example HTTP header:

`X-Frame-Options: deny`

### Further reading

[X-Frame-Options at Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/HTTP/X-Frame-Options)<br>
[OWASP Clickjacking Cheat Sheet](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet) []()

## XSS Protection

Cross-site scripting (XSS or CSS) protection is built into most popular browsers, with the notable exception of Firefox. This protection is user configurable and can be turned off. Therefore, it's a good idea to explicitly request for the browser to employ its XSS filter on your website.

Conversely, websites can request for the XSS protection to be disabled on a page-by-page basis. This is definitely not a good idea.

### Recommendation

Use the following HTTP header:

`X-Xss-Protection: 1; block`

### Further reading

[Guidelines for setting security headers](https://www.veracode.com/blog/2014/03/guidelines-for-setting-security-headers) []()

## Cache Control

Indicates the preferences for caching the page output. The appropriate value varies with the nature of the website data, but including a preference is strongly recommended. Otherwise, it's up to the browsers and proxies to choose whether to cache the content. An inappropriate choice can cause performance problems, security issues, or both.

### Recommendation

Develop a caching strategy and then include the cache preference as a HTTP header.

`Cache-Control: public*` 

<sup>*</sup>

One of _public_, _private_, _no-cache_ or _no-store_. If caching is to be allowed, a _max-age_ value should be included for **Cache-Control** as well as a **Etag** header to allow for cache validation by clients.

### Further reading

[HTTP Caching Guide from Google Developers](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching) []()

## Content Type Options

MIME sniffing is when browsers treat files from server in a different way than the server instructs. This can be dangerous when a website is hosting untrusted (e.g. user-provided) content. Lets say the server allows image uploads from users. Should a user upload a HTML document instead, a browser might render it as a web execute the scriptpage even though the server clearly says it is sending an image. The non-standard header `X-Content-Type-Options` instructs browsers not do any MIME sniffing away from the specified type.

### Recommendation

Always set the header:

`X-Content-Type-Options: nosniff`

### Further reading

[IE8 Security Part VI (see section on MIME-Handling)](http://blogs.msdn.com/b/ie/archive/2008/09/02/ie8-security-part-vi-beta-2-update.aspx) []()

## Subresource Integrity

Browsers typically load a host of resources, javascripts and stylesheets, from external domains. Content delivery networks are often used. Should the external resource be compromised, the security of the dependant site can be as well. Subresource integrity allows the browser to verify the javascript or stylesheet has not been unexpectedly modified.

### Recommendation

Set the `integrity` attribute for external javascripts and stylesheets.

`<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js" integrity="sha384-6ePHh72Rl3hKio4HiJ841psfsRJveeS+aLoaEf3BWfS+gTF0XdAqku2ka8VddikM"></script>`

### Note

You should always provide local copies of the external script and implement a way to fall back to loading them in case the external load fails the integrity check. Otherwise your site might break. Example:

`<script>window.jQuery || document.write('<script src="/jquery.min.js"><\/script>')</script>`

### Further reading

[A CDN that can not XSS you: Using Subresource Integrity](https://frederik-braun.com/using-subresource-integrity.html) []()

## Iframe Sandbox

Iframes are everywhere on the WWW. The average website [has 5.1 iframes](http://www.debug.is/2015/04/15/youve-been-framed), mostly for loading third-party content. These iframes have many ways to harm the hosting websites, including running scripts and plugins and redirecting visitors. The `sandbox` attribute enables restrictions to be placed on what can be done inside the iframe.

### Recommendation

Set the `sandbox` attribute for iframes and then add the required permissions.

`<iframe src="https://example.com" sandbox="allow-same-origin allow-scripts"></script>`

### Further reading

[HTML5 Rocks - Play safely in sandboxed IFrames](http://www.html5rocks.com/en/tutorials/security/sandboxed-iframes/)<br>
[You've Been Framed!](http://www.debug.is/2015/04/15/youve-been-framed/) - A survey of iframes and the sandbox attribute. []()

## Server Clock

Servers include a timestamp in all responses. An inaccurate clock doesn't cause problems for the client browser. However, issues can arise when interfacing with other systems or services.

### Recommendation

Use the network time protocol (NTP) to keep server clocks accurate.

### Further reading

[What is NTP?](http://www.ntp.org/ntpfaq/NTP-s-def.htm)

[]()

## Server Banner

Most web servers set headers to identify themselves and their version number. This serves only informational purpose and practical uses are very limited. Removing the entire header, while perfectly acceptable, is usually not necessary. However, it is recommended to remove the version numbers from the header. In the case that bugs exist for a specific web server version, including the version number can act as an invitation for script kiddies to try exploits against the server.

### Recommendation

Include the server name but remove the version number.

`Server: nginx`

### Further reading

[How to configure your web server to not disclose it's identity](http://www.acunetix.com/blog/articles/configure-web-server-disclose-identity/) []()[]()

## Web Framework Information

Many web frameworks set HTTP headers identifying the framework and/or version number. This serves little purpose except for satisfying the curiosity of users and mainly acts as an advertisement for the technology stack. These headers are non-standard and have no effect on how browsers render the site.

While they have little practical use, these headers can prove invaluable to robots or spiders searching for websites running outdated versions of software which may contain security vulnerabilities. These headers can make easy targets out of websites if they aren't regularly updated.

### Recommendation

Remove these headers from the server responses: `X-Powered-By`, `X-Runtime`, `X-Version` and `X-AspNet-Version`

### Further reading

[Shhh... don't let your response headers talk too loudly](http://www.troyhunt.com/2012/02/shhh-dont-let-your-response-headers.html)

[]()[]()

## Cookie Security

Cookies containing sensitive information, especially session IDs, need to be marked as _secure_, assuming the website is server over HTTPS. This stops the cookies from being sent clear-text over HTTP. The only other way for a website to prevent non-secure cookies from being transferred over HTTP is through [HSTS](https://httpsecurityreport.com/best_practice.html). Using both secure cookies and HSTS is recommended.

Session cookies should be marked with the _HttpOnly_ value to prevent them from being accessible from javascript. This prevents attackers from leveraging XSS to steal session cookies, amongst other things. Other cookies might not strictly need to be marked this way. But unless there's a clear need to access their values from javascript, it's best to stay on the safe side and mark all cookies as _HttpOnly_

### Recommendation

Mark all cookies _secure_ and _HttpOnly_.

`Set-Cookie: Key=Value; path=/; secure; HttpOnly, Key2=Value2; secure; HttpOnly`

### Further reading

[OWASP Session Management Cheat Sheet - Cookies](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet#Cookies)
