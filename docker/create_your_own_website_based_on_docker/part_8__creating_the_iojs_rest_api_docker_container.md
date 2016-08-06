---
title: "Series: How to create your own website based on Docker (Part 8 - Creating the ioJS REST API Docker container)"
url: "http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html"
date_saved: "07/19/2016 08:05-12"
excerpt: "This is part 8 of the series:&#xA0;How to create your own website based on Docker. In the last part of the series, we have created our "dockerized" mongodb noSQL database server to read our persisted&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_8__creating_the_iojs_rest_api_docker_container.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_8__creating_the_iojs_rest_api_docker_container.png)
---

# Series: How to create your own website based on Docker (Part 8 - Creating the ioJS REST API Docker container)

[Original URL](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html)

> This is part 8 of the series: How to create your own website based on Docker. In the last part of the series, we have created our "dockerized" mongodb noSQL database server to read our persisted...

[![](http://1.bp.blogspot.com/-allUO2V_TtY/VVoITWanIdI/AAAAAAAAr2w/oXYD-S7pAMY/s200/420px-Io.js_logo.png)](http://1.bp.blogspot.com/-allUO2V_TtY/VVoITWanIdI/AAAAAAAAr2w/oXYD-S7pAMY/s1600/420px-Io.js_logo.png) This is part 8 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

In the last part of the series, we have created our "dockerized" mongodb noSQL database server to read our persisted entries from and based on our architecture we have decided, that only the REST API (which will be based on [ioJS](http://iojs.org/)) is allowed to talk to our database container.

So now it's about time to create the actual REST API that can be called via our nginx reverse proxy (using 

<span>api.project-webdev.com</span>

) to read some person object entry from our database. We'll also create a very simple way to create a Person as well as list all available persons. As soon as you've understood how things work, you'll be able to implement more features of the REST API yourself - so consider this as pretty easy example.

## Source code

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

<br>

<span>
  <br>
</span>

## Technologies to be used

Our REST API will use the following technologies:

- [ioJS](http://iojs.org/) as JavaScript application server
- [hapiJS](http://hapijs.com/) as REST framework
- [mongoose](http://mongoosejs.com/) as mongoDB driver, to connect to our database container
- [pm2](https://github.com/Unitech/pm2) to run our nodejs application (and restart it if it crashes for some reason)

## 

## 

## 

## First things first - creating the ioJS image

Creating the ioJS image is basically the same every time. Let's create a new directory called 

<span>/opt/docker/projectwebdev-api/</span>

 and within this new directory we'll create another directory called 

<span>app</span>

 and our 

<span>Dockerfile</span>

:

> # mkdir -p /opt/docker/projectwebdev-api/app/<br>
> # > /opt/docker/projectwebdev-api/Dockerfile

The new Dockerfile is based on the [official ioJS Dockerfile](https://github.com/nodejs/docker-iojs), but I've added added some application/image specific information, so that we can implement our ioJS application:

- Added our ubuntu base image (we're not using debian wheezy like in the official image)
- Installed the latest NPM, PM2 and gulp (for later; we're not using gulp for this little demo)
- Added our working directories
- Added some clean up code
- Added PM2 as CMD (we'll talk about that soon)

So just create your 

<span>/opt/docker/projectwebdev-api/Dockerfile</span>

 with the following content:

> # Pull base image.<br>
> FROM docker_ubuntubase

> ENV DEBIAN_FRONTEND noninteractive

> RUN apt-get update<br>
> RUN apt-get update –fix-missing<br>
> RUN curl -sL <https://deb.nodesource.com/setup\_iojs\_2.x> | bash -

> RUN apt-get install -y iojs gcc make build-essential openssl make node-gyp<br>
> RUN npm install -g npm@latest<br>
> RUN npm install -g gulp<br>
> RUN npm install -g pm2@latest<br>
> RUN apt-get update –fix-missing

> RUN mkdir -p /var/log/pm2<br>
> RUN mkdir -p /var/www/html

> # Cleanup<br>
> RUN apt-get clean && rm -rf /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_InRelease /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_InRelease /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_source_Sources /var/lib/apt/lists/lock /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-amd64_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-i386_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_Release /var/lib/apt/lists/partial /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_InRelease /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_source_Sources /tmp/mongodb-27017.sock /tmp/pear /tmp/systemd-private-19c027e560a549b3ad3c6140875ea6c5-systemd-timesyncd.service-oD9o3a /var/tmp/*<br>
> RUN apt-get autoremove -y<br>
> RUN ln -s /usr/bin/nodejs /usr/local/bin/node

> WORKDIR /var/www/html

> CMD ["pm2", "start", "index.js","–name","projectwebdevapi","–log","/var/log/pm2/pm2.log","–watch","–no-daemon"]

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/projectwebdev-api/Dockerfile>

## 

## 

## Adding our REST API code to our container

Now let's create a simple application, that listens to a simple GET request and returns and entry from our mongoDB container. Just to proof that it works, I'll create a REST API that returns a simple Person object that contains an id as well as a first and a last name.

In order to get this object later, I'd have to call 

<span>
  <a href="http://api.projectwebdev.com/person/{id}">http://api.projectwebdev.com/person/{id}</a>
</span>

 and it will return that object in JSON format. We'll also add a router to return all persons as well as a route that allows to add a new person - but we'll cover that in a second.

Since PM2 will only start (and not build) our ioJS application, we have to make sure that NPM (packaged with ioJS or nodeJS) is installed on your server, so that you can build the project there.

So here is my simple flow:

- I create the ioJS application on my local machine
- Then I upload the files to my server
- On my server I use

  <span> npm install</span>

   to fetch all dependencies
- PM2 restart the application automatically if it detects changes

In a later blog posting I will explain how you can setup a Git Push-To-Deploy mechanism which will take care of this automatically, but for this simple application we're doing it manually.

To get started, I'll create a new directory on my local machine (which has ioJS installed) and create a basic application:

> # mkdir -p /home/mastixmc{development/projectwebdev-api && $_<br>
> # npm init<br>
> # npm install hapi mongoose –save

<span>npm init</span>

 will ask you a bunch of questions, and then write a package.json for you. It attempts to make reasonable guesses about what you want things to be set to, and then writes a 

<span>package.json</span>

 file with the options you've selected. (Info: Every nodeJS/ioJS application needs to have a 

<span>package.json</span>

 as descriptor)

<span>npm install hapi mongoose –save</span>

 will download/install [hapiJS](http://hapijs.com/) and [mongoose](http://mongoosejs.com/) and will save the dependency in our 

<span>package.json</span>

 file, so our server can download it later as well.

## Creating the application

In our new directory, we'll create a file called 

<span>index.js</span>

, with the following contents (we'll get into details afterwards):

> var hapi = require('hapi');<br>
> var mongoose = require('mongoose');<br>
> // connect to database<br>
> mongoose.connect('mongodb://'+process.env.MONGODB_1_PORT_3333_TCP_ADDR+':'+process.env.MONGODB_1_PORT_3333_TCP_PORT+'/persons', function (error) {<br>
> if (error) {<br>
> console.log("Connecting to the database failed!");<br>
> console.log(error);<br>
> }<br>
> });<br>
> // Mongoose Schema definition<br>
> var PersonSchema = new mongoose.Schema({<br>
> id: String,<br>
> firstName: String,<br>
> lastName: String<br>
> });<br>
> // Mongoose Model definition<br>
> var Person = mongoose.model('person', PersonSchema);<br>
> // Create a server with a host and port<br>
> var server = new hapi.Server();<br>
> server.connection({<br>
> port: 3000<br>
> });<br>
> // Add the route to get a person by id.<br>
> server.route({<br>
> method: 'GET',<br>
> path:'/person/{id}',<br>
> handler: PersonIdReplyHandler<br>
> });<br>
> // Add the route to get all persons.<br>
> server.route({<br>
> method: 'GET',<br>
> path:'/person',<br>
> handler: PersonReplyHandler<br>
> });<br>
> // Add the route to add a new person.<br>
> server.route({<br>
> method: 'POST',<br>
> path:'/person',<br>
> handler: PersonAddHandler<br>
> });<br>
> // Return all users in the database.<br>
> function PersonReplyHandler(request, reply){<br>
> Person.find({}, function (err, docs) {<br>
> reply(docs);<br>
> });<br>
> }<br>
> // Return a certain user based on its id.<br>
> function PersonIdReplyHandler(request, reply){<br>
> if (request.params.id) {<br>
> Person.find({ id: request.params.id }, function (err, docs) {<br>
> reply(docs);<br>
> });<br>
> }<br>
> }<br>
> // add new person to the database.<br>
> function PersonAddHandler(request, reply){<br>
> var newPerson = new Person();<br>
> newPerson.id = request.payload.id;<br>
> newPerson.lastName = request.payload.lastname;<br>
> newPerson.firstName = request.payload.firstname;<br>
> newPerson.save(function (err) {<br>
> if (!err) {<br>
> reply(newPerson).created('/person/' + newPerson.id); // HTTP 201<br>
> } else {<br>
> reply("ERROR SAVING NEW PERSON!!!"); // HTTP 403<br>
> }<br>
> });<br>
> }<br>
> // Start the server<br>
> server.start();

***Disclaimer:** Since this is just a little example, I hope you don't mind that I've put everything into on file - in a real project, I'd recommend to structure the project correctly, so that it scales in larger deployments - but for now, we're fine. Also, I did not add any error-checking or whatsoever to this code as it's just for demonstration purposes.*

Now I we can copy our index.js and package.json file to our server (

<span>/opt/docker/projectwebdev-api/app/</span>

), ssh into our server and run 

<span>npm install</span>

 within that directory. This will download all dependencies and create a 

<span>node_modules</span>

 folder for us. You'll have a fully deployed ioJS application on your Docker host now, which can be used by the 

<span>projectwebdev-api</span>

 container, since this directory is mounted into it.

## 

## 

## Explaining the REST-API code

So what does this file do? Pretty simple:

HapiJS creates a server that will listen on port 

<span>3000</span>

 - I've also added the following routes including their handlers:

- <span>GET</span>

   to 

  <span>/person</span>

  , which will then call a 

  <span>PersonReplyHandler</span>

   function, that uses Mongoose to fetch all persons stored in our database.
- <span>GET</span>

   to 

  <span>/person/{id}</span>

  , which will then call a 

  <span>PersonIdReplyHandler</span>

   function, that uses Mongoose to fetch a person with a certain id from our database.
- <span>POST</span>

   to 

  <span>/person</span>

  , which will then call a 

  <span>PersonAddHandler</span>

   function, that uses Mongoose to store a person in our database.

A Person consists of the following fields (we're using the Mongoose Schema here):

> // Mongoose Schema definition<br>
> var PersonSchema = new mongoose.Schema({<br>
> **id**: String,<br>
> **firstname**: String,<br>
> **lastname**: String<br>
> });

So the aforementioned handlers (e.g. PersonAddHandler) will make sure that this information is served or stored from/to the database.<br>
**** **Later, when you have set up your nginx reverse proxy**, you'll be able to use the following requests to 

<span>GET</span>

 or 

<span>POST</span>

 persons. But we'll get into that in the last part!

Add a new person:

> curl -X POST -H "Accept: application/json" -H "Content-Type: multipart/form-data" -F "id=999" -F "firstname=Sascha" -F "lastname=Sambale" <http://api.project-webdev.com/person>

Result:

> [{<br>
> "_id": "555c827959a2234601c5ddfa",<br>
> "firstName": "Sascha",<br>
> "lastName": "Sambale",<br>
> "id": "999",<br>
> "__v": 0<br>
> }]

Get all persons:

> curl -X GET -H "Accept: application/json" <http://api.project-webdev.com/person/>

Result:

> [{<br>
> _id: "555c81f559a2234601c5ddf9",<br>
> firstName: "John",<br>
> lastName: "Doe",<br>
> id: "15",<br>
> __v: 0<br>
> }, {<br>
> _id: "555c827959a2234601c5ddfa",<br>
> firstName: "Sascha",<br>
> lastName: "Sambale",<br>
> id: "999",<br>
> __v: 0<br>
> }]

Get a person with id 999:

> curl -X GET -H "Accept: application/json" <http://api.project-webdev.com/person/999>

Result:

> [{<br>
> "_id": "555c827959a2234601c5ddfa",<br>
> "firstName": "Sascha",<br>
> "lastName": "Sambale",<br>
> "id": "999",<br>
> "__v": 0<br>
> }]

You'll be able to do that as soon as you've reached the end of this series! ;)

## <span>Explaining the database code</span>

I guess the most important part of the database code is how we establish the connection to our mongodb container.

> // connect to database<br>
> mongoose.connect('mongodb://'+process.env.MONGODB_1_PORT_3333_TCP_ADDR+':'+process.env.MONGODB_1_PORT_3333_TCP_PORT+'/persons', function (error) {<br>
> if (error) {<br>
> console.log("Connecting to the database failed!");<br>
> console.log(error);<br>
> }<br>
> });

Since we're using container links, we can not know which ip our mongodb container will get when it gets started. So we have to use [environment variables that Docker provides us](https://docs.docker.com/userguide/dockerlinks/#environment-variables).

Docker uses this prefix format to define three distinct environment variables:

- The prefix_ADDR variable contains the IP Address from the URL, for example 

  <span>WEBDB_PORT_8080_TCP_ADDR</span>

  =172.17.0.82.
- The prefix_PORT variable contains just the port number from the URL for example 

  <span>WEBDB_PORT_8080_TCP_PORT</span>

  =8080.
- The prefix_PROTO variable contains just the protocol from the URL for example 

  <span>WEBDB_PORT_8080_TCP_PROTO</span>

  =tcp.

If the container exposes multiple ports, an environment variable set is defined for each one. This means, for example, if a container exposes 4 ports that Docker creates 12 environment variables, 3 for each port.

In our case the environment variables look like this:

- MONGODB_1_PORT_3333_TCP_ADDR
- MONGODB_1_PORT_3333_TCP_PORT
- MONGODB_1_PORT_3333_TCP_PROTO

Where 

<span>MONGODB</span>

 is the name and 

<span>PORT</span>

 is the port number we've specified in our docker-compose.yml file:

> **mongodb**:<br>
> build: ./mongodb<br>
> **expose:

> ```
>   - “3333”**  
> volumes:  
>     - ./logs/:/var/log/mongodb/  
>     - ./mongodb/db:/data/db
> ```

Docker Compose also creates environment variables with the name 

<span>DOCKER_MONGODB</span>

, which we are not going to use as it might happen that we switch from Docker Compose to something else in the future.

So Docker provides the environment variables and ioJS uses the 

<span>process.env</span>

 object to access them. We can therefore create a mongodb connection URL that looks like this:

> mongodb://172.17.0.82:3333/persons

... which will be the link to our Docker container that runs mongodb on port 

<span>3333</span>

... Connection established!

## 

## 

## Running ioJS in production mode

As mentioned before, in order to start (and automatically restart our REST API application, when we update the application files or the application crashes for some reason) we're using [PM2](https://github.com/Unitech/pm2), which will be configured via command line paramaters in our CMD instruction (see our Dockerfile):

> CMD ["pm2", "start", "index.js","–name","projectwebdevapi","–log","/var/log/pm2/pm2.log","–watch","–no-daemon"]

So what does this command do?

- <span>“pm2”, “start”, “index.js”</span>

   starts our application from within our 

  <span>WORKDIR</span>

   (

  <span>/var/www/html/</span>

  ).
- <span>“–name”,“projectwebdevapi”</span>

   names our application projectwebdevapi.
- <span>“–log”,"/var/log/pm2/pm2-project.log</span>

  " logs everything to

  <span> /var/log/pm2/pm2-project.log</span>

   (and since this is a mounted directory it will be stored on our docker host in 

  <span>/opt/docker/logs</span>

   - see our 

  <span>docker-compose.yml</span>

   file).
- <span>“–watch”</span>

   watches our 

  <span>WORKDIR</span>

   (

  <span>/var/www/html/</span>

  ) for changes and will restart the application if something has changed. So you'll be able to update the application on your docker host and the changes will be reflected on the live site automatically.
- <span>“–no-daemon”</span>

   runs PM2 in the foreground so the container does not exit and keeps running.

That's pretty much it - now, whenever you start your container later (in our case Docker Compose will start it), PM2 will start your application and will make sure that it keeps running.

[In the next part we'll create the frontend application that calls our new REST-API!](http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part9-nginx-website-docker-image.html)
