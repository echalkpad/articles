# How to use GeoIP with Nginx on Ubuntu 16.04

[Original URL](https://www.howtoforge.com/tutorial/how-to-use-geoip-with-nginx-on-ubuntu-16.04/)

> Apply to multiple top tech companies with 1 application on Indeed Prime This tutorial explains how to use the GeoIP module with nginx on Ubuntu 16.04 to find out where your visitors come from. The...

[Apply to multiple top tech companies with 1 application on Indeed Prime](https://srv.buysellads.com/ads/click/x/GTND423NF6AD5KJECT74YKQWCYAIE5QEFT7IVZ3JCESDL5QWCTBDTKQKC6BDK5QYCESIKK3EHJNCLSIZ)![](https://www.launchbit.com/taz-pixel/11341-6487-111)

This tutorial explains how to use the GeoIP module with nginx on Ubuntu 16.04 to find out where your visitors come from. The GeoIP module sets multiple variables like 

<span class="system">$geoip_country_name</span>

, 

<span class="system">$geoip_country_code</span>

, 

<span class="system">$geoip_city</span>

, etc. that you can use in your PHP scripts or directly in your nginx configuration, for example to serve content in different languages based on the user's country.

## 1 Preliminary Note

I'm using the website 

<span class="system">www.example.com</span>

 here with the document root 

<span class="system">/var/www/www.example.com/web/</span>

 and the Nginx vhost configuration file 

<span class="system">/etc/nginx/sites-enabled/www.example.com.vhost</span>

. I will use this tutorial for the basic Ubuntu-Nginx setup. <https://www.howtoforge.com/tutorial/installing-nginx-with-php7-fpm-and-mysql-on-ubuntu-16.04-lts-lemp/>[](https://www.howtoforge.com/tutorial/installing-nginx-with-php7-fpm-and-mysql-on-ubuntu-16.04-lts-lemp/)

This tutorialis compatible with ISPConfig nginx setups as well.

**A note for Ubuntu users:**

Because we must run all the steps from this tutorial with root privileges, we can either prepend all commands in this tutorial with the string 

<span class="system">sudo</span>

, or we become root right now by typing

sudo -s

## 2 Find out if Nginx has support for GeoIP

Before we start we must find out if the GeoIP module is built into our nginx server:

nginx -V

[[email protected]](https://www.howtoforge.com/cdn-cgi/l/email-protection):~# nginx -V<br>
nginx version: nginx/1.10.0 (Ubuntu)<br>
built with OpenSSL 1.0.2g-fips 1 Mar 2016<br>
TLS SNI support enabled<br>
configure arguments: –with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2' –with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now' –prefix=/usr/share/nginx –conf-path=/etc/nginx/nginx.conf –http-log-path=/var/log/nginx/access.log –error-log-path=/var/log/nginx/error.log –lock-path=/var/lock/nginx.lock –pid-path=/run/nginx.pid –http-client-body-temp-path=/var/lib/nginx/body –http-fastcgi-temp-path=/var/lib/nginx/fastcgi –http-proxy-temp-path=/var/lib/nginx/proxy –http-scgi-temp-path=/var/lib/nginx/scgi –http-uwsgi-temp-path=/var/lib/nginx/uwsgi –with-debug –with-pcre-jit –with-ipv6 –with-http_ssl_module –with-http_stub_status_module –with-http_realip_module –with-http_auth_request_module –with-http_addition_module –with-http_dav_module 

<span class="highlight">–with-http_geoip_module</span>

 –with-http_gunzip_module –with-http_gzip_static_module –with-http_image_filter_module –with-http_v2_module –with-http_sub_module –with-http_xslt_module –with-stream –with-stream_ssl_module –with-mail –with-mail_ssl_module –with-threads

## 3 Download the GeoIP Databases

On Debian and Ubuntu, there is the package 

<span class="system">geoip-database</span>

 that can be installed through apt, but it is a bit outdated and only contains 

<span class="system">GeoIP.dat</span>

 (country database), not 

<span class="system">GeoLiteCity.dat</span>

 (city database). Therefore we don't install that package, but download fresh copies from the GeoIP web site to the 

<span class="system">/etc/nginx/geoip</span>

 directory:

mkdir /etc/nginx/geoip

cd /etc/nginx/geoip<br>
wget <http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz><br>
gunzip GeoIP.dat.gz<br>
wget <http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz><br>
gunzip GeoLiteCity.dat.gz

## 4 Configure Nginx

Now we configure nginx. Open 

<span class="system">/etc/nginx/nginx.conf</span>

...

nano /etc/nginx/nginx.conf

... and add the 

<span class="system">geoip_country</span>

 and 

<span class="system">geoip_city</span>

 directives to the 

<span class="system">http {}</span>

 container:

```
[...]
http { ##
 # Basic Settings
 ## geoip_country /etc/nginx/geoip/GeoIP.dat; # the country IP database
 geoip_city /etc/nginx/geoip/GeoLiteCity.dat; # the city IP database
[...]
```

The 

<span class="system">geoip_country</span>

 directive makes the following variables available:

- <span class="system">$geoip_country_code</span>

   - two-letter country code, for example, 

  <span class="system">RU</span>

  , 

  <span class="system">US</span>

  .
- <span class="system">$geoip_country_code3</span>

   - three-letter country code, for example, 

  <span class="system">RUS</span>

  , 

  <span class="system">USA</span>

  .
- <span class="system">$geoip_country_name</span>

   - the (verbose) name of the country, for example, 

  <span class="system">Russian Federation</span>

  , 

  <span class="system">United States</span>

  , etc.

The 

<span class="system">geoip_city</span>

 directive provides the following variables:

- <span class="system">$geoip_city_country_code </span>

   - two-letter country code, for example, 

  <span class="system">RU</span>

  , 

  <span class="system">US</span>

  .
- <span class="system">$geoip_city_country_code3 </span>

   - three-letter country code, for example, 

  <span class="system">RUS</span>

  , 

  <span class="system">USA</span>

  .
- <span class="system">$geoip_city_country_name </span>

   - the name of the country, for example, 

  <span class="system">Russian Federation</span>

  , 

  <span class="system">United States</span>

   - if available.
- <span class="system">$geoip_region </span>

   - the name of region (province, region, state, province, federal land, and the like), for example, 

  <span class="system">Moscow City</span>

  , 

  <span class="system">DC</span>

   - if available.
- <span class="system">$geoip_city </span>

   - the name of the city, for example, 

  <span class="system">Moscow</span>

  , 

  <span class="system">Washington</span>

  , 

  <span class="system">Lisbon</span>

  , etc. - if available.
- <span class="system">$geoip_postal_code </span>

   - zip code or postal code - if available.
- <span class="system">$geoip_city_continent_code </span>

   - if available.
- <span class="system">$geoip_latitude </span>

   - latitude - if available.
- <span class="system">$geoip_longitude </span>

   - longitude - if available.

In order to make these variables available to your PHP scripts as well, we must set a few 

<span class="system">fastcgi_param</span>

 directives. It is best to do this in the file 

<span class="system">/etc/nginx/fastcgi_params</span>

 where the other 

<span class="system">fastcgi_param</span>

 directives are:

nano /etc/nginx/fastcgi_params

```
[...]
### SET GEOIP Variables ###
fastcgi_param GEOIP_COUNTRY_CODE $geoip_country_code;
fastcgi_param GEOIP_COUNTRY_CODE3 $geoip_country_code3;
fastcgi_param GEOIP_COUNTRY_NAME $geoip_country_name;

fastcgi_param GEOIP_CITY_COUNTRY_CODE $geoip_city_country_code;
fastcgi_param GEOIP_CITY_COUNTRY_CODE3 $geoip_city_country_code3;
fastcgi_param GEOIP_CITY_COUNTRY_NAME $geoip_city_country_name;
fastcgi_param GEOIP_REGION $geoip_region;
fastcgi_param GEOIP_CITY $geoip_city;
fastcgi_param GEOIP_POSTAL_CODE $geoip_postal_code;
fastcgi_param GEOIP_CITY_CONTINENT_CODE $geoip_city_continent_code;
fastcgi_param GEOIP_LATITUDE $geoip_latitude;
fastcgi_param GEOIP_LONGITUDE $geoip_longitude;
```

(Make sure you have the line 

<span class="system">include /etc/nginx/fastcgi_params;</span>

 in your 

<span class="system">location ~ \.php$ {}</span>

 container in your vhost configuration, because otherwise, the above configuration is useless for your vhost.)

If you use nginx as a reverse proxy and want to pass the GeoIP variables to the backend, you should create/edit the file 

<span class="system">/etc/nginx/proxy.conf</span>

...

nano /etc/nginx/proxy.conf

... and add the following lines to it:

```
[...]
### SET GEOIP Variables ###
proxy_set_header GEOIP_COUNTRY_CODE $geoip_country_code;
proxy_set_header GEOIP_COUNTRY_CODE3 $geoip_country_code3;
proxy_set_header GEOIP_COUNTRY_NAME $geoip_country_name;

proxy_set_header GEOIP_CITY_COUNTRY_CODE $geoip_city_country_code;
proxy_set_header GEOIP_CITY_COUNTRY_CODE3 $geoip_city_country_code3;
proxy_set_header GEOIP_CITY_COUNTRY_NAME $geoip_city_country_name;
proxy_set_header GEOIP_REGION $geoip_region;
proxy_set_header GEOIP_CITY $geoip_city;
proxy_set_header GEOIP_POSTAL_CODE $geoip_postal_code;
proxy_set_header GEOIP_CITY_CONTINENT_CODE $geoip_city_continent_code;
proxy_set_header GEOIP_LATITUDE $geoip_latitude;
proxy_set_header GEOIP_LONGITUDE $geoip_longitude;
```

(Make sure you use the line 

<span class="system">include /etc/nginx/proxy.conf;</span>

 in your nginx proxy configuration because otherwise, the backend cannot use the GeoIP variables.)

Now reload nginx...

systemctl reload nginx.service

... for the changes to take effect.

Restart PHP-FPM as follows:

systemctl restart php7.0-fpm.service

## 5 A short Test

To see if the GeoIP module is working correctly, we can create a small PHP file in our 

<span class="system">www.example.com</span>

 web space (e.g. 

<span class="system">/var/www/www.example.com/web</span>

):

nano /var/www/www.example.com/web/geoiptest.php

We can access the GeoIP variables as follows:

$geoip_country_code = getenv(GEOIP_COUNTRY_CODE);

Or like this:

$geoip_country_code = $_SERVER['GEOIP_COUNTRY_CODE'];

```
<html>
<body>
<?php

$geoip_country_code = getenv(GEOIP_COUNTRY_CODE);
/*
$geoip_country_code = $_SERVER['GEOIP_COUNTRY_CODE']; // works as well
*/
$geoip_country_code3 = getenv(GEOIP_COUNTRY_CODE3);
$geoip_country_name = getenv(GEOIP_COUNTRY_NAME);

$geoip_city_country_code = getenv(GEOIP_CITY_COUNTRY_CODE);
$geoip_city_country_code3 = getenv(GEOIP_CITY_COUNTRY_CODE3);
$geoip_city_country_name = getenv(GEOIP_CITY_COUNTRY_NAME);
$geoip_region = getenv(GEOIP_REGION);
$geoip_city = getenv(GEOIP_CITY);
$geoip_postal_code = getenv(GEOIP_POSTAL_CODE);
$geoip_city_continent_code = getenv(GEOIP_CITY_CONTINENT_CODE);
$geoip_latitude = getenv(GEOIP_LATITUDE);
$geoip_longitude = getenv(GEOIP_LONGITUDE);

echo 'country_code: '.$geoip_country_code.'<br>';
echo 'country_code3: '.$geoip_country_code3.'<br>';
echo 'country_name: '.$geoip_country_name.'<br>';

echo 'city_country_code: '.$geoip_city_country_code.'<br>';
echo 'city_country_code3: '.$geoip_city_country_code3.'<br>';
echo 'city_country_name: '.$geoip_city_country_name.'<br>';
echo 'region: '.$geoip_region.'<br>';
echo 'city: '.$geoip_city.'<br>';
echo 'postal_code: '.$geoip_postal_code.'<br>';
echo 'city_continent_code: '.$geoip_city_continent_code.'<br>';
echo 'latitude: '.$geoip_latitude.'<br>';
echo 'longitude: '.$geoip_longitude.'<br>';

?>
</body>
</html>
```

Call that file in a browser (

<span class="system">
  <a href="http://www.example.com/geoiptest.php">http://www.example.com/geoiptest.php</a>
</span>

), and you should see GeoIP at work 

<span class="highlight">(make sure that you’re calling the file from a public IP address, not a local one)</span>

:

[![GeoIP Test](https://www.howtoforge.com/images/how-to-use-geoip-with-nginx-on-ubuntu-16.04/geoip_test1.png)](https://www.howtoforge.com/images/how-to-use-geoip-with-nginx-on-ubuntu-16.04/big/geoip_test1.png)

It is also possible to use GeoIP variables directly in the nginx configuration, e.g. as follows:

nano /etc/nginx/sites-enabled/www.example.com.vhost

```
[...]
 location / {
 index index.html index.php;
 try_files /index_$geoip_country_code.html /index.html;
 }
[...]
```

systemctl reload nginx.service

In this example, if a visitor comes from Germany (country code: 

<span class="system">DE</span>

), and the file 

<span class="system">index_DE.html</span>

 exists, then this file is served, otherwise the default 

<span class="system">index.html</span>

 file is served.

This can be used to serve content in different languages, based on the user's origin.

![](https://www.howtoforge.com/images/pdficon_small.png) [view as pdf](https://www.howtoforge.com/subscription/) | ![](https://www.howtoforge.com/images/print.gif) [print](https://www.howtoforge.com/subscription/)

**Share this page:**