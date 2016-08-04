# Install Spotweb on Raspberry Pi for Personal Usenet Indexer

[Original URL](http://www.htpcguides.com/install-spotweb-on-raspberry-pi-for-personal-usenet-indexer/)

> Spotweb is a usenet indexing software very popular in the Netherlands. Spotweb has a simple interface for displaying the latest spots and associated nzbs. The Spotweb framework provides a...

![spotweb-logo]()

[Spotweb](https://github.com/spotweb/spotweb) is a usenet indexing software very popular in the Netherlands. Spotweb has a simple interface for displaying the latest spots and associated nzbs. The Spotweb framework provides a newznab compatible API that can be used as a personal indexer with automation software like Sonarr, SickRage, CouchPotato, Headphones, Mylar, LazyLibrarian and more. The Raspberry Pi works as a Spotweb personal usenet indexer surprisingly well updating spots every hour. This guide was tested on a Raspberry Pi 2 but should work on earlier versions as well like the Raspberry Pi B+ and A models. Spotweb requires several components to function

- MySQL database for storing the category of spots and nzbs
- PHP for processing headers and displaying the Spotweb web interface

## Install MySQL

Update your Raspberry Pi repositories

```
sudo apt-get update
```

Install MySQL to store Spots and nzb information on the Raspberry Pi

```
sudo apt-get install mysql-server -y
```

Enter your MySQL as the root user to create the spotweb user and database

```
mysql -u root -p
```

Create a MySQL user for spotwebuser, create the database called spotwebdb and grant privileges to the spotwebuser.

```
CREATE USER spotwebuser@localhost IDENTIFIED BY 'password';
CREATE DATABASE spotwebdb;
GRANT ALL PRIVILEGES ON spotwebdb.* TO spotwebuser@localhost IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
exit
```

## Install PHP

Install PHP or Spotweb won't run

```
sudo apt-get install php5-mysql php5-curl php5-gd php5-cli -y
```

Change your timezone for php

```
sudo nano /etc/php5/fpm/php.ini
```

Uncomment ;date.timezone= by deleting the semicolon, a list of timezones can be found [here](http://php.net/manual/en/timezones.php)

```
date.timezone = Europe/Copenhagen
```

You can adjnust the amount of memory php can use though I have found that Spotweb indexing is more CPU intensive than anything else.

```
memory_limit = 512M
```

Ctrl+X, Y and Enter to Save

Make the same changes in the command line php.ini since this is what is used for grabbing new headers

```
sudo nano /etc/php5/cli/php.ini
```

## Install nginx

Install nginx and php5-fpm for serving PHP, SSL is included in case you want to use https

```
sudo apt-get install nginx openssl php5-fpm -y
```

Create the Spotweb nginx vhost configuration

```
nano /etc/nginx/sites-available/spotweb
```

Here is the Spotweb nginx configuration adapted from [here](https://github.com/Thermionix/Dockerfiles/blob/master/spotweb/spotweb.nginx.conf)

```
server {
    listen 80;
    server_name htpcguides.crabdance.com, 192.168.40.120;
    root /var/www;
    index index.html index.htm index.php;

    location /spotweb {

 satisfy any;

 if ($uri !~ "api/"){
 rewrite /api/?$ /spotweb/index.php?page=newznabapi last;
 }
    location ~ \.php$ {
 try_files $uri =404;
 include fastcgi_params;
 fastcgi_pass unix:/var/run/php5-fpm.sock;
 fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
    }
}
```

Disable the default nginx virtual host and enable the nginx Spotweb virtua host

```
unlink /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/spotweb /etc/nginx/sites-enabled/spotweb
```

## Install Spotweb on Raspberry Pi

Install git so you can get the Spotweb source

```
apt-get install git -y
```

Grab the latest Spotweb

```
git clone https://github.com/spotweb/spotweb /var/www/spotweb
```

Change the owner of Spotweb to be www-data so nginx has access to it

```
chown -R www-data:www-data /var/www/spotweb
```

Restart nginx and php5-fpm

```
service nginx restart
service php5-fpm restart
```

## Configure Spotweb on Raspberry Pi

Go to <http://ip.address/spotweb/install.php> where ip.address is your Raspberry Pi's IP address to begin configuring Spotweb

This is the Spotweb php check to make sure you have all the dependencies, click **Next**

[![spotweb step 1 php]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-step-1-php.png)

Enter your MySQL database name, username and password we created earlier and click **Verify database**

[![]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-step-2-mysql-configuration.png)

Choose your Usenet server from the dropdown and enter your credentials, click **Verify usenet server**

[![spotweb step 3 usenet server]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-step-3-usenet-server.png)

Now it's time to create the site type and an Administrative user. I chose Single user since this is a personal indexer I won't be sharing with anybody, click **Create system** when you are done

[![spotweb step 4 users]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-step-4-users.png)

Installation is now complete

[![spotweb step 5 installation complete]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-step-5-installation-complete.png)

Now navigate to <http://ip.address/spotweb> and let's start the initial retrieve process

Click **Config** in the top right and then **Settings**

[![spotweb click settings]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-click-settings.png)

Click the **Retrieve** tab

Set your retrieve settings and then run the initial retrieval manually. It will take a very long time depending on how far back you have chosen to index.

Move forward in time in the calendar to choose the date from which to index. In the example below spots from March 2015 onwards will be indexed

Consider unchecking comments as it requires more time to process.

[![spotweb change retrieve settings]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-change-retrieve-settings.png)

Run the Spotweb retrieve in a screen session so you can close the SSH session and still let it run in the background

```
sudo apt-get install screen -y
```

Enter a screen session

```
screen
```

Run the Spotweb retrieve script

```
php /var/www/spotweb/retrieve.php
```

Now you can close the SSH and re-attach the screen session to see how it's doing

```
screen -r
```

When the initial Spotweb retrieval is done add a cronjob to have your spots updated at a fixed interval. This cronjob checks for new spots every 30 minutes, you can increase or decrease it if you want.

It will create a fresh log file every hour on the hour at /var/www/spotweb/retrieve.log

```
crontab -l | { cat; echo "00 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii php /var/www/spotweb/retrieve.php > /var/www/spotweb/retrieve.log"; } | crontab -
```

When adding Spotweb as an indexer for automation sfotware like Sonarr, SickRage, CouchPotato, Mylar, Headphones, LazyLibrarian and others. Choose newznab as the indexer type. The URL is <http://192.168.40.120/spotweb/> where 192.168.40.120 is your Spotweb indexer's IP address.

You can get your Spotweb API key by clicking your username in the top right corner and choosing **Change User**, scroll down to **API Key** and copy it to the clipboard and paste it in your automation software.

[![]()](http://www.htpcguides.com/wp-content/uploads/2015/06/spotweb-show-api-key.png)

## Move the SQL Database

It is a good idea to move your Spotweb MySQL database off the SD card, if you plan on retaining a lot of spots and nzbs you will eventually run out of space on the SD card.

Stop MySQL and gninx temporarily

```
sudo service mysql stop
sudo service nginx stop
```

Make a MySQL directory on your USB or SATA drive and change the owner to mysql

```
mkdir -p /mnt/usbstorage/mysql
sudo chown -R mysql:mysql /mnt/usbstorage/mysql
```

Copy the database over

```
cp -r /var/lib/mysql/ /mnt/usbstorage/mysql
```

Change the MySQL configuration

```
sudo nano /etc/mysql/my.cnf
```

Find this line and adjust it to your USB storage path's mysql folder

```
datadir         = /var/lib/mysql
```

Ctrl+X, Y and Enter to Save

Restart MySQL and nginx

```
sudo service mysql restart
sudo service nginx restart
```

Spotweb is installed on the Raspberry Pi and you can enjoy your personal Spotweb indexer to browse or use for automation
