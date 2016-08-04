# Your OpenStreetMap server in 120GB

[Original URL](http://thinkonbytes.blogspot.com/2016/07/your-openstreetmap-server-in-120gb.html)

> 1. Install Ubuntu 14.04 server. Remember to enable the firewall: costales@maps:~$ sudo ufw allow http costales@maps:~$ sudo ufw allow ssh costales@maps:~$ sudo ufw enable 2\. Check that you have all...

1. Install [Ubuntu 14.04 server](http://releases.ubuntu.com/14.04/).<br>
  Remember to enable the firewall:<br>
  costales@maps:~$ sudo ufw allow http<br>
  costales@maps:~$ sudo ufw allow ssh

costales@maps:~$ sudo ufw enable

1. Check that you have all locales right:

costales@maps:~$ locale

If some of them are empty, add them to /etc/environment, in my case LC_ALL & LANGUAGE:

costales@maps:~$ cat /etc/environment<br>
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"<br>
_LC_ALL=en_US.UTF-8_<br>
_LANGUAGE=en_US.UTF-8_<br>
costales@maps:~$

1. Install the [server from a PPA](https://switch2osm.org/serving-tiles/building-a-tile-server-from-packages/):

sudo apt-get install software-properties-common

sudo add-apt-repository ppa:kakrueger/openstreetmap

sudo apt-get update

sudo apt-get install libapache2-mod-tile osmctools

1. Import a map: We'll drop so many data for allow the smallest database, then space in hard disk ;)

4.1 Download [from here](http://download.geofabrik.de/) in pbf. For example, europe-latest.osm.pbf:

costales@maps:~$ wget <http://download.geofabrik.de/**europe-latest.osm.pbf**>

4.2 Do it small, we'll keep only the roads:

costales@maps:~$ osmconvert **europe-latest.osm.pbf** -o=

**<span>europe.o5m</span>**

costales@maps:~$ osmfilter

**<span>europe.o5m</span>**

 –drop-author –drop-version –keep="highway=cycleway" –keep="highway=path" –keep="highway=footway" –keep="highway=track" –keep="highway=service" –keep="highway=pedestrian" –keep="highway=unclassified" –keep="highway=residential" –keep="highway=tertiary" –keep="highway=secondary" –keep="highway=primary" –keep="highway=trunk" –keep="highway=motorway" –keep="highway=" –drop-tags="alt_name" –drop-tags="source" –drop-tags="maxspeed" –drop-tags="created_by" –drop-tags="wheelchair*" -o=

**<span>europe_tmp.o5m</span>**

costales@maps:~$ osmconvert 

**<span>europe_tmp.o5m</span>**

 -o=

**<span>europe_end.pbf</span>**

costales@maps:~$ rm europe-latest.osm.pbf europe.o5m europe_tmp.o5m

4.3 Import it into the database:

costales@maps:~$ osm2pgsql –drop –slim -C 1700 –number-processes 2 

**<span>europe_end.pbf</span>**

_1700_ is the GB of RAM and _2_ the CPUs.

1. Set it as complete and restart the service:

costales@maps:~$ touch /var/lib/mod_tile/planet-import-complete

costales@maps:~$ sudo /etc/init.d/renderd restart

1. It's done! Check it: 

  <span>
    <a href="http://localhost/osm/slippymap.html">http://localhost/osm/slippymap.html</a>
  </span>
