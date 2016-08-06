---
title: "Series: How to create your own website based on Docker (Part 9 - Creating the nginx/Angular 2 web site Docker container)"
url: "http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part9-nginx-website-docker-image.html"
date_saved: "07/19/2016 08:05-18"
excerpt: "This is part 9 of the series:&#xA0;How to create your own website based on Docker. In the last two parts we've created the whole backend (REST API&#xA0;+ database), so it's about time to create the&hellip;"
thumbnail: [![](png/series_how_to_create_your_own_website_based_on_docker_part_9__creating_the_nginxangular_2_web_site_docker_container.png)](png/full/series_how_to_create_your_own_website_based_on_docker_part_9__creating_the_nginxangular_2_web_site_docker_container.png)
---

# Series: How to create your own website based on Docker (Part 9 - Creating the nginx/Angular 2 web site Docker container)

[Original URL](http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part9-nginx-website-docker-image.html)

> This is part 9 of the series: How to create your own website based on Docker. In the last two parts we've created the whole backend (REST API + database), so it's about time to create the...

This is part 9 of the series: [How to create your own website based on Docker](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part1.html).

In the last two parts we've created the whole backend (REST API + database), so it's about time to create the website that makes use of it. Since we have a simple person REST API (see [part 8](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html)), we need a site that can list all persons as well as create new ones. Since Angular 2.0 has achieved "Developer Preview" status, this sounds like a perfect demo for [Angular 2.0](https://angular.io/)!

**A word of advice**: Angular 2.0 is not production ready yet! This little project is perfect for playing around with the latest version of Angular 2.0, but please do not start new projects with it, since a lot of new changes are going to be introduced until the framework is officially released. If you have ever played around with Angular 2.0 alpha, you probably don't want to use it for production anyways... It's still **very very unstable and the hassle with the typings makes me sad every time I use Angular 2.0**. But this will change some time soon and then we'll be able to work Angular like pros! :)

## Source code

<span>All files mentioned in this series are </span>

[available on Github](https://github.com/mastix/project-webdev-docker-demo)

<span>, so you can play around with it! :)</span>

<br>

<span>
  <br>
</span>

## Technologies to be used

Our website will use the following technologies (and to make sure that we have full support of everything, I recommend to use the latest greatest Chrome):

## 

## 

## 

## 

## 

## 

## First things first - creating the nginx image

Creating the nginx image is basically the same every time. Let's create a new directory called 

<span>/opt/docker/projectwebdev/</span>

 and within this new directory we'll create other directories called 

<span>config and</span>

 

<span>html</span>

 as well as our 

<span>Dockerfile</span>

:

> # mkdir -p /opt/docker/projectwebdev/config/<br>
> # mkdir -p /opt/docker/projectwebdev/html/<br>
> # > /opt/docker/projectwebdev/Dockerfile

After creating the fourth Dockerfile, you should now be able to understand what this file is doing, so I'm not going into details now - as a matter of fact, this is a pretty easy one.

> # Pull base image.<br>
> FROM docker_ubuntubase

> ENV DEBIAN_FRONTEND noninteractive

> # Install Nginx.<br>
> RUN \<br>
> add-apt-repository -y ppa:nginx/stable && \<br>
> apt-get update && \<br>
> apt-get install -y nginx && \<br>
> rm -rf /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_InRelease /var/lib/apt/lists/apt.dockerproject.org_repo_dists_ubuntu-xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_InRelease /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/deb.nodesource.com_node%5f6.x_dists_xenial_main_source_Sources /var/lib/apt/lists/lock /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-backports_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_restricted_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial_universe_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_InRelease /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_main_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_multiverse_source_Sources /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-amd64_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_binary-i386_Packages /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_i18n_Translation-en /var/lib/apt/lists/mirrors.digitalocean.com_ubuntu_dists_xenial-updates_universe_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-amd64_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_binary-i386_Packages /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_nginx_source_Sources /var/lib/apt/lists/nginx.org_packages_ubuntu_dists_xenial_Release /var/lib/apt/lists/partial /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_InRelease /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_main_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_multiverse_source_Sources /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-amd64_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_binary-i386_Packages /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_i18n_Translation-en /var/lib/apt/lists/security.ubuntu.com_ubuntu_dists_xenial-security_universe_source_Sources && \<br>
> chown -R www-data:www-data /var/lib/nginx

> # Define working directory.<br>
> WORKDIR /etc/nginx

> # Copy all config files<br>
> COPY ./config/default.conf /etc/nginx/conf.d/default.conf<br>
> COPY ./config/nginx.conf /etc/nginx/nginx.conf

> # Define default command.<br>
> CMD nginx

Source: <https://github.com/mastix/project-webdev-docker-demo/blob/master/projectwebdev/Dockerfile>

I've created the following config files that you have to copy to the 

<span>/opt/docker/projectwebdev/config/</span>

 folder - the Dockerfile will make sure that these files get copied into the image:

**default.conf:**

> ## Start www.project-webdev.com ##<br>
> server {<br>
> listen 8081;<br>
> server_name _;<br>
> access_log /var/log/nginx/project-webdev.access.log;<br>
> error_log /var/log/nginx/project-webdev.error.log;<br>
> root /var/www/html;<br>
> index index.html;<br>
> }<br>
> ## End www.project-webdev.com ##

**nginx.conf:**

> user www-data;<br>
> worker_processes auto;<br>
> pid /run/nginx.pid;<br>
> daemon off;

> events {<br>
> worker_connections 768;<br>
> }

> http {<br>
> ##<br>
> # Basic Settings<br>
> ##<br>
> sendfile on;<br>
> tcp_nopush on;<br>
> tcp_nodelay off;<br>
> keepalive_timeout 65;<br>
> types_hash_max_size 2048;<br>
> server_tokens off;<br>
> server_names_hash_bucket_size 64;<br>
> include /etc/nginx/mime.types;<br>
> default_type application/octet-stream;

> ##<br>
> # Logging Settings<br>
> ##<br>
> access_log /var/log/nginx/access.log;<br>
> error_log /var/log/nginx/error.log;

> ##<br>
> # Gzip Settings<br>
> ##<br>
> gzip on;<br>
> gzip_disable "msie6";<br>
> gzip_http_version 1.1;<br>
> gzip_proxied any;<br>
> gzip_min_length 500;<br>
> gzip_types text/plain text/xml text/css<br>
> text/comma-separated-values text/javascript<br>
> application/x-javascript application/atom+xml;

> ##<br>
> # Virtual Host Configs<br>
> ##<br>
> include /etc/nginx/conf.d/*.conf;<br>
> include /etc/nginx/sites-enabled/*;<br>
> }

These two files are the basic configuration for our frontend server and will make sure that it listens on port 8081 ([see architecture](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part4-docker-container-architecture.html): 4-digit-ports are not exposed to the web) and enables gzip. These files contain basic settings and should be adjusted to your personal preferences when creating your own files.

That's it... your nginx frontend server is ready to run!

## Let's create the frontend application

Since I don't want to re-invent the wheel I'm going to use a pretty cool [Angular 2.0 seed project](https://github.com/mgechev/angular2-seed) by [Minko Gechev](http://blog.mgechev.com/). This project uses [gulp](http://gulpjs.com/) (which I'm using for pretty much every frontend project) as build system. We're going to use this as bootstrapped application and will add our person-demo-specific code to it - for the sake of simplicity I've tried not to alter the original code/structure much.

Our demo will do the following:

- List all persons stored in our mongodb
- Create new persons and store them in in our mongodb

**Important:** This demo will not validate any input data nor does it implement any performance optimizations, it's just a **very very little** Angular 2.0 demo application that talks to our Docker REST container.

## Let's get started with the basic project setup

To get started, I've created a fork of the original Angular 2.0 seed repository and [you can find the complete application source code right here](https://github.com/mastix/angular2-seed)! So grab the source if you want to play around with it! :)

The gulp script in this project offers several tasks that will create the project for us by collecting all files, minifying them and preparing our HTML markup. The task we're going to use is 

<span>gulp build.prod</span>

 (build production, which will minify files). You can also use 

<span>gulp build.dev</span>

 (build development) if you want to be able to debug the generated JavaScript files in your browser. Whenever you run your build, all project-necessary generated files will be copied to 

<span>dist/prod/</span>

<span> - so the files in this directory represent the website that you need to copy to your docker host later - we’ll cover that later.</span>

Although I've said that I'm not going to alter the original code much, I've included Twitter Bootstrap via Bower - for those who don't know: Bower is a frontend dependency management framework. Usually you would call 

<span>bower install</span>

 to install all dependencies, but I added this call to the 

<span>package.json</span>

 file, so all you have to do is call 

<span>npm install</span>

 (which you have to do anyways when downloading my project from github), which will call 

<span>bower install</span>

 afterwards.

## 

## 

## The model

We've covered the basic project setup, so let's get started with the code.

Since we're using TypeScript, we can make use of types. So we're creating our own type, which represents our 

<span>Person</span>

and thanks to TypeScript and ES6, we can use a class for that. This model consists of the same properties that we've used for our mongoose schema in our REST API (

<span>id</span>

, 

<span>firstname</span>

, 

<span>lastname</span>

). For that I have created a 

<span>models</span>

 directory and within that directory I've added a file called 

<span>Person.ts</span>

 which contains the following code:

> export class Person {<br>
> private id:string;<br>
> private firstname:string;<br>
> private lastname:string;<br>
> constructor(theId:string, theFirstname:string, theLastname:string) {<br>
> this.id = theId;<br>
> this.firstname = theFirstname;<br>
> this.lastname = theLastname;<br>
> }<br>
> public getFirstName() {<br>
> return this.firstname;<br>
> }<br>
> public getLastName() {<br>
> return this.lastname;<br>
> }<br>
> public getId() {<br>
> return this.id;<br>
> }<br>
> }

Source: <https://github.com/mastix/person-demo-angular2-seed/blob/master/app/models/Person.ts>

## The service

No matter if you're working with AngularJS, jQuery, ReactJS or Angular 2.0 you always have to make sure that you outsource your logic into a service or any other detached component that can be replaced if something changes. In Angular 2.0 we don't have a concept of Factories, Services and Providers like in AngularJS - everything is a 

<span>@Component</span>

. So we're creating our 

<span>PersonService</span>

 class that allows us to read and store our data by firing XMLHttpRequests (XHR) to our REST API (api.project-webdev.com).

Since this service needs to work with our 

<span>Person</span>

 model, we need to import our model to our code. In TypeScript/ES6 we can use the import statement for that.

> import {Person} from '../models/Person';<br>
> export class PersonService {<br>
> getAllPersons() {<br>
> var personService = this;<br>
> return new Promise(function (resolve, reject) {<br>
> personService.getJSON('[http://api.yourdomain.com/person').then(function](http://api.yourdomain.com/person’).then(function) (retrievedPersons) {<br>
> if (!retrievedPersons || retrievedPersons.length == 0) {<br>
> reject("ERROR fetching persons...");<br>
> }<br>
> resolve(retrievedPersons.map((p)=>new Person(p.id, p.firstname, p.lastname)));<br>
> });<br>
> });<br>
> }<br>
> addPerson(thePerson:Person) {<br>
> this.postJSON('[http://api.yourdomain.com/person'](http://api.yourdomain.com/person’), thePerson).then((response)=>alert('Added person successfully! Click list to see all persons.'));<br>
> }<br>
> getJSON(url:string) {<br>
> return new Promise(function (resolve, reject) {<br>
> var xhr = new XMLHttpRequest();<br>
> xhr.open('GET', url);<br>
> xhr.onreadystatechange = handler;<br>
> xhr.responseType = 'json';<br>
> xhr.setRequestHeader('Accept', 'application/json');<br>
> xhr.send();<br>
> function handler() {<br>
> if (this.readyState === this.DONE) {<br>
> if (this.status === 200) {<br>
> resolve(this.response);<br>
> } else {<br>
> reject(new Error('getJSON: `' + url + '` failed with status: [' + this.status + ']'));<br>
> }<br>
> }<br>
> }<br>
> });<br>
> }<br>
> postJSON(url:string, person:Person) {<br>
> return new Promise(function (resolve, reject) {<br>
> var xhr = new XMLHttpRequest();<br>
> var params = `id=${person.getId()}&firstname=${person.getFirstName()}&lastname=${person.getLastName()}`;<br>
> xhr.open("POST", url, true);<br>
> xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");<br>
> xhr.onreadystatechange = handler;<br>
> xhr.responseType = 'json';<br>
> xhr.setRequestHeader('Accept', 'application/json');<br>
> xhr.send(params);<br>
> function handler() {<br>
> if (this.readyState === this.DONE) {<br>
> if (this.status === 201) {<br>
> resolve(this.response);<br>
> } else {<br>
> reject(new Error('getJSON: `' + url + '` failed with status: [' + this.status + ']'));<br>
> }<br>
> }<br>
> }<br>
> });<br>
> }<br>
> }

<span>Source: </span>

<span>
  <a href="https://github.com/mastix/person-demo-angular2-seed/blob/master/app/services/PersonService.ts">https://github.com/mastix/person-demo-angular2-seed/blob/master/app/services/PersonService.ts</a>
</span>

## The application

Since we can read and store persons now, it's about time to take care of the UI and the point where everything starts is the Angular 2.0 application itself. It creates the whole application by glueing logic and UI together. The file we're talking about here is the 

<span>app.ts</span>

.

> import {Component, View, bootstrap, NgFor} from 'angular2/angular2';<br>
> import {RouteConfig, RouterOutlet, RouterLink, routerInjectables} from 'angular2/router';<br>
> import {PersonList} from './components/personlist/personlist';<br>
> import {PersonAdd} from './components/personadd/personadd';<br>
> @Component({<br>
> selector: 'app'<br>
> })<br>
> @RouteConfig([<br>
> {path: '/', component: PersonList, as: 'personlist'},<br>
> {path: '/personadd', component: PersonAdd, as: 'personadd'}<br>
> ])<br>
> @View({<br>
> templateUrl: './app.html?v=<%= VERSION %>',<br>
> directives: [RouterOutlet, RouterLink]<br>
> })<br>
> class App {<br>
> }<br>
> bootstrap(App, [routerInjectables]);

## <span>Source: </span>

<span>
  <span>
  <a href="https://github.com/mastix/person-demo-angular2-seed/blob/master/app/app.ts">https://github.com/mastix/person-demo-angular2-seed/blob/master/app/app.ts</a>
</span>
</span>

We need four different imports in our application:

- Everything that is needed in order to render the application correctly
- Everything that is needed in order to route our links correctly
- Our page that allows us to list all stored persons
- Out page that allows us to create a new person

Let's have a look at some snippets:

> @Component({<br>
> selector: 'app'<br>
> })

You can think of Angular apps as a tree of components. This root component we've been talking about acts as the top level container for the rest of your application. The root component's job is to give a location in the 

<span>index.html</span>

 file where your application will render through its element, in this case 

<span>&lt;app&gt;</span>

. There is also nothing special about this element name; you can pick it as you like. The root component loads the initial template for the application that will load other components to perform whatever functions your application needs - menu bars, views, forms, etc.

> @RouteConfig([<br>
> {path: '/', component: PersonList, as: 'personlist'},<br>
> {path: '/personadd', component: PersonAdd, as: 'personadd'}<br>
> ])

Our demo application will have two links. One that loads a page which lists all stored persons and another one that allows to create a new one. So we need two routes in this case. Each route is directly linked to components, which we'll cover in a sec.

> @View({<br>
> templateUrl: './app.html?v=<%= VERSION %>',<br>
> directives: [RouterOutlet, RouterLink]<br>
> })

The 

<span>@View</span>

 annotation defines the HTML that represents the component. The component I've developed uses an external template, so it specifies a 

<span>templateUrl</span>

 property including the path to the HTML file. Since we need to iterate over our stored persons, we need to inject the 

<span>ng-For/For</span>

 directive that we have imported. You can only use directives in your markup if they are specified here. Just skip the 

<span>&lt;%= VERSION %&gt;</span>

 portion, as this is part of the original Angular 2.0 Seed project and not changed in our application.

> bootstrap(App, [routerInjectables]);

At the bottom of our 

<span>app.ts</span>

, we call the 

<span>bootstrap()</span>

 function to load your new component into its page. The bootstrap() function takes a component and our injectables as a parameter, enabling the component (as well as any child components it contains) to render.

## The index.html

The 

<span>index.html</span>

 represents the outline which will be filled with our components later. This is a pretty basic html file, that uses the aforementioned 

<span>&lt;app&gt;</span>

 tag (see our 

<span>app.ts</span>

 above) - you might also want to use a name like 

<span>&lt;person-app&gt;</span>

 or something, but then you need to adjust your 

<span>app.ts</span>

. This is the hook point for our application.

> <!DOCTYPE html><br>
> <head><br>
> [...]<br>
> </head><br>
> <body><br>
> [...]<br>
> <div class="jumbotron"><br>
> <div class="container"><br>
> <h1>Person Demo</h1><br>
> <p>This is just a little demonstration about how to use Angular 2.0 to interact with a REST API that we have<br>
> created in the following series: <a<br>
> href="[http://project-webdev.blogspot.com/2015/05/create-site-based-on-docker-part1.html"](http://project-webdev.blogspot.com/2015/05/create-site-based-on-docker-part1.html”)<br>
> target="_blank">Series: How to create your own website based on Docker </a><br>
> </div><br>
> </div><br>
> <div class="container"><br>
> **<app>Loading...</app>**<br>
> [...]<br>
> </div><br>
> <!– inject:js –><br>
> <!– endinject –><br>
> <script src="./init.js?v=<%= VERSION %>"></script><br>
> </body><br>
> </html>

That's it... we've created our base application. Now everything we'll create will be rendered in the 

<span>&lt;app&gt;</span>

 portion of the page.

## Creating the person list

Since encapsulation is very important in huge deployments, we're adding all self-contained components into the following folder: 

<span>
  <a href="https://github.com/mastix/person-demo-angular2-seed/tree/master/app/components">/app/components/</a>
</span>

<span> so in terms of the person list, this is going to be a folder called </span>

<span>
  <a href="https://github.com/mastix/person-demo-angular2-seed/tree/master/app/components/personlist">/app/components/personlist/</a>
</span>

<span>.</span>

Each component consists of

- the component code itself
- the template to use

As mentioned before, everything in Angular 2.0 is a component and so the structure of our 

<span>personlist.ts</span>

 pretty much looks like the 

<span>app.ts</span>

.

> import {Component, View,NgFor} from 'angular2/angular2';<br>
> // import the person list, which represents the array that contains all persons.<br>
> import {PersonService} from '../../services/PersonService';<br>
> //import our person model that represents our person from the REST service.<br>
> import {Person} from '../../models/Person';<br>
> @Component({<br>
> selector: 'personlist',<br>
> appInjector: [PersonService]<br>
> })<br>
> @View({<br>
> templateUrl: './components/personlist/personlist.html?v=<%= VERSION %>',<br>
> directives: [NgFor]<br>
> })<br>
> export class PersonList {<br>
> personArray:Array<string>;<br>
> constructor(ps:PersonService) {<br>
> ps.getAllPersons().then((array)=> {<br>
> this.personArray = array;<br>
> });<br>
> }<br>
> }

Source: <https://github.com/mastix/person-demo-angular2-seed/blob/master/app/components/personlist/personlist.ts>

As you can see, we're importing the following:

- Standard Angular Components to be render the page (NgFor Directive is used to iterate through our list later)
- Our Person service
- Our Person model

We need to inject our 

<span>PersonService</span>

 into our component and the 

<span>NgFor</span>

 directive into our View, so we can use them later (e.g. see <https://github.com/mastix/person-demo-angular2-seed/blob/master/app/components/personlist/personlist.html>).

The real logic happens in the 

<span>PersonList</span>

 class itself - ok, there's not much here... but it's important. The constructor of this class uses the 

<span>PersonService</span>

 to fetch all Persons (the service will then fire a request to our API to fetch the list of persons) and to store them in an array. This array will then be accessible in the view, so we can iterate over it.

> <table class="table table-striped"><br>
> <tr><br>
> <th>ID</th><br>
> <th>FIRST NAME</th><br>
> <th>LAST NAME</th><br>
> </tr><br>
> <tr ***ng-for="#person of personArray"**><br>
> <td>**{{person.id}}**</td><br>
> <td>**{{person.firstname}}**</td><br>
> <td>**{{person.lastname}}**</td><br>
> </tr><br>
> </table>

Source: <https://github.com/mastix/person-demo-angular2-seed/blob/master/app/components/personlist/personlist.html>

We're using a table to represent the list of persons. So the only thing we need to do is to iterate over the 

<span>personArray</span>

 that we have created in our 

<span>PersonList</span>

 component. In every iteration we're creating a row (tr) with 3 fields (td) that contains the person's id, first name and last name.

## Creating the person add page

Ok, since we can now list all persons, let's add the possibility to create a new one. We're following the same pattern here and create a 

<span>personadd</span>

 component (

<span>
  <a href="https://github.com/mastix/person-demo-angular2-seed/tree/master/app/components/personadd">/app/components/personadd</a>
</span>

) that consists of some logic and a view as well.

> import {Component, View, NgFor} from 'angular2/angular2';<br>
> // import the person list, which represents the array that contains all persons.<br>
> import {PersonService} from '../../services/PersonService';<br>
> //import our person model that represents our person from the REST service.<br>
> import {Person} from '../../models/Person';<br>
> @Component({<br>
> selector: 'personadd',<br>
> appInjector: [PersonService]<br>
> })<br>
> @View({<br>
> templateUrl: './components/personadd/personadd.html?v=<%= VERSION %>',<br>
> })<br>
> export class PersonAdd {<br>
> addPerson(theId, theFirstName, theLastName) {<br>
> new PersonService().addPerson(new Person(theId, theFirstName, theLastName));<br>
> }<br>
> }

Source: <https://github.com/mastix/person-demo-angular2-seed/blob/master/app/components/personadd/personadd.ts>

I'm not going to cover the annotations here, since they follow pretty much the same pattern like the 

<span>PersonList</span>

. But what's important here is that the 

<span>PersonAdd</span>

 class offers a property/method/function called 

<span>addPerson()</span>

, which takes three parameters: 

<span>id</span>

, 

<span>firstname</span>

, 

<span>lastname</span>

. Based on these parameters, we can create our 

<span>Person</span>

 model and call our 

<span>PersonService</span>

 to store it on our server (in our mongodb Docker container via our ioJS REST Docker container).<br>
**** **Important:** Usually you would add some validation here, but for the sake of simplicity I've skipped that.

As mentioned before, everything that we specify in the class will be available in the View, so this method can later be called from the HTML markup.

> <form><br>
> <div class="form-group"><br>
> <label for="inputId">ID</label><br>
> <input **#id** type="number" class="form-control" id="inputId" placeholder="Enter ID"><br>
> </div><br>
> <div class="form-group"><br>
> <label for="inputFirstName">First name</label><br>
> <input **#firstname** type="text" class="form-control" id="inputFirstName" placeholder="First name"><br>
> </div><br>
> <div class="form-group"><br>
> <label for="inputLastName">First name</label><br>
> <input **#lastname** type="text" class="form-control" id="inputLastName" placeholder="Last name"><br>
> </div><br>
> </form><br>
> <button class="btn btn-success" **(click)="addPerson(id.value, firstname.value, lastname.value)**">Add Person</button>

Source: <https://github.com/mastix/person-demo-angular2-seed/blob/master/app/components/personadd/personadd.html>

I could have used 

<span>angular2/forms</span>

 here, but believe me, it is not ready to work with... I've struggled so much that I've decided to skip it (e.g. I'd have to update my type definitions and so on...). But what's really important here is that we can call our 

<span>addPerson()</span>

 method from our 

<span>PersonAdd</span>

 component and pass the values from our fields. Pretty easy, right?

Now we can build our project by running 

<span>gulp build.prod</span>

 and copy the contents of the newly created 

<span>dist/prod/</span>

 folder to our docker host. Remember: In our docker compose file we've specified that our 

<span>/opt/docker/projectwebdev/html</span>

 folder will be mounted in our container (as 

<span>/var/www/html</span>

). So we can easily update our HTML files and the changes will be reflected on our website on-the-fly.

So when you've copied all files, the directory structure should look like that:

> ├── config<br>
> │ ├── default.conf<br>
> │ └── nginx.conf<br>
> ├── Dockerfile<br>
> └── html<br>
> ├── app.html<br>
> ├── app.js<br>
> ├── bootstrap<br>
> │ └── dist<br>
> │ └── css<br>
> │ └── bootstrap.min.css<br>
> ├── components<br>
> │ ├── personadd<br>
> │ │ └── personadd.html<br>
> │ └── personlist<br>
> │ └── personlist.html<br>
> ├── index.html<br>
> ├── init.js<br>
> ├── lib<br>
> │ └── lib.js<br>
> └── robots.txt

## Here is what it looks like later

### 

### Adding a new person

[![](http://2.bp.blogspot.com/-khetjx9wEOg/VXqQKAtrXSI/AAAAAAAAslg/gwz5Qi8AIDI/s320/2015-06-12_09h51_42.png)](http://2.bp.blogspot.com/-khetjx9wEOg/VXqQKAtrXSI/AAAAAAAAslg/gwz5Qi8AIDI/s1600/2015-06-12_09h51_42.png)

### Listing all persons

[![](http://4.bp.blogspot.com/-o-Tz1COuy5s/VXqQKNHnVWI/AAAAAAAAslc/fMQTwEkAtpU/s400/2015-06-12_09h52_05.png)](http://4.bp.blogspot.com/-o-Tz1COuy5s/VXqQKNHnVWI/AAAAAAAAslc/fMQTwEkAtpU/s1600/2015-06-12_09h52_05.png)

That's it... we have the [backend](http://project-webdev.blogspot.de/2015/05/create-site-based-on-docker-part8-iojs-rest-api-docker-image.html) and the frontend now... [it's about time to create our nginx reverse proxy to make them all accessible](http://project-webdev.blogspot.de/2015/06/create-site-based-on-docker-part10-nginx-reverse-proxy-docker-image.html)!
