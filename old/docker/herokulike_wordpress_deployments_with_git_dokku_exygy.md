# Heroku-like WordPress Deployments with git & dokku | Exygy

[Original URL](http://exygy.com/heroku-like-wordpress-deployments-with-git-and-dokku/)

> This is part 2 of a 3 part tutorial: For WordPress sites, I'm always in search of making updates as easy as possible. For me, this means using some form of git deployment. There are a number...

This is part 2 of a 3 part tutorial:

For WordPress sites, I'm always in search of making updates as easy as possible. For me, this means using some form of git deployment. There are a number of options for WordPress-powered sites. WP Engine and Pantheon have excellent git based deployment systems if you're looking for a managed server environment. You can roll your own Capistrano setup, if you have the ability to change the web root to a symlink (usually need root access on a VPS). Probably the simplest version is to have your web server work as a bare git remote and use the post-receive hook to checkout the new code into your web root.

dokku is a newer option, that combines git, Heroku build packs, and docker for a sweet container based server setup. Let's see what it takes for us to get WordPress up and running with dokku.

See [part 1](http://exygy.com/dokku-a-mini-heroku-on-your-vps "dokku: a mini-Heroku on your VPS") of this tutorial for getting a server setup

## 2\. Start a new git repo with a copy of WordPress.

I like to run the root of the website from a subdirectory in my application repository, so my configuration files aren't hanging around in the web root. So let's create a git repo with WordPress core in the `/web` directory

```
$ curl -O https://wordpress.org/latest.zip # get the latest version of WordPress
$ unzip latest.zip
$ mkdir mysite # or whatever you’d like your site repo to be called
$ mv wordpress mysite/web # move WordPress into a subdirectory of your repo
$ cd mysite
$ git init
```

Add a WordPress specific `.gitignore` file:

```
# Always-ignore extensions #
############################
*.bak
*.diff
*.err
*.orig
*.log
*.rej
*.swo
*.swp
*.zip
*.vi
*~
*.un~
*.sass-cache

# OS generated files #
######################
*~
.svn
.cvs
.DS_Store*
._*
.Spotlight-V100
.Trashes
Icon?
ehthumbs.db
Thumbs.db
.cache
.project
.settings
.tmproj
*.esproj
nbproject
*.sublime*

# Folders to ignore #
#####################
.hg
.svn
.CVS
.idea
node_modules
dist
.rvmrc
.vagrant
vendor

# WordPress files #
###################
.env
**/wp-content/uploads/
**/wp-content/blogs.dir/
**/wp-content/upgrade/
**/wp-content/backup-db/
**/wp-content/wp-cache-config.php
**/wp-content/cache/
**/wp-content/backups/
sitemap.xml
sitemap.xml.gz
```

Make your first commit:

```
$ git add .
$ git commit -m “Initial commit”
```

## 3\. Configure our application to for using php

The [Heroku php buildpack](https://github.com/heroku/heroku-buildpack-php) uses composer for configuration and setup.

a. Install composer for your system: <https://getcomposer.org/doc/00-intro.md>

Installing through [homebrew](http://brew.sh/) is recommended for Mac OSX users:

```
$ brew tap homebrew/dupes
$ brew tap homebrew/versions
$ brew tap homebrew/homebrew-php
$ brew update
$ brew install composer
```

b. Create a composer.json file:

```
$ composer init

Welcome to the Composer config generator 
This command will guide you through creating your composer.json config.

Package name (<vendor>/<name>) [you/mysite]: you/mysite
Description []: WordPress powered website
Author [John Doe <john@doe.com>]: 
Minimum Stability []: stable
License []: GPL-2.0

Define your dependencies.

Would you like to define your dependencies (require) interactively [yes]? no
Would you like to define your dev dependencies (require-dev) interactively [yes]? no

{
 “name”: “you/mysite”,
 “description”: “WordPress powered website”,
 “license”: “GPL-2.0”,
 “authors”: [
 {
 “name”: “John Doe”,
 “email”: “john@doe.com”
 }
 ],
 “minimum-stability”: “stable”,
 “require”: {}
}

Do you confirm generation [yes]? yes
```

Now add which version of php we require:

```
“require”: {
 “php”: “>=5.2.4”
}
```

And add our `composer.json` to git:

```
$ git add .
$ git commit -m “Added composer configuration”
```

## 4\. Configure the web server

Heroku's php build pack offers a number of configuration options:

<https://devcenter.heroku.com/categories/php>

Of particular interest are

<https://devcenter.heroku.com/articles/php-support><br>
<https://devcenter.heroku.com/articles/custom-php-settings>

First we need to create a Procfile. This will tell the buildpack which web server to use, what the web root should be, and specify a custom configuration file.

```
web: vendor/bin/heroku-php-nginx -C config/nginx-custom.conf web/
```

This tells the buildpack

- Use nginx for the web server
- Add the settings in `config/nginx-custom.conf` to the configuration
- Serve the site from the `web/` directory (this should always be the last argument in the command, after any other options)

nginx needs some custom settings for WordPress to work correctly. Let's create our `config/nginx.custom.conf` file

```
# Custom nginx configuration #
##############################

# WordPress permalinks
location / {
 index index.php index.html;
 try_files $uri $uri/ /index.php?$args;
}

# Everything below here is optional, but recommended

# Add trailing slash to */wp-admin requests.
rewrite /wp-admin$ $scheme://$host$uri/ permanent;

# Deny access to any files with a .php extension in the uploads directory
# Works in sub-directory installs and also in multisite network
location ~* /(?:uploads|files)/.*.php$ {
 deny all;
}

# enable gzip compression
 gzip on;
 # Minimum file size in bytes (really small files aren’t worth compressing)
 gzip_min_length 1000;
 # Compression level, 1-9
 gzip_comp_level 2;
 gzip_buffers 4 32k;
 gzip_types text/plain application/javascript text/xml text/css image/svg+xml;
 # Insert `Vary: Accept-Encoding` header, as specified in HTTP1.1 protocol 
 gzip_vary on;
# end gzip configuration

# Set time to expire for headers on assets
location ~* .(js|css|png|jpg|jpeg|gif|ico|svg)$ {
 expires 1y;
}

# Sitemap url, for WordPress SEO plugin
rewrite ^/sitemap_index.xml$ /index.php?sitemap=1 last;
rewrite ^/([^/]+?)-sitemap([0-9]+)?.xml$ /index.php?sitemap=$1&sitemap_n=$2 last;</code></pre>
Now commit our configuration files to git.
```

## 5\. Deploy to dokku

We're ready for our first deployment to dokku. Add dokku as a git remote and push.

```
$ git remote add dokku dokku@myserver.com:my site
$ git push dokku master
Counting objects: 1275, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (1251/1251), done.
Writing objects: 100% (1275/1275), 5.94 MiB | 388.00 KiB/s, done.
Total 1275 (delta 101), reused 0 (delta 0)
——> Cleaning up …
——> Building mysite …
——> PHP app detected
——> Resolved composer.json requirement for PHP >=5.2.4 to version 5.6.1.
——> Installing system packages…
 - PHP 5.6.1
 - Apache 2.4.10
 - Nginx 1.6.0
——> Installing PHP extensions…
 - zend-opcache (automatic; bundled, using ‘ext-zend-opcache.ini’)
——> Installing dependencies…
 Composer version 1.0-dev (1d8f05f1dd0e390f253f79ea86cd505178360019) 2015-02-11 11:31:57
 Loading composer repositories with package information
 Installing dependencies
 Nothing to install or update
 Generating optimized autoload files
——> Preparing runtime environment…
——> Discovering process types
 Procfile declares types -> web
 Default process types for PHP -> web
——> Releasing mysite …
——> Deploying mysite …
——> Running pre-flight checks
 check-deploy: /home/dokku/mysite/CHECKS not found. attempting to retrieve it from container …
 CHECKS file not found in container. skipping checks.
——> Running post-deploy
——> Creating new /home/dokku/mysite/VHOST…
——> Configuring mysite.myserver.com…
——> Creating http nginx.conf
——> Running nginx-pre-reload
 Reloading nginx
=====> Application deployed:
 http://mysite.myserver.com

To dokku:mysite
 01orgvirtualstoragemanager.md avrtutorial.md azureazurexplatcli.md data.json data_ml devops docker embedded gaming github go how_to_create_flexible_services_for_a_coreos_cluster_with_fleet_unit_files.md how_to_serve_flask_applications_with_uwsgi_and_nginx_on_ubuntu_1404.md how_to_set_up_automatic_deployment_with_git_with_a_vps.md how_to_use_fabric_to_automate_administration_tasks_and_deployments.md how_to_use_fleet_and_fleetctl_to_manage_your_coreos_cluster.md html installing_paz_on_digital_ocean_·_paz.md javascript linux mobile null.md one_ui_to_rule_them_all_manage_your_docker_containers_along_with_your_servers_and_vms.md piwigo_is_open_source_photo_gallery_software_for_the_web.md png programming quick_start_guide_—_gluster.md recordit_blog.md science simple_blog_deployment_using_ghost_and_docker__packt.md social storage_ceph.md tidy url_to_filename.csv web_dev yodlrcoregi.md [new branch] master -> master
```

Woohoo! You've deployed the site. Now visit mysite.myserver.com.

[![Screen Shot 2015-02-12 at 2.16.50 PM](http://exygy.com/wp-content/uploads/2015/02/Screen-Shot-2015-02-12-at-2.16.50-PM-198x300.png)](http://exygy.com/wp-content/uploads/2015/02/Screen-Shot-2015-02-12-at-2.16.50-PM.png)

You're prompted to setup WordPress. However, we still need a database for WordPress to work. It would also be nice to have a domain.

## 6\. Domain for our WordPress site (optional)

If we want our site to have it's own domain, rather than just a subdomain of myserver.com, dokku makes it easy for our web server to assign a domain to this application.

```
# To access the site from http://mysite.com
$ ssh -t dokku@myserver.com domains:add mysite mysite.com
```

After change your domain's DNS settings to point to your dokku server you should now be able to visit the site in your browser using the domain you specified.

## 7\. Setup a database

dokku has several plugins that extend its functionality, like for databases and caching. Let's add mariadb as a database to our application. (mariadb is a drop-in, fully compatible alternative to mysql)

[Maria DB plugin for dokku](https://github.com/ohardy/dokku-mariadb)

This plugin is meant to use one database container for all of your applications. If you prefer a separate container for each app, try <https://github.com/Kloadut/dokku-md-plugin>

To setup, ssh into your dokku server. You should run these commands with root privileges:

```
$ cd /var/lib/dokku/plugins
$ sudo git clone https://github.com/ohardy/dokku-mariadb mariadb
$ sudo dokku plugins-install
```

Now, from your local machine, setup a database for the WordPress site:

```
$ ssh -t dokku@myserver.com mariadb:start
$ ssh -t dokku@myserver.com mariadb:create mysite

# We should now see a database with the same name as our application
ssh -t dokku@myserver.com mariadb:list
Database
information_schema
mysql
performance_schema
mysite
```

## 8\. Configure WordPress

Create a `wp-config.php` file with with database credentials:

```
$ cp web/wp-config-sample.php web/wp-config.php
```

And add our database credentials. The mariadb plugin add them to the server environment, so we can access them with `get_env()`. Note that the password is `’DB_PASS’` (as opposed to the WordPress php constant, `DB_PASSWORD`) and to include both the host and port for `’DB_HOST’` :

```
// 01orgvirtualstoragemanager.md avrtutorial.md azureazurexplatcli.md data.json data_ml devops docker embedded gaming github go how_to_create_flexible_services_for_a_coreos_cluster_with_fleet_unit_files.md how_to_serve_flask_applications_with_uwsgi_and_nginx_on_ubuntu_1404.md how_to_set_up_automatic_deployment_with_git_with_a_vps.md how_to_use_fabric_to_automate_administration_tasks_and_deployments.md how_to_use_fleet_and_fleetctl_to_manage_your_coreos_cluster.md html installing_paz_on_digital_ocean_·_paz.md javascript linux mobile null.md one_ui_to_rule_them_all_manage_your_docker_containers_along_with_your_servers_and_vms.md piwigo_is_open_source_photo_gallery_software_for_the_web.md png programming quick_start_guide_—_gluster.md recordit_blog.md science simple_blog_deployment_using_ghost_and_docker__packt.md social storage_ceph.md tidy url_to_filename.csv web_dev yodlrcoregi.md MySQL settings - You can get this info from your web host 01orgvirtualstoragemanager.md avrtutorial.md azureazurexplatcli.md data.json data_ml devops docker embedded gaming github go how_to_create_flexible_services_for_a_coreos_cluster_with_fleet_unit_files.md how_to_serve_flask_applications_with_uwsgi_and_nginx_on_ubuntu_1404.md how_to_set_up_automatic_deployment_with_git_with_a_vps.md how_to_use_fabric_to_automate_administration_tasks_and_deployments.md how_to_use_fleet_and_fleetctl_to_manage_your_coreos_cluster.md html installing_paz_on_digital_ocean_·_paz.md javascript linux mobile null.md one_ui_to_rule_them_all_manage_your_docker_containers_along_with_your_servers_and_vms.md piwigo_is_open_source_photo_gallery_software_for_the_web.md png programming quick_start_guide_—_gluster.md recordit_blog.md science simple_blog_deployment_using_ghost_and_docker__packt.md social storage_ceph.md tidy url_to_filename.csv web_dev yodlrcoregi.md //
/** The name of the database for WordPress */
define(‘DB_NAME’, getenv(‘DB_NAME’));

/** MySQL database username */
define(‘DB_USER’, getenv(‘DB_USER’));

/** MySQL database password */
define(‘DB_PASSWORD’, getenv(‘DB_PASS’));

/** MySQL hostname */
define(‘DB_HOST’, getenv(‘DB_HOST’).’:’.getenv(‘DB_PORT’));
```

It's also a good idea to fill out the authentication keys and salts, by visiting <https://api.wordpress.org/secret-key/1.1/salt/> and pasting that into the file. You could also get fancy and add them as environmental variables to your dokku application environment, but I'll leave that for you to figure out.

Commit `wp-config.php` to git, and push to dokku. You should now be able to finish WordPress setup from mysite.com

[![Screen Shot 2015-02-12 at 3.03.57 PM](http://exygy.com/wp-content/uploads/2015/02/Screen-Shot-2015-02-12-at-3.03.57-PM-246x300.png)](http://exygy.com/wp-content/uploads/2015/02/Screen-Shot-2015-02-12-at-3.03.57-PM.png)

## 9\. Uploads

We're almost there. Now you should have a functioning WordPress site. However, whenever you update the code for the site, the server environment is recreated from scratch. This means that any file uploads will get wiped out. We need to use set a persistent volume for our `wp-content/uploads` folder to save our uploads.

Let's leverage another docker plugin, <https://github.com/ohardy/dokku-volume>

ssh into our dokku server to install:

```
$ cd /var/lib/dokku/plugins
$ sudo git clone https://github.com/ohardy/dokku-volume volume
$ sudo dokku plugins-install
```

And now, from our local machine, configure the volume. We need to reference the path absolutely (with a leading `/`). Docker runs our app from within an `app/` directory, so we reference the uploads folder from there:

```
$ ssh -t dokku@myserver.com volume:add mysite /app/web/wp-content/uploads 
$ ssh -t dokku volume:list mysite
/home/dokku/.o_volume/data/random-hash:/app/web/wp-content/uploads
```

In order to activate the volume, we need to push our application again. Make some incremental change, like adding an extra line, commit, and push to dokku.

Now upload an image. To check and make sure our volume is working, ssh into your dokku server:

```
$ ls /home/dokku/.o_volume/data/random-hash # use the same hash that displayed above when we listed the volumes
```

You should see a folder for the current year, after you've uploaded a file. Now uploads will persist across deployments.

You now have a working WordPress site running on dokku!

In Part III of this tutorial, I'll cover optimizing your WordPress site with redis caching and configuring SSL. And let me know in the comments if there's anything else I should cover about WordPress and dokku.
