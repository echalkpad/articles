# Understanding Object Streams

[Original URL](https://nodesource.com/blog/understanding-object-streams/)

> One of the most useful, yet most commonly misunderstood types of Node.js streams are object streams. In this article we'll explain what exactly object streams are, how they behave in contrast to...

One of the most useful, yet most commonly misunderstood types of Node.js streams are object streams. In this article we'll explain what exactly object streams are, how they behave in contrast to regular streams, and how we can use them ourselves.

## When Buffers and Strings Don't Suffice

Streams were originally designed to make processing I/O in Node more manageable and efficient. Streams are essentially `EventEmitter`s that can represent a readable and/or writable source of data. Just like a stream of liquid, the data flows to/from.

By default streams only support dealing with `String`s and `Buffer`s. Node's core modules don't use object streams, partly because there aren't any real use cases yet. However, we can make use of object streams in our own code by using the `objectMode` option.

When in `objectMode`, streams can push `String`s and `Buffer`s as well as any other JavaScript object. Another major difference is that when in `objectMode`, the internal buffering algorithm counts objects rather than bytes. This means if we have a Transform stream with the `highWaterMark` option set to 5, the stream will only buffer a maximum of 5 objects internally.

## Creating an Object Stream

We'll start off by demonstrating a simple stream with `objectMode` enabled. We're going to create a simple `Transform` stream that receives data from a source, uppercases it, and splits the result at every whitespace character. As a result, our stream will emit arrays. Object streams don't have to be `Transform` streams, but we're going to implement one as an example.

For the examples in this article we're going to use a module from npm called _[through2](https://www.npmjs.com/package/through2)_. _through2_ is a tiny abstraction around Node's core `stream.Tranform` class that allows you to create streams easily. We're also going to use another module called _[split2](https://www.npmjs.com/package/split2)_ to split our input by newlines.

```
var through2 = require('through2') 
var split2 = require('split2')

var stream = through2({ objectMode: true }, function(chunk, enc, callback) { 
 var string = chunk.toString()
 var result = string.replace(/\n/, '').toUpperCase().split(/[ \t]/)

 this.push(result)
 callback()
})

stream.on('data', function(data) { 
 var toString = Object.prototype.toString.call(data)
 console.log('type of data:', toString)
 console.log('data:', data, '\n')
})

process.stdin 
 .pipe(split2())
 .pipe(stream)
```

If we run this and feed some lines into the standard input, they'll be written back to the standard output like so.

```
Hello world 
type of data: [object Array] 
data: ["HELLO", "WORLD"]

Transform streams are great! 
type of data: [object Array] 
data: ["TRANSFORM", "STREAMS", "ARE", "GREAT!"] 
```

## Playing Nicely With Other Streams

If an object stream _isn't_ emitting `String`s or `Buffer`s, it's important to note that you can't pipe it to a non-object stream. Let's take the following code:

```
var through2 = require('through2')

var objectStream = through2.obj(function(chunk, encoding, callback) { 
 chunk.timestamp = new Date()
 this.push(chunk)
 callback()
})

objectStream.pipe(process.stdout)

objectStream.write({ status: 404, message: 'Not found' }) 
objectStream.write({ status: 500, message: 'Internal server error'}) 
```

If we try run that, it'll fail straight away with a `TypeError: invalid data`. This happens because `process.stdout` is a regular stream that can only deal with `String`s and `Buffer`s. If we want to be able to pipe our data to `process.stdout`, we need to create another object stream that appropriately transforms our data, for example, by emitting a JSON-stringified version of our object.

```
var jsonStream = through2.obj(function(chunk, encoding, callback) { 
 this.push(JSON.stringify(chunk, null, 4) + '\n')
 callback()
})

objectMode.pipe(jsonStream) 
 .pipe(process.stdout)
```

If we run this code again, we can see that we get JSON data in our console.

```
{
 "status": 400,
 "message": "Not found",
 "timestamp": "2015-01-31T14:04:02.978Z"
}
{
 "status": 500,
 "message": "Internal server error",
 "timestamp": "2015-01-31T14:04:02.978Z"
}
```

## Where Are Object Streams Useful?

Though object streams aren't used in Node's core, they can be useful in a few cases.

### Protocol Parsers

These streams can take in raw data from a server and create useful and consumable representations of it in JavaScript. For example, the _[irc-message-stream](https://www.npmjs.com/package/irc-message-stream)_ module can take a stream of IRC data, parse it and push descriptive JavaScript objects.

```
var net = require('net') 
var messageStream = require('irc-message-stream')

net.connect(6667, 'irc.freenode.net') 
 .pipe(messageStream())
 .on('data', function(message) {
 console.log(JSON.stringify(message, null, 4))
 console.log('is server?', message.prefixIsServer())
 })
```

This will produce output similar to the following:

```
{
 "tags": {}
 "prefix": "server.freenode.net",
 "command": "*",
 "params": ["randomuser", "*** Looking up your hostname..."]
}
is server? true 
```

_[csv-parse](https://www.npmjs.com/package/csv-parse)_ (the module used in _[csv](https://www.npmjs.com/package/csv)_) also uses an object stream, to parse CSV data from buffers. You can see the implementation for this [over here](https://github.com/wdavidw/node-csv-parse/blob/7be7339b5215c461651ee0ed19103b07e6848295/lib/index.js#L70), the module would be used like so:

```
var fs = require('fs') 
var csvParse = require('csv-parse')

fs.createReadStream('data.csv') 
 .pipe(csvParse({ auto_parse: true }))
 .on('data', function(record) {
 console.log(JSON.stringify(record))
 })
```

If we create _data.csv_ with some CSV data (such as below), we can see what output we'll get from running our code.

```
Fionn,Kelleher,"Writer, Developer",1234 
Phil,Lynott,"Singer, Songwriter",5678 

["Fionn","Kelleher","Writer, Developer",1234]
["Phil","Lynott","Singer, Songwriter",5678]
```

If we wanted to, we could chain our stream of CSV data with another Transform stream to convert it into a more descriptive object.

```
var fs = require('fs') 
var csvParse = require('csv-parse') 
var through2 = require('through2')

fs.createReadStream('data.csv') 
 .pipe(csvParse({ auto_parse: true }))
 .pipe(through2.obj(function(chunk, encoding, callback) {
 this.push({
 firstName: chunk[0],
 lastName: chunk[1],
 professions: chunk[2].split(', '),
 id: chunk[3]
 })
 callback()
 }))
 .on('data', function(record) {
 console.log(JSON.stringify(record, null, 4))
 })
```

This will give us the following output.

```
{
 "firstName": "Fionn",
 "lastName": "Kelleher",
 "professions": [
 "Writer",
 "Developer"
 ],
 "id": 1234
}
{
 "firstName": "Phil",
 "lastName": "Lynott",
 "professions": [
 "Singer",
 "Songwriter"
 ],
 "id": 5678
}
```

### Database Records

Another common use case for object streams is for consuming database records. Modules like _[LevelUP](https://www.npmjs.com/package/levelup)_ can create streams that emit objects with key/value pairs.

```
var levelup = require('levelup') 
var db = levelup('./testdb')

db.createReadStream() 
 .on('data', function (data) {
 console.log(data.key, "=", data.value)
 })

db.put('hello', 'world', function (err) { 
 if (err) console.log(err)
})
```

Hopefully from reading this, you have a more concrete understanding of object streams and how they can be applied.
