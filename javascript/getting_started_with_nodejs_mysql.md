# Getting Started with Node.js + MySQL

[Original URL](https://www.codementor.io/nodejs/tutorial/node-js-mysql)

> Through code examples, this Node.js Tutorial will teach you how to get started with using MySQL, how to use it in production, and how to test the concurrent users. The article was originally posted...

![](https://s3.amazonaws.com/codementor_content/2015-Apr-week4/node_mysql.png)

_Through code examples, this Node.js Tutorial will teach you how to get started with using MySQL, how to use it in production, and how to test the concurrent users. The article was originally posted at [Codementor Shahid Shaikh](https://www.codementor.io/codeforgeek)'s [blog](http://codeforgeek.com/2015/01/nodejs-mysql-tutorial/)._

Node.js and MySQL is one of the necessary binding needed for any web application. MySQL is one of the most popular open source databases in world, and it's efficient as well. Almost every popular programming language like [Java](http://codeforgeek.com/2014/07/java-mysql-connectivity-jdbc/ "Java and MySQL Connectivity Using JDBC")or [PHP](http://codeforgeek.com/2014/07/php-mysql-connectivity/ "PHP and MySQL Connectivity")provides a driver to access and perform operations with MySQL.

In this tutorial I am trying to cover code for learning and code for production.

## Introduction

Node.js is rich with the number of popular packages registered at package registry called [NPM](http://codeforgeek.com/2014/08/how-to-create-nodejs-npm-package/ "How to create a NodeJS NPM package"). Most of them are not so reliable to use for production but there are some on which we can rely upon. For MySQL there is one popular driver called **node-mysql**.

In this tutorial I am going to cover following points related to [Node.js](http://codeforgeek.com/2014/12/cluster-node-js-performance/ "Cluster in Node.js tutorial") and MySQL:

- Sample code to get started.
- Code for Production.
- Testing concurrent users.

## Getting Started

### Project directory:

```
---node_modules
 -----+ mysql
 -----+ express
 ---index.js
 ---package.json
```

**package.json**

```
{
 "name": "node-mysql",
 "version": "0.0.1",
 "dependencies": {
 "express": "^4.10.6",
 "mysql": "^2.5.4"
 }
 }
```

Install dependencies using

```
npm install
```

Here is the sample code which connects to Database and performs an SQL query:

```
var mysql = require('mysql');
 var connection = mysql.createConnection({
 host : 'localhost',
 user : '< MySQL username >',
 password : '< MySQL password >',
 database : '<your database name>'
 });

 connection.connect();

 connection.query('SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii from < table name >', function(err, rows, fields) {
 if (!err)
 console.log('The solution is: ', rows);
 else
 console.log('Error while performing Query.');
 });

 connection.end();
```

Make sure that you have started MySQL on your default port that you have and changed the parameter in the code above. Then, run the code using:

```
node file_name.js
```

## Using MySQL in Production

The first example code is just for learning purposes and not for production payload. MySQL is different for a production scenario, as there may be thousands of concurrent users which turns into tons of MySQL queries. The first sample won't run for concurrent users and I'll show you why.

Let's modify our code little bit and add Express routes in that:

```
test.js ( Change database settings in code )

var express = require("express");
 var mysql = require('mysql');
 var connection = mysql.createConnection({
 host : 'localhost',
 user : 'root',
 password : '',
 database : 'address_book'
 });
 var app = express();

 connection.connect(function(err){
 if(!err) {
 console.log("Database is connected ... \n\n"); 
 } else {
 console.log("Error connecting database ... \n\n"); 
 }
 });

 app.get("/",function(req,res){
 connection.query('SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii from user LIMIT 2', function(err, rows, fields) {
 connection.end();
 if (!err)
 console.log('The solution is: ', rows);
 else
 console.log('Error while performing Query.');
 });
 });

 app.listen(3000);
```

Install **siege** in your system. I use this command to install it in Ubuntu:

```
apt-get install siege
```

Then, run our node and server, and then use the following command (Assuming you are running Node server on Port 3000):

```
node test.js
siege -c10 -t1M http://localhost:3000
```

Here is the output:<br>
![Node and mysql](https://s3.amazonaws.com/codementor_content/2015-Apr-week4/pic.png)<br>
In the code above, we are allowing it to run for standalone connections i.e one connection at a time, but reality is bit different. You may get 100 or 1000 connections at one particular time and if your server is not powerful enough to serve those requests, then at least it should put them in queue.

### Pooling connections in MySQL :

Connection Pooling is a mechanism to maintain a cache of database connections. This way, that connection can be reused after releasing it. In Node MySQL, we can use pooling directly to handle multiple connections and reuse them.

Let's write some code with pooling, and check whether it can handle multiple connections or not.<br>
**test.js**

```
var express = require("express");
 var mysql = require('mysql');
 var app = express();

 var pool = mysql.createPool({
 connectionLimit : 100, //important
 host : 'localhost',
 user : 'root',
 password : '',
 database : 'address_book',
 debug : false
 });

 function handle_database(req,res) {

 pool.getConnection(function(err,connection){
 if (err) {
 connection.release();
 res.json({"code" : 100, "status" : "Error in connection database"});
 return;
 } 

 console.log('connected as id ' + connection.threadId);

 connection.query("select articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii from user",function(err,rows){
 connection.release();
 if(!err) {
 res.json(rows);
 } 
 });

 connection.on('error', function(err) { 
 res.json({"code" : 100, "status" : "Error in connection database"});
 return; 
 });
 });
 }

 app.get("/",function(req,res){-
 handle_database(req,res);
 });

 app.listen(3000);
```

Run the app using

```
node test.js
```

and fire 10 concurrent users for 1 minute using siege by using this command:

```
siege -c10 -t1M http://localhost:3000
```

Here is the output:<br>
![Code is stable ! ](https://s3.amazonaws.com/codementor_content/2015-Apr-week4/pic2.png)

### Final comments :

Siege is a really powerful tool for testing servers under pressure. We have created a 100-connection limit in code, so you might be wondering if the code will break after 100 concurrent connections. Well let me answer it via code. Fire 1000 concurrent user for 1 minute and let's see how our code reacts.

```
siege -c1000 -t1M http://localhost:3000
```

If your MySQL server is configured to handle such traffic at one socket, then it will run and our code will manage the scheduling of concurrent connection. It will serve 100 connections a time but the remaining 900 will be in queue. Thus, the code will not break.

### Conclusion :

MySQL is one of widely used database engines in world and it works very well with Node. Node-MySQL pooling and event based debugging is really powerful and easy to code.

### Further reading:

I believe [Github help](https://github.com/felixge/node-mysql) page for node-mysql module is enough. Have a look and give it a star.
