# How to get your very own RStudio Server and Shiny Server with DigitalOcean

[Original URL](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/)

> If you've always wanted to have an RStudio Server of your own so that you can access R from anywhere, or your own Shiny Server to host your awesome shiny apps or Rmarkdown documents,...

If you've always wanted to have an RStudio Server of your own so that you can access R from anywhere, or your own Shiny Server to host your awesome shiny apps or Rmarkdown documents, [DigitalOcean](https://www.digitalocean.com/?refcode=358494f80b99) (DO) can help you get there easily.

DigitalOcean provides virtual private servers (they call each server a _droplet_), which means that you can pay _$5/month_ to have your own server "in the cloud" that you can access from anywhere and host anything on. Check out [my DO droplet](http://daattali.com/) to see it in action! Use [my referral link](https://www.digitalocean.com/?refcode=358494f80b99) to get $10 in credits, which is enough to give you a private server for your first 2 months.

I only found out about DO a couple of months ago when I asked my supervisor, [Jenny Bryan](https://twitter.com/JennyBryan), if there was a way for me to do some R-ing when I'm away from my machine. She told me that she doesn't have a solution for me, but that I should check out DigitalOcean, so I did. And it turns out that it's very convenient for hosting my own RStudio Server and anything else I'd like to host, and very affordable **even for my student self**. :)

This post will cover how to set up a machine from scratch, set up R, RStudio Server, Shiny Server, and a few other helpful features on a brand new DO droplet (remember: droplet = your machine in the cloud). The tutorial might seem lengthy, but it's actually very simple, I'm just breaking up every step into very fine details. Based on feedback from others, it should take no more than 30 minutes to go through this post and get everything set up.

## Table of contents

## Step 1: Sign up to DigitalOcean

Go to [DigitalOcean](https://www.digitalocean.com/?refcode=358494f80b99) (use this referral link to get 2 months!) and sign up. Registration is quick and painless, but I think in order to verify your account (and to get your $10) you have to provide credit card details. If you go to [your Billing page](https://cloud.digitalocean.com/settings/billing) hopefully you will see the $10 credit.

## Step 2: Create a new droplet

Now let's claim one of DO's machines as our own! It's so simple that you definitely don't need my instructions, just click on the big "Create Droplet" button and choose your settings. I chose the smallest/weakest machine ($5/month plan) and it's good enough for me. I also chose San Francisco because it's the closest to me, though it really wouldn't make much of a noticeable difference where the server is located. For OS, I chose to go with the default Ubuntu 14.04 x64\. I highly recommend you add an SSH key at the last step if you know how to do that. If not, either read up on it [here](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-putty-on-digitalocean-droplets-windows-users) or just proceed without an SSH key.

_Note: all subsequent steps assume that you are also using the weakest server possible with Ubuntu 14.04 x64\. If you chose different settings, the general instructions will still apply but some of the specific commands/URLs might need to change._

Even though you probably don't need it, here's a short GIF showing me creating a new droplet: [![Create droplet](http://deanattali.com/img/blog/digital-ocean/do-create.gif)](http://deanattali.com/img/blog/digital-ocean/do-create.gif)

Once the droplet is ready (can take a few minutes), you'll be redirected to a page that shows you information about the new droplet, including its IP. From now on, I'll use the random IP address `123.456.1.2` for the rest of this post, but remember to always substitute your actual droplet's IP with this one.

One option to log into your droplet is through the "Access" tab on the page you were redirected to, but it's slow and ugly, so I prefer logging in on my own machine. If you're on a unix machine, you can just use `ssh 123.456.1.2`. I'm on Windows, so I use [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) to SSH ("login") into other machines. Use the IP that you see on the page, with the username `root`. If you used an SSH key then you don't need to provide a password; otherwise, a password was sent to your email.

You should be greeted with a welcome message and some stats about the server that look like this: [![Login screen](http://deanattali.com/img/blog/digital-ocean/login.png)](http://deanattali.com/img/blog/digital-ocean/login.png)

## Step 4: Ensure you don't shoot yourself in the foot

The first thing I like to do is add a non-root user so that we won't accidentally do something stupid as "root". Let's add a user named "dean" and give him admin power. You will be asked to give some information for this new user.

```
adduser dean
gpasswd -a dean sudo
```

From now on I will generally log into this server as "dean" instead of "root". If I'll need to run any commands requiring admin abilities, I just have to prepend the command with `sudo`. Let's say goodbye to "root" and switch to "dean".

```
su - dean
```

## Step 5: See your droplet in a browser

Right now if you try to visit `http://123.456.1.2` in a browser, you'll get a "webpage not available error". Let's make our private server serve a webpage there instead, as a nice visual reward for getting this far. Install nginx:

```
sudo apt-get update
sudo apt-get -y install nginx
```

Now if you visit `http://123.456.1.2`, you should see a welcome message to nginx. Instant gratification!

### Quick nginx references

The default file that is served is located at `/usr/share/nginx/html/index.html`, so if you want to change what that webpage is showing, just edit that file with `sudo vim /usr/share/nginx/html/index.html`. For example, I just put a bit of text redirecting to other places [in my index page](http://daattali.com/). The configuration file is located at `/etc/nginx/nginx.conf`.

When you edit an HTML file, you will be able to see the changes immediately when you refresh the page, but if you make configuration changes, you need to restart nginx. In the future, you can stop/start/restart nginx with

```
sudo service nginx stop
sudo service nginx start
sudo service nginx restart
```

## Step 6: Install R

To ensure we get the most recent version of R, we need to first add `trusty` to our `sources.list`:

```
sudo sh -c 'echo "deb http://cran.rstudio.com/bin/linux/ubuntu trusty/" >> /etc/apt/sources.list'
```

Now add the public keys:

```
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9
gpg -a --export E084DAB9 | sudo apt-key add -
```

Now we're ready to install R

```
sudo apt-get update
sudo apt-get -y install r-base
```

You should now be able to run R and hopefully be greeted with a message containing the latest R version.

```
R
```

[![R welcome](http://deanattali.com/img/blog/digital-ocean/R-welcome.png)](http://deanattali.com/img/blog/digital-ocean/R-welcome.png)

Now you need to quit R (`quit()`) because there are a couple small things to adjust on the server so that R will work well.

If you also chose the weakest machine type like I did, many packages won't be able to install because of not enough memory. We need to add 1G of swap space:

```
sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
sudo /sbin/mkswap /var/swap.1
sudo /sbin/swapon /var/swap.1
sudo sh -c 'echo "/var/swap.1 swap swap defaults 0 0 " >> /etc/fstab'
```

Now installing most packages will work, but before installing any package, I always like having `devtools` available so that I can install GitHub packages. `devtools` will currently not be able to get installed because it has a few dependencies. Let's install those dependencies:

```
sudo apt-get -y install libcurl4-gnutls-dev
sudo apt-get -y install libxml2-dev
sudo apt-get -y install libssl-dev
```

Ok, now we can start installing R packages, both from CRAN and from GitHub!

```
sudo su - -c "R -e \"install.packages('devtools', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"devtools::install_github('daattali/shinyjs')\""
```

Feel free to play around with R now.

## Step 6.1: Important note re: installing R packages

Note that instead of launching R and installing the packages from R, I'm doing it from the terminal with `sudo su - -c "R ..."`. Why? Because if you log into R and install packages, by default they will be installed in your personal library and will only be accessible to the current user (`dean` in this case). By running the command the way I do above, it installs the packages as the `root` user, which means the packages will be installed in a global library and will be available to all users.

As a demonstration, launch R (simply run `R`) and run `.libPaths()`. This will show you all the locations where R will search for a package, and the first one is where a new package will get installed. You'll probably notice that the first entry, where packages will get installed, is a path under the current user's home (for me it's `/home/dean/R/x86_64-pc-linux-gnu-library/3.2`). From now on, whenever you want to install a package for the whole system, you should either log in as `root` and intall the package, or use the above command. To install an R package for just one user, it's ok to proceed as normal and just install the package when you're logged in as the intended user.

## Step 7: Install RStudio Server

Great, R is working, but RStudio has become such an integral part of our lives that we can't do any R without it!

Let's install some pre-requisites:

```
sudo apt-get -y install libapparmor1 gdebi-core
```

Download the latest RStudio Server - consult [RStudio Downloads page](http://www.rstudio.com/products/rstudio/download-server/) to get the URL for the latest version. Then install the file you downloaded. These next two lines are using the latest version as of writing this post.

```
wget http://download2.rstudio.org/rstudio-server-0.98.1103-amd64.deb
sudo gdebi rstudio-server-0.98.1103-amd64.deb
```

Done! By default, RStudio uses port 8787, so to access RStudio go to `http://123.456.1.2:8787` and you should be greeted with an RStudio login page. (If you forgot what your droplet's IP is, you can find out by running `hostname -I`)

[![RStudio](http://deanattali.com/img/blog/digital-ocean/rstudio.png)](http://deanattali.com/img/blog/digital-ocean/rstudio.png)

You can log in to RStudio with any user/password that are available on the droplet. For example, I would log in with username `dean` and my password. If you want to let your friend Joe have access to your RStudio, you can create a new user for them with `adduser joe`.

Go ahead and play around in R a bit, to make sure it works fine. I usually like to try out a `ggplot2` function, to ensure that graphics are working properly.

## Step 8: Install Shiny Server

You can safely skip this step if you don't use `shiny` and aren't interested in being able to host Shiny apps yourself. **But** don't forget that Shiny Server can also be used to host Rmarkdown files, not just shiny apps. This means that even if you don't develop shiny apps you might still have a use for Shiny Server if you want to host interactive Rmarkdown documents.

To install Shiny Server, first install the `shiny` package:

```
sudo su - -c "R -e \"install.packages('shiny', repos='http://cran.rstudio.com/')\""
```

(Note again that we're installing `shiny` in a way that will make it available to all users, as I explained [above](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#user-libraries)).

Just like when we installed RStudio, again we need to get the URL of the latest Shiny Server [from the Shiny Server downloads page](http://www.rstudio.com/products/shiny/download-server/), download the file, and then install it. These are the two commands using the version that is most up-to-date right now:

```
wget http://download3.rstudio.org/ubuntu-12.04/x86_64/shiny-server-1.3.0.403-amd64.deb
sudo gdebi shiny-server-1.3.0.403-amd64.deb
```

Shiny Server is now installed and running. Assuming there were no problems, if you go to `http://123.456.1.2:3838/` you should see Shiny Server's default homepage, which includes some instructions and two Shiny apps:

[![Shiny Server](http://deanattali.com/img/blog/digital-ocean/shiny.png)](http://deanattali.com/img/blog/digital-ocean/shiny.png)

If you see an error on the bottom Shiny app, it's probably because you don't have the `rmarkdown` R package installed (the instructions on the default Shiny Server page mention this). After installing `rmarkdown` in R, the bottom Shiny app should work as well. Don't forget to install `rmarkdown` so that it will be available to all users as described [above](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#user-libraries). I suggest you read through the instructions page at `http://123.456.1.2:3838/`.

### Quick Shiny Server references:

- Shiny Server log is at `/var/log/shiny-server.log`.
- The default Shiny Server homepage you're seeing is located at `/srv/shiny-server/index.html` - you can edit it or remove it.
- Any Shiny app directory that you place under `/srv/shiny-server/` will be served as a Shiny app. For example, there is a default app at `/srv/shiny-server/sample-apps/hello/`, which means you can run the app by going to `http://123.456.1.2:3838/sample-apps/hello/`.
- The config file for Shiny Server is at `/etc/shiny-server/shiny-server.conf`.
- To reload the server after editing the config, use `sudo reload shiny-server`.
- When hosting an Rmarkdown file, name the file `index.rmd` and add `runtime: shiny` to the document's frontmatter.

**Important!** If you look in the config file, you will see that by default, apps are ran as user "shiny". It's important to understand which user is running an app because things like file permissions and personal R libraries will be different for each user and it might cause you some headaches until you realize it's because the app should not be run as "shiny". Just keep that in mind.

The fact that apps run as the user `shiny` means that any package required in a shiny app needs to be either in the global library or in `shiny`'s library. [As I mentioned above](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#user-libraries), you might need to install R packages in a special way to make sure the `shiny` user can access them.

## Step 8.1: Set up proper user permissions on Shiny Server

As I just mentioned, dealing with which user is running an app and user permissions can be a bit annoying. It took me a while to figure our how to set up the users in a way that works well for me, and this is the setup I came up with. I'm not saying it's necessarily the most correct way to work, but it works for me. Feel free to do the same.

Currently, assuming you're logged in as user `dean`, when you create a folder in the Shiny Server folder, `dean` will be considered the only owner of that folder and nobody else (except `root`) will be able to write files there. Why is this a problem? It means that if an app is trying to write a file to the filesystem, the app will crash because `shiny` user does not have those permissions. One option is to add `run_as dean;` to the shiny server config, but I don't like that because then when I want to see which user is using up the server's resources, I won't be able to differentiate between a shiny app running under my name vs me running R code myself.

So my solution is to create a user group called `shiny-apps` and add both `dean` and `shiny` users to it. Then I'll make sure that the whole `/srv/shiny-server` folder has read+write permissions to the group. (Again, disclaimer: I'm not a sysadmin so I'm learning all this as I go. Don't treat this as a work of god).

```
sudo groupadd shiny-apps
sudo usermod -aG shiny-apps dean
sudo usermod -aG shiny-apps shiny
cd /srv/shiny-server
sudo chown -R dean:shiny-apps .
sudo chmod g+w .
sudo chmod g+s .
```

Now both `dean` and `shiny` will have access to any new or existing files under `/srv/shiny-server`. I like it because now I can develop an app from my RStuio Server (logged in as `dean`), be able to run it through RStudio (as `dean`), and also be able to run it via my Shiny Server (as `shiny`).

## Step 8.2: Populate Shiny Server with Shiny apps using a git repository

As mentioned above, any Shiny app you place under `/srv/shiny-server/` will be automatically served as a Shiny app. But how do you get shiny apps into there in the first place? If you're creating a new app, you can just log into your RStudio Server and develop your shiny app from there, and just save the app under `/srv/shiny-server/`.

But what if you want to bring into your server an app you already have on your machine? One option is to directly transfer files using something like [FileZilla](https://filezilla-project.org/) or the `scp` command. The moment a shiny app directory is transferred to your droplet, the corresponding app will be available to use on the web right away. Another approach is to use git. If you don't know what git is, that's outside the scope of this article, so either look it up and come back here or just use FileZilla :)

The main idea is to have the `/srv/shiny-server/` folder be a git repository, so that you can push to this repository from your personal computer and whenever you do a `git pull` on your droplet, it will update and grab the new apps you added. That's how I set up my own shiny server, you can take a look at [my shiny server on GitHub](https://github.com/daattali/shiny-server) and fork it if you'd like a good starting point.

The first step is to install git and make the `/srv/shiny-server/` directory a git repository.

```
sudo apt-get -y install git
cd /srv/shiny-server
git init
```

Next we will create a GitHub repository, so go to [GitHub](https://github.com/) and add a new repository named `shiny-server`.

[![Create new repository](http://deanattali.com/img/blog/digital-ocean/git-repo-create.png)](http://deanattali.com/img/blog/digital-ocean/git-repo-create.png)

Now we need to grab the URL of the repository from GitHub, so on the new page you were redirected to, click on "HTTPS" and then copy the URL to its right, as shown in the image below:

[![Get git repo URL](http://deanattali.com/img/blog/digital-ocean/git-repo-url.png)](http://deanattali.com/img/blog/digital-ocean/git-repo-url.png)

Now we need to make the connection between the git repository we made on our droplet and the one we just created, and then add all the files that are currently in `/srv/shiny-server/` to this repository. **Be sure to replace the URL in the first command with the URL that you copied from your repository**.

```
git remote add origin git@github.com:daattali/shiny-server.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

If you now refresh the GitHub page, you should see the files that were added from the droplet.

Now that git is set up, you can add shiny apps to this repository (assuming you know basic git usage). Whenever you add a new shiny app or edit the index page or an existing app, you'll need to do a `git pull` on your droplet to grab those changes and display them in your server. That's it, it's pretty convenient in my opinion.

As mentioned previously, Shiny Server can also be used as a great tool to host interactive Rmarkdown documents (not just shiny apps), so you can use this method to publish your rmarkdown files.

## Step 9: Make pretty URLs for RStudio Server and Shiny Server

This is optional and a little more advanced. You might have noticed that to access both RStudio and Shiny Server, you have to remember weird port numbers (`:8787` and `:3838`). Not only is it hard and ugly to remember, but some workplace environments often block access to those ports, which means that many people/places won't be able to access these pages. The solution is to use a reverse proxy, so that nginx will listen on port 80 (default HTTP port) at the URL `/shiny` and will _internally_ redirect that to port 3838\. Same for RStudio - we can have nginx listen at `/rstudio` and redirect it to port 8787\. This is why my Shiny apps can be reached at [daattali.com/shiny/](http://daattali.com/shiny/) which is an easy URL to type, but also at [daattali.com:3838](http://daattali.com:3838).

You need to edit the nginx config file `/etc/nginx/sites-enabled/default`:

```
sudo vim /etc/nginx/sites-enabled/default
```

(I'm assuming you know how to use `vim`. If not, then just Google for "how to edit a file on linux". In short: press `I` to start typing text, then press `Esc` to stop typing text, then press `:wq` followed by Enter to save the file).

Add the following lines right after the line that reads `server_name localhost;`:

```
location /shiny/ {
 proxy_pass http://127.0.0.1:3838/;
}

location /rstudio/ {
 proxy_pass http://127.0.0.1:8787/;
}
```

Since we changed the nginx config, we need to restart nginx for it to take effect.

```
sudo service nginx restart
```

Now you should be able to go to `http://123.456.1.2/shiny/` or `http://123.456.1.2/rstudio/`. Much better!

**Bonus for advanced users:** The above setup should be just fine for most users, but I did notice a few small issues with RStudio that seem to be fixed by allowing nginx to proxy WebSockets. For example, I noticed that when using the `ggvis` package in my RStudio, tooltips were not working. The fix is to add the following three lines inside the `location /rstudio/` settings (keep the `proxy_pass` line and just add these three, and remember you have to restart nginx after changing the settings):

```
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

If you're hosting a Shiny Server, add these 3 lines after the `proxy_pass` line inside `location /shiny/` as well.

**Make your Shiny apps work with and without trailing slashes:** If you have a Shiny app called "myapp", then you would have to go to the URL `http://123.456.1.2/shiny/myapp/` to see it. But if you omit the trailing slash (`http://123.456.1.2/shiny/myapp`), it will not work. I figured out a way to solve this, but again, keep in mind that I'm an nginx noob so it might not be a _good_ solution. I added the following line inside `location /shiny/` (just after the previous 3 lines from the previous paragraph):

```
rewrite ^(/shiny/[^/]+)$ $1/ permanent;
```

## Step 10: Custom domain name

If you have a custom domain that you want to host your droplet on, that's not too hard to set up. For example, my main droplet's IP is [198.199.117.12](http://198.199.117.12), but I also purchased the domain [daattali.com](http://daattali.com/) so that it would be able to host my droplet with a much simpler URL.

There are two main steps involved: you need to configure your domain on DO, and to change your domain servers from your registrar to point to DO.

### Configure your domain

In the DO website, click on "DNS" at the top, and then we want to add a domain. Select your droplet from the appropriate input box, and put in your domain name in the URL field. Do not add "www" to the beginning of your domain. Then click on "Create Domain".

You will now get to a page where you can enter more details.

- The "A" row should have `@` in the first box and the droplet's IP in the second box.
- In the three "NS" fields, you should have the values `ns1.digitalocean.com.`, `ns2.digitalocean.com.`, `ns3.digitalocean.com.`
- You also need to add a "CNAME" record, so click on "Add Record", choose "CNAME", enter `www` in the first box, and your domain's name in the second box. You need to append a dot (`.`) to the end of the domain name.

Here is what my domain settings look like, make sure yours look similar (note the dot suffix on all the domain names):

[![DigitalOcean DNS settings](http://deanattali.com/img/blog/digital-ocean/DigitalOceanDNS.png)](http://deanattali.com/img/blog/digital-ocean/DigitalOceanDNS.png)

### Change your domain servers to DigitalOcean

You also need to configure your domain registrar by adding the 3 nameservers `ns1.digitalocean.com`, `ns2.digitalocean.com`, `ns3.digitalocean.com`. It's fairly simple, but the exact instructions are different based on your registrar, so [here is a guide](https://www.digitalocean.com/community/tutorials/how-to-point-to-digitalocean-nameservers-from-common-domain-registrars) with all the common registrars and how to do this step with each of them.

I use Namecheap, so this is what my domain configuration needs to look like: [![Namecheap domain servers](http://deanattali.com/img/blog/digital-ocean/namecheap-domain-server.png)](http://deanattali.com/img/blog/digital-ocean/namecheap-domain-server.png)

**And that's it! Now you have a nicely configured private web server with your very own RStudio and Shiny Server, and you can do anything else you'd like on it.**

**[2015-05-11]** I've gotten several people asking me if this can be a solution for hosting rmarkdown files as well. **YES it can!** Shiny Server works great as hosting .Rmd files, and you can even embed a Shiny app inside the Rmd file. [Here's an example on my server](http://daattali.com/shiny/rbloggers-twitter/) in case you're curious.

**[2015-05-11]** There is some inquiry about whether or not this setup should be "Dockerized" ([What is Docker?](http://www.docker.com/whatisdocker/)). Docker is of course a great alternative to setting this up and can be even simpler by taking away all the pain of doing the setup yourself and providing you with a container that already has RStudio/Shiny Server installed. [Dirk Eddelbuettel](https://twitter.com/eddelbuettel) and [Carl Boettiger](https://twitter.com/cboettig) already did a fantastic job of making some R-related docker containers, including RStudio and Shiny Server, so [check out Rocker](https://registry.hub.docker.com/repos/rocker/) if you want to go that route. I think it's nice to do all this installation yourself because it can look scary and intimidating before you do it the first time, and it can be a nice feeling to see that it's actually very doable and really doesn't take very long (less than half an hour) if you have a guide that takes away the annoying Googling at every step. But you can quickly surpass all these steps and use docker if you prefer :)

**[2015-05-12]** Added missing dependencies to install devtools and [Important note re: installing R packages](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#user-libraries).

**[2015-05-13]** Added section about [populating Shiny Server with Shiny apps using a git repository](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#shiny-git).

**[2015-05-13]** Added a bit in [step 9](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#reverse-proxy) about proxying WebSockets to fix a few small potential issues with RStudio Server.

**[2015-05-14]** Added section about [setting up proper user permissions on Shiny Server](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#shiny-user-perms).

**[2015-05-18]** As mentioned in the comments, students with an educational GitHub account can get $100 credits through the GitHub Student Developer Pack.

**[2015-09-01]** Matthew Lincoln posted a script in the commends section that runs all the commands in this tutorial automatically when a DigitalOcean droplet is initialized. His exact comment: "I've tried my hand at getting most of this scripted into a cloud-config file that you can paste into the 'User Data' section when starting up a DO droplet... It doesn't handle anything like custom user options (yet!), but I've found it a great way to automate a fair number of these steps." I myself haven't tried his script, but you can check it out [here](https://gist.github.com/mdlincoln/1f40f4e88ce32c55b5f3), and I also found a DigitalOcean tutorial about clour-config and User Data [here](https://www.digitalocean.com/community/tutorials/an-introduction-to-cloud-config-scripting). Again, I haven't tried it myself so if you have any questions, please direct it at Matthew :)

**[2015-12-05]** Added a small paragraph to [step 9](http://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/#reverse-proxy) for fixing Shiny app URLs that don't have a trailing slash.

## Resources

This is a list of the main blog/StackOverflow/random posts I had to consult while getting all this to work.

## Disclaimer

I'm not a sysadmin and a lot of this stuff was learned very quickly from random Googling, so it's very possible that some steps here are not the very best way of performing some tasks. If anyone has any comments on anything in this document, I'd love to [hear about it](http://deanattali.com/aboutme#contact)!
