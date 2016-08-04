# Better logging in Nodejs using Bunyan

[Original URL](http://www.codingdefined.com/2014/12/better-logging-in-nodejs-using-bunyan.html)

> In this post we will discuss about logging in Nodejs using Bunyan. You might have seen examples of nodejs in our site as well as in any other site, that everyone uses console.log to log the messages....

[![](http://4.bp.blogspot.com/-vJcblLR5n7g/U7Vh52-vssI/AAAAAAAAACA/M2EcY7husXc/s1600/nodejslogo.jpg)](http://4.bp.blogspot.com/-vJcblLR5n7g/U7Vh52-vssI/AAAAAAAAACA/M2EcY7husXc/s1600/nodejslogo.jpg)

In this post we will discuss about logging in Nodejs using [Bunyan](https://github.com/trentm/node-bunyan/). You might have seen examples of nodejs in our site as well as in any other site, that everyone uses console.log to log the messages. Thus it gives a wrong impression to the developers that this is the best way to log messages in an application.<br>
[]()<br>
But, a BIGG NO to 

<span>console.log</span>

. You must use something better than console.log when you are creating your node applications. In this post we will discuss about better logging in your node application by using Bunyan.

[![](http://4.bp.blogspot.com/-nyr2yCe1OA4/VJVAHI6FvRI/AAAAAAAAAPo/QQmXAHgaXyY/s1600/bunyan.png)](http://4.bp.blogspot.com/-nyr2yCe1OA4/VJVAHI6FvRI/AAAAAAAAAPo/QQmXAHgaXyY/s1600/bunyan.png)

PC: <https://www.npmjs.com/package/bunyan>

As per [Bunyan](https://github.com/trentm/node-bunyan/), it is a simple and fast JSON logging library for nodejs services. They also have a Bunyan CLI tool for nicely viewing the logs.<br>
**** **How to Install Bunyan**<br>
**** 

<span>npm install -g bunyan</span>

 

<span>// It will get you Bunyan CLI in your Path</span>

**How to Use Bunyan**

Create a file named example5.js whose contents will be :

<span>var bunyan = require(’bunyan’);</span>

<br>

<span>var log = bunyan.createLogger({name: ‘myFirstBunyanExample’});</span>

<br>

<span>log.info(’1st Log using Bunyan’);</span>

<br>

<span>log.warn(’It’s just a warning’);</span>

**Output Without Using CLI**

[![](http://2.bp.blogspot.com/-ZwZNBCNDlyo/VJ-eEqnMvDI/AAAAAAAAAUg/lzZA8Sc9RHE/s1600/bsiilji.png)](http://2.bp.blogspot.com/-ZwZNBCNDlyo/VJ-eEqnMvDI/AAAAAAAAAUg/lzZA8Sc9RHE/s1600/bsiilji.png)

**Output With Using CLI**<br>
**** [![](http://3.bp.blogspot.com/-4JjPOq7evQA/VJ-eEjH5PhI/AAAAAAAAAUk/QSOQdNZet30/s1600/sy4zp3u.png)](http://3.bp.blogspot.com/-4JjPOq7evQA/VJ-eEjH5PhI/AAAAAAAAAUk/QSOQdNZet30/s1600/sy4zp3u.png)

**Log Levels**

As you can see in the output level field is a number, where for Info it is 30 and for Warning it is 40\. So, let see what are the different Log Levels available

<span>Level Name : trace, Level Number : 10</span>

<br>

<span>Level Name : debug, Level Number : 20</span>

<br>

<span>Level Name : info, Level Number : 30</span>

<br>

<span>Level Name : warn, Level Number : 40</span>

<br>

<span>Level Name : error, Level Number : 50</span>

<br>

<span>Level Name : fatal, Level Number : 60</span>

**Log Method API**

<span>log.info();</span>

 

<span>// returns a boolean message to know is “info” level enabled</span>

<br>

<span>log.info(’message’);</span>

 

<span>// logs a simple message</span>

<br>

<span>log.info(’message %s’, a, b);</span>

 

<span>// message formatting using util.format</span>

<br>

<span>log.info({a: ‘b’}, ‘message’);</span>

 

<span>//adds “a” field to log record</span>

<br>

<span>
  <br>
</span>

 Please Like and Share the Blog, if you find it interesting and helpful.
