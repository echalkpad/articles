# Even Easier: RESTful API with Node.js and Express Framework

[Original URL](http://ericbrandel.com/2013/01/27/even-easier-restful-api-with-node-js-and-express-framework/)

> I just finished up an absolutely thrilling four part series on using the Slim PHP Framework to knock out a RESTful API fairly quickly. This post (and likely a follow-up one) will show how it's...

I just finished up an _absolutely_ [thrilling four part series](http://ericbrandel.com/2012/10/18/quickly-build-restful-apis-in-php-with-slim/ "Quickly Build RESTful APIs in PHP with Slim") on using the Slim PHP Framework to knock out a RESTful API fairly quickly. This post (and likely a follow-up one) will show how it's even easier to accomplish the same thing with [Node.js](http://nodejs.org/ "Node.js") and the [Express Web Application Framework](http://expressjs.com/ "Express Web Application Framework").

For an IDE, instead of the old faithful Netbeans, I switched over to [JetBrain's WebStorm](http://www.jetbrains.com/webstorm/ "JetBrain WebStorm"). JetBrain is best known for the very powerful [IntelliJ IDEA](http://www.jetbrains.com/idea/ "IntelliJ IDEA") and WebStorm is their attempt at crafting the ultimate IDE for Javascript development. So far I've been pretty impressed, and it seems to be the most evolved Javascript focused development environment out there.

[![JetBrain WebStorm - It's probably true](http://i0.wp.com/ericbrandel.com/wp-content/uploads/2013/01/webstorm.gif?resize=716%2C332)](http://i0.wp.com/ericbrandel.com/wp-content/uploads/2013/01/webstorm.gif) JetBrain WebStorm – It's probably true Anyways, there won't be a ton of Node.js info here, other than to point out it's becoming a very popular choice for server-side coding. Everyone from LinkedIn to Walmart to Microsoft is investing significant time and energy into Node.js based development. One key benefit of running Javascript on the server is that you are able to tap into existing competencies of web developers, very few of which will not have at least a decent Javascript skillset.

Frequently on top of Node.js is the [Express Web Application Framework](http://expressjs.com/ "Express Web Application Framework"). Express is similar to Slim in that it does a lot, but stays out of your way, and makes routing RESTful APIs a breeze. For example, setting up a new web app is pretty simple:

```
var express = require('express')
 , get = require('./routes/get')
 , http = require('http')
 , path = require('path');

var app = express();

app.configure(function(){
 app.set('port', process.env.PORT || 3000);
 app.use(express.logger('dev'));
 app.use(express.bodyParser());
 app.use(express.methodOverride());
 app.use(app.router);
 app.use(express.static(path.join(__dirname, 'public')));
});

app.configure('development', function(){
 app.use(express.errorHandler());
});

app.get('/', get.all);
app.get('/:id', get.one);

http.createServer(app).listen(app.get('port'), function(){
 console.log("Express server listening on port " + app.get('port'));
});
```

Not much there really. The two key lines are really the two app.get entries:

```
app.get('/', get.all);
app.get('/:id', get.one);
```

They setup the routes for _/_ and _/:id_ and hooks them to _get.all_ and _get.one_. The second app.get contains a parameter, _:id_, which is available to the routed functions via a fairly simple params array. _get_ was defined on the second line, which points to _/routes/get.js_:

```
var mysql = require('mysql');
var connection = mysql.createConnection({ host: 'localhost', user: 'api', 
 password: '12345', database: 'api'});

exports.all = function(req, res){
 if (connection) {
 connection.query('select create_a_rest_api_with_nodejs.md data data.json html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md links_list.txt md parse_urls_json.sh png sqljs.md tidy timothycrosleyhug.md url_to_filename.csv from commodores order by name', function(err, rows, fields) {
 if (err) throw err;
 res.contentType('application/json');
 res.write(JSON.stringify(rows));
 res.end();
 });
 }
};

exports.one = function(req, res){
 var id = req.params.id;
 if (connection) {
 var queryString = 'select create_a_rest_api_with_nodejs.md data data.json html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md links_list.txt md parse_urls_json.sh png sqljs.md tidy timothycrosleyhug.md url_to_filename.csv from commodores where id = ?';
 connection.query(queryString, [id], function(err, rows, fields) {
 if (err) throw err;
 res.contentType('application/json');
 res.write(JSON.stringify(rows));
 res.end();
 });
 }
};
```

Again, very little effort needed to get what we want, which is the data back to us in JSON. No ORM in use here, so there's just some straight SQL calls to the MySQL database. The results from hitting the two URLs:<br>
***URL:** <http://localhost:3000/*>

```
[
{"id":"12","name":"Commodore 128","url":"http:\/\/en.wikipedia.org\/wiki\/Commodore_128"}
{"id":"9","name":"Commodore 16","url":"http:\/\/en.wikipedia.org\/wiki\/Commodore_16"}
...
{"id":"8","name":"Commodore SX-64","url":"http:\/\/en.wikipedia.org\/wiki\/Commodore_SX-64"}
{"id":"3","name":"Commodore VIC-20","url":"http:\/\/en.wikipedia.org\/wiki\/Commodore_VIC-20"}
]
```

To request a specific item:<br>
***URL:** <http://localhost:3000/2*>

```
[{"id":2,"name":"Commodore PET/CBM","url":"http://en.wikipedia.org/wiki/Commodore_PET"}]
```

And that's all that is needed for the two most common Get calls in a RESTful API. Part 2 will show how to setup the remaining Post, Put, and Delete calls, and will be linked here when it is complete.

[Google+](https://plus.google.com/109768187739364803836?rel=author)