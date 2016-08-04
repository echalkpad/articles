# Beginner's Guide to Getting Started with Sails.js

[Original URL](https://www.codementor.io/nodejs/tutorial/how-to-setup-sailsjs-tutorial-beginners)

> Introduction Sails.js is node.js framework that allows you to build enterprise-ready, custom MVC ( model, view, controller ) application on-the-go. Sails.js has built-in features such as an API...

## Introduction

Sails.js is node.js framework that allows you to build enterprise-ready, custom MVC ( model, view, controller ) application on-the-go. Sails.js has built-in features such as an API creator, and its socket integration in every route and database ORM makes it very useful and helps speed up development.

## Sails.js features

Sails.js has lots of features which will help you to build custom, enterprise-ready applications. Some of them are as follows:

- Auto generated API
- Database ORM
- Inbuilt task runner
- Security code
- Built-in web sockets in routes.

Unlike MEAN stack, where you need to create API's manually and do same repetitive thing again and again, Sails.js allows you to automatically create skeleton APIs. Just a single command, and you will have block of code generated to get you started.

Additionally, Sails.js provides database drivers for every major database systems such as MySQL, MongoDB, PostgreSQL. Sails.js uses Grunt task runner as the default task runner, and also provides an effective way to manage custom grunt tasks.

Furthermore, common security parameters such as CORS and CSRF are already included in Sails.js project. All you need to do is to enable them from respective files and your application is secured with the latest security standards.

Sails.js uses Express to handle HTTP operations and Socket.io to handle web socket messages. It provides web socket messages to your routes without configuring it in back-end or front-end layer.

We will learn about all of these features in a practical way in the coming section, where we'll generate an API and configure our database with models.

## Installation and configuration

In order to install sails in your computer, make sure you have the latest version of Node and npm installed, because it needs various new things which is not present in legacy node versions.

Run following command to install it.

```
npm install -g sails
```

![enter image description here](https://www.filepicker.io/api/file/zplMUeIWTLSCiZOFqbQe "enter image title here")

Depending on your internet connection, it will take some time to install Sails.js. Once the installation completes, you can use the **sails** command line to create new projects.

In order to create a new project, run the following command.

```
sails create projectName
```

![enter image description here](https://www.filepicker.io/api/file/URuJBHOESdSPKVW69eGk "enter image title here")

It will create a new directory from where you ran the command and place all needed files into it. Once everything has been created, go to the project directory and run the following command to install modules.

```
npm install
```

It will install all the required packages needed to run your new project.

## Running the Project

To run the project, open up your terminal and go to project directory and run the following command.

```
sails lift
```

![enter image description here](https://www.filepicker.io/api/file/GzLflpqRVqJXoHPigfkg "enter image title here")

Visit localhost:1337 to view the app.

![enter image description here](https://www.filepicker.io/api/file/Zuhr4sopRniuxonhu9E1 "enter image title here")

## Folder structure

Here is the folder view of the application.<br>
```javascript

├── api<br>
│ ├── controllers<br>
│ ├── models<br>
│ ├── policies<br>
│ ├── responses<br>
│ └── services<br>
├── assets<br>
│ ├── images<br>
│ ├── js<br>
│ │ └── dependencies<br>
│ ├── styles<br>
│ └── templates<br>
├── config<br>
│ ├── env<br>
│ └── locales<br>
├── node_modules<br>
│ ├── ejs<br>
│ ├── grunt<br>
│ ├── grunt-contrib-clean<br>
│ ├── rc<br>
│ ├── sails<br>
│ └── sails-disk<br>
├── tasks<br>
│ ├── config<br>
│ └── register<br>
└── views<br>
```

The **controllers** and **model** folders are the most important. **Controller** contains every piece of the code your application needs to drive a backend system and communicate with the View or User interface.

**Model** contains how the object in data store will look like. So, if you are using a relational database, then it will be a table in database and JSON object in model file, or if you are using NoSQL databases such as MongoDB, then it will be collection under model.

## Configuring Your Database

Suppose you want to configure the database say, MySQL in your Sails project. You can do this in steps.

Install the driver.<br>
Configure the connection.

So, for MySQL, you need to install **sails-mysql** and tell Sails to use it. Here are the steps.

Install the module using the following command.<br>
`javascript npm install --save sails-mysql`<br>
Once everything has been installed, go to **config/connections.js** and configure the MySQL settings, put in a proper username and password.

```
 someMysqlServer: {
 adapter: 'sails-mysql',
 host: 'YOUR_MYSQL_SERVER_HOSTNAME_OR_IP_ADDRESS',
 user: 'YOUR_MYSQL_USER',
 password: 'YOUR_MYSQL_PASSWORD',
 database: 'YOUR_MYSQL_DB'
 }
```

Now to tell Sails to use it, open **config/models.js** file and change the driver.

```
 connection: ‘someMysqlServer’,
 migrate : ‘alter’
```

Sails will use this connection to perform the database operation. Migrate can be :<br>
alter<br>
drop<br>
safe

`Alter` and `drop` settings will tell Sails to automatically create a database with tables in it. While `safe` means database creation is the developer's responsibility so Sails will not create them. In a production environment, `safe` is set to default by Sails.

## Creating an Auto-generated API

Sails.js has an awesome feature which will let you generate new API's on the go with default functionalities. To create one, run the following command in terminal.

```
sails generate api testAPI
```

![enter image description here](https://www.filepicker.io/api/file/WhubA4rgT2Q8yijWOhyT "enter image title here")

It will create routes and model files in your controller directory, have a look. In the **api/controllers** folder, it will create a file called **TestAPIController.js**, which will have all routers.

In the Model folder, it will create new file called **testAPI.js**. Put the following code in it.

```
/**
* TestAPI.js
*
* @description :: TODO: You might write a short summary of how this model works and what it represents here.
* @docs :: http://sailsjs.org/#!documentation/models
*/

module.exports = {

 attributes: {
    userName : {
        type : 'string'     
    },
    password : {
        type : 'string'
    }
 }
};
```

Make sure you have created a database and provided it in the connection file. To run the code, lift the app using following code.

```
sails lift
```

Now open your API simulator ( I recommend POSTMAN chrome extension ) and test the API.

- **Create new user**<br>
  ![enter image description here](https://www.filepicker.io/api/file/vchssW0KSZqG1yp88iLw "enter image title here")
- **Get user data**<br>
  ![enter image description here](https://www.filepicker.io/api/file/JG2mppQsSqQcthLmgteN "enter image title here")
- **Update user info**<br>
  ![enter image description here](https://www.filepicker.io/api/file/35ILr0z8Ru6Pi0pl8boQ "enter image title here")
- **Delete user info**<br>
  ![enter image description here](https://www.filepicker.io/api/file/D4aUAGbCQ1ulGkqcxeiJ "enter image title here")

## Configuring Task Runner

Sails already has grunt configured to run tasks, so in order to configure it, go to **/tasks/register** and add your tasks.

```
module.exports = function (grunt) {
    grunt.registerTask('default', ['compileAssets', 'linkAssets', 'watch']);
};
```

To add new task, you need to create the task file in **tasks/config** folder and export the module.

```
module.exports = function(grunt) {
// Your grunt task
};
```

and add it to register by creating a new file, and then adding them into grunt task. OR, you can directly hook it up to the default grunt file - the choice is up to you.

## Security and web socket

Sails has CORS and CSRF ( Cross origin site request and Cross site request forgery) attack prevention code already written. You can enable it by going to /config/CSRF.js and /config/CORS.js.

Sails.js is one of the best MVC framework and can help you to rapidly develop web applications. For those who don't want to reinvent the wheel like we do in the MEAN stack, Sails is a great option.
