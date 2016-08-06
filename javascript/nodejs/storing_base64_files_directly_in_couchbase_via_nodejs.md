# Storing Base64 Files Directly in Couchbase via Node.js

[Original URL](http://blog.couchbase.com/2016/february/storing-base64-files-directly-in-couchbase-via-nodejs)

> So you're developing an application and you want to store your files in Couchbase. As of right now, Couchbase allows for documents up to 20MB in size which is larger than you think. Why would we...

So you're developing an application and you want to store your files in Couchbase. As of right now, Couchbase allows for documents up to 20MB in size which is larger than you think. Why would we store our files in Couchbase rather than the file system? Maybe you don't have a lot of storage space on your application file system or better yet, maybe you'd like to take advantage of the replication features that Couchbase offers so you are never at risk of losing your precious data.

We're going to take a look at accepting file uploads, converting them into base64 strings, and then storing the strings in Couchbase along with any necessary metadata.

## Getting Setup with Couchbase

Couchbase Server will be our NoSQL database of choice here. Being that it is a document database, storing JavaScript objects makes perfect sense.

If you don't already have a copy of Couchbase Server 4.1 or higher, head to the [downloads](http://www.couchbase.com/nosql-databases/downloads) section, and get the binary that meets your platform needs (Mac, Linux, Windows).

With Couchbase installed we need to add something to allow us to use the N1QL query language. We need to add a primary index. This can be done by using the Couchbase Query (CBQ) Client.

On Mac, launch CBQ by executing the following from the Terminal:

```
./Applications/Couchbase Server.app/Contents/Resources/couchbase-core/bin/cbq
```

On Microsoft Windows, the same can be done by executing the following:

```
C:/Program Files/Couchbase/Server/bin/cbq.exe
```

If you've never used CBQ before, you'll find it a lot like the MySQL command line tool or Oracle's SQLPlus. We can run queries with it among other things. The query we will run will create a primary index. Execute the following:

```
CREATE PRIMARY INDEX ON `default` USING GSI;
```

Couchbase Server is now ready to be used with our Node.js application.

## Configuring a Basic Node.js Application

Much of what we see here was taken from two different tutorials I wrote on the subject. I once demonstrated how to accept [file uploads in Node.js via an Angular 2 application](https://blog.nraboy.com/2016/02/upload-files-to-node-js-using-angular-2/), as well as [how to convert those files into base64 data](https://blog.nraboy.com/2016/02/convert-an-uploaded-image-to-a-base64-string-in-node-js/). This time we are just going to insert the base64 data into Couchbase Server.

First lets create a fresh Node.js project. It won't have a front-end, but you can create your own or use a tool like Postman for testing. Your project should have the following files and directories:

```
uploads/
config.json
app.js
```

Using a Terminal (Mac and Linux) or Command Prompt (Windows), execute the following from within the project directory:

```
npm init -y
```

The above command will create a new **package.json** file. Now we need to install our project dependencies. This can be done by executing the following from the Command Prompt or Terminal:

```
npm install couchbase express uuid body-parser multer --save
```

Now we can start developing our small Node.js application. We're going to keep it very simple. In the **config.json** file include the following information about our Couchbase node:

```
{
 "couchbase": {
 "server": "127.0.0.1:8091",
 "bucket": "default"
 }
}
```

This will prevent us from having to hardcode our server information throughout the project.

Now we want to populate our core Node.js logic file, the **app.js** file. Go ahead and add the following code:

```
var express = require("express");
var bodyParser = require("body-parser");
var multer = require("multer");
var fs = require("fs");
var app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

app.use(function(req, res, next) {
 res.header("Access-Control-Allow-Origin", "*");
 res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
 next();
});

app.post("/upload", multer({dest: "./uploads/"}).array("uploads", 12), function(req, res) {
 var fileInfo = [];
 for(var i = 0; i < req.files.length; i++) {
 fileInfo.push({
 "originalName": req.files[i].originalName,
 "size": req.files[i].size,
 "b64": new Buffer(fs.readFileSync(req.files[i].path)).toString("base64")
 });
 fs.unlink(req.files[i].path);
 }
 res.send(fileInfo);
});

var server = app.listen(3000, function() {
 console.log("Listening on port %s...", server.address().port);
});
```

You're actually caught up to where I left off in my [Node.js base64 conversion tutorial](https://blog.nraboy.com/2016/02/convert-an-uploaded-image-to-a-base64-string-in-node-js/). The thing is, now we want to store that data into Couchbase Server.

## Inserting File Data into Couchbase via N1QL Queries

Let's start by including the various dependency information that we downloaded in the earlier steps. At the top of the **app.js** file with the other imports, include the following:

```
var couchbase = require("couchbase");
var N1qlQuery = require('couchbase').N1qlQuery;
var uuid = require("uuid");
var config = require("./config");
```

We're including Couchbase, the N1QL engine, our server information, and the library for generating unique values. Now, before or above the block of code for cross origin resource sharing, add the following line:

```
var bucket = (new couchbase.Cluster(config.couchbase.server)).openBucket(config.couchbase.bucket);
```

Like I said it doesn't really matter where you put it, but this line will connect to a Couchbase cluster, and open the bucket, both of which we defined in the **config.json** file.

Now time for the juicy stuff!

We are going to change the endpoint function for uploads to save to Couchbase before returning the data back to the user. Go ahead and change the endpoint function to look like the following:

```
app.post("/upload", multer({dest: "./uploads/"}).array("uploads", 12), function(req, res) {
 var fileInfo = [];
 for(var i = 0; i < req.files.length; i++) {
 fileInfo.push({
 "originalName": req.files[i].originalName,
 "size": req.files[i].size,
 "b64": new Buffer(fs.readFileSync(req.files[i].path)).toString("base64")
 });
 fs.unlink(req.files[i].path);
 }
 var statement = "INSERT INTO `" + config.couchbase.bucket + "` (KEY,VALUE) VALUES " +
 "($1, {'files': $2})";
 var query = N1qlQuery.fromString(statement);
 bucket.query(query, [uuid.v4(), fileInfo], function(error, result) {
 if(error) {
 return res.status(400).send({"status": "error", "message": error});
 }
 res.send(fileInfo);
 });
});
```

Notice that we are now creating an `INSERT` statement? This is a parameterized query to prevent any kind of SQL injection attacks. The parameters are defined in the actual query and they are the unique id value for the key and the file array that we created. Only once the data has been saved will it be returned to the user.

## Conclusion

Yes there was a bit of hand-waving done here when it comes to the actual file uploads, but that is because we care more about the inserting into Couchbase. Also because it was explained in my [previous tutorial](https://blog.nraboy.com/2016/02/convert-an-uploaded-image-to-a-base64-string-in-node-js/). Here we saw how to do a N1QL query to insert the array that contained all our base64 files that were uploaded. As long as the file sizes don't exceed the Couchbase limits, it is a perfectly acceptable solution and will allow for replication should it be enabled.
