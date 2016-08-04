# How to look up the geographic location of an IP address from the command line

[Original URL](http://xmodulo.com/geographic-location-ip-address-command-line.html)

> If you want to find out where a given IP address is physically located on earth, there are quite a few online GeoIP lookup services you can try (e.g. geoiptool.com). These online services are mostly...

If you want to find out where a given IP address is physically located on earth, there are quite a few online GeoIP lookup services you can try (e.g. geoiptool.com). These online services are mostly powered by freely available GeoIP databases such as those from [MaxMind](http://dev.maxmind.com/geoip/legacy/geolite/). Besides using such web-based services, there are different ways to query the GeoIP databases, notably via the Linux command line.

In this tutorial, I am going to describe **how to geolocate an IP address from the command line in Linux**.

## Method One

The first method is to use `geoiplookup` tool which is a command-line client for MaxMind's GeoIP databases. `geoiplookup` allows you to look up the geography or network information of an IP address (or hostname). You can install the tool (along with the free GeoIP database used by the tool) as follows.

To install `geoiplookup` on Debian, Ubuntu or Linux Mint:

$ sudo apt-get install geoip-bin

To install `geoiplookup` on Fedora:

$ sudo yum install geoip

To install `geoiplookup` on CentOS, first [enable EPEL repository](http://xmodulo.com/2013/03/how-to-set-up-epel-repository-on-centos.html), and then use `yum` command:

$ sudo yum install geoip

The default installation of `geoiplookup` comes with `GeoIP.dat` database file which is located in /usr/share/GeoIP. With this database, you can look up the country information only.

$ geoiplookup 23.66.166.151

```
GeoIP Country Edition: US, United States
```

You can download additional GeoIP databases from [MaxMind](http://dev.maxmind.com/geoip/legacy/geolite/), which give you more detailed information about IP addresses beyond country info. You can also download more up-to-date `GeoIP.dat` from the site. This is recommended because `GeoIP.dat` may have already been outdated by the time you install it from Linux repositories. The GeoIP databases available on MaxMind website are updated every month.

To install additional GeoIP databases from MaxMind, do the following. You may want to set up a monthly cronjob to automate this process.

$ wget <http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz><br>
$ wget <http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz><br>
$ wget <http://download.maxmind.com/download/geoip/database/asnum/GeoIPASNum.dat.gz><br>
$ gunzip GeoIP.dat.gz<br>
$ gunzip GeoIPASNum.dat.gz<br>
$ gunzip GeoLiteCity.dat.gz<br>
$ sudo cp GeoIP.dat GeoIPASNum.dat GeoLiteCity.dat /usr/share/GeoIP/

Now if you re-run `geoiplookup`, you will see the additional AS number information of an IP address. This basically tells you which administrative domain the IP address belongs to.

$ geoiplookup 128.112.119.209

```
GeoIP Country Edition: US, United States
GeoIP ASNum Edition: AS88 Princeton University
```

When run without any parameter, `geoiplookup` tool automatically uses `GeoIP.dat` and `GeoIPASNum.dat` only, but not use `GeoLiteCity.dat`. The latter can give you city-level information.

To obtain city-level geolocation information, explicitly tell `geoiplookup` to use `GeoLiteCity.dat` database.

$ geoiplookup -f /usr/share/GeoIP/GeoLiteCity.dat 23.66.166.151

```
GeoIP City Edition, Rev 1: US, MA, Cambridge, 02142, 42.362598, -71.084297, 506, 617
```

The output includes state, city, zipcode, latitude and longitude. The accuracy of the inferred location [varies](http://www.maxmind.com/en/geolite_city_accuracy) across different countries and networks. For example, the geolocation result tends to be more accurate for broadband IP addresses, but not as accurate for mobile networks.

## Method Two

If you want to avoid the hassle of installing and updating GeoIP databases, you can try [ipinfo.io](http://ipinfo.io/) online service. Unlike other services, ipinfo.io provides JSON-based geolocation API, so you can easily look up geolocation from the command line, using tools like `curl`.

$ curl ipinfo.io/23.66.166.151

[![](http://farm4.staticflickr.com/3770/13117907603_3cce4123de_z.jpg)](http://www.flickr.com/photos/xmodulo/13117907603/)

Note that the access to their API is rate-limited at 1,000 API requests per day.

### ![](http://xmodulo.com/images/rss_small.png) Subscribe to Xmodulo

Do you want to receive **Linux FAQs, detailed tutorials and tips** published at Xmodulo? Enter your email address below, and we will deliver our Linux posts straight to your email box, for free. Delivery powered by Google Feedburner.
