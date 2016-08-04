# Working With IndexedDB - Tuts+ Code Tutorial

[Original URL](https://code.tutsplus.com/tutorials/working-with-indexeddb--net-34673)

> One of the more interesting developments in web standards lately is the Indexed Database (IndexedDB for short) specification. For a fun time you can read the spec yourself. In this tutorial I'll be...

One of the more interesting developments in web standards lately is the Indexed Database (IndexedDB for short) specification. For a fun time you can read the [spec](http://www.w3.org/TR/IndexedDB/ "Spec") yourself. In this tutorial I'll be explaining this feature and hopefully giving you some inspiration to use this powerful feature yourself.

## Overview

> As a specification, IndexedDB is currently a Candidate Recommendation.

In a nutshell, IndexedDB provides a way for you to store large amounts of data on your user's browser. Any application that needs to send a lot of data over the wire could greatly benefit from being able to store that data on the client instead. Of course storage is only part of the equation. IndexedDB also provides a powerful indexed based searching API to retrieve the data you need.

You may wonder how IndexedDB differs from other storage mechanisms?

Cookies are extremely well supported, but have legal implications and limited storage space. Also - they are sent back and forth to the server with every request, completely negating the benefits of client-side storage.

Local Storage is also very well supported, but limited in terms of the total amount of storage you can use. Local Storage doesn't provide a true "search" API as data is only retrieved via key values. Local Storage is great for "specific" things you may want to store, for example, preferences, whereas IndexedDB is better suited for Ad Hoc data (much like a database).

Before we go any further though, let's have an honest talk about the state of IndexedDB in terms of browser support. As a specification, IndexedDB is currently a Candidate Recommendation. At this point the folks behind the specification are happy with it but are now looking for feedback from the developer community. The specification may change between now and the final stage, W3C Recommendation. In general, the browsers that support IndexedDB now all do in a fairly consistent manner, but developers should be prepared to deal with prefixes and take note of updates in the future.

As for those browsers supporting IndexedDB, you've got a bit of a dilemma. Support is pretty darn good for the desktop, but virtually non-existent for mobile. Let's see what the _excellent_ site CanIUse.com says:

![CanIUse Report for IndexedDB](https://cdn.tutsplus.com/net/uploads/2013/09/s1.png)<br>
Chrome for Android does support the feature, but very few people are currently using that browser on Android devices. Does the lack of mobile support imply you shouldn't use it? Of course not! Hopefully all our readers are familiar with the concept of progressive enhancement. Features like IndexedDB can be added to your application in a manner that won't break in non-supported browsers. You could use wrapper libraries to switch to WebSQL on mobile, or simply skip storing data locally on your mobile clients. Personally I believe the ability to cache large blocks of data on the client is important enough to use now even without mobile support.

## Let's Get Started

We've covered the specification and support, now let's look at using the feature. The very first thing we should do is check for IndexedDB support. While there are tools out there that provide generic ways to check for browser features, we can make this much simpler since we're just checking for one particular thing.

```
document.addEventListener("DOMContentLoaded", function(){

 if("indexedDB" in window) {
 console.log("YES!!! I CAN DO IT!!! WOOT!!!");
 } else {
 console.log("I has a sad.");
 }

},false);
```

The code snippet above (available in `test1.html` if you download the zip file attached to this article) uses the `DOMContentLoaded` event to wait for the page to load. (Ok, that's kind of obvious, but I recognize this may not be familiar to folks who have only used jQuery.) I then simply see if indexedDB exists in the `window` object and if so, we're good to go. That's the simplest example, but typically we would probably want to store this so we know later on if we can use the feature. Here's a slightly more advanced example (`test2.html`).

```
var idbSupported = false;

document.addEventListener("DOMContentLoaded", function(){

 if("indexedDB" in window) {
 idbSupported = true;
 }

},false);
```

All I've done is created a global variable, `idbSupported`, that can be used as a flag to see if the current browser can use IndexedDB.

## Opening a Database

IndexedDB, as you can imagine, makes use of databases. To be clear, this isn't a SQL Server implementation. This database is local to the browser and only available to the user. IndexedDB databases follow the same rules as cookies and local storage. A database is unique to the domain it was loaded from. So for example, a database called "Foo" created at foo.com will not conflict with a database of the same name at goo.com. Not only will it not conflict, it won't be available to other domains as well. You can store data for your web site knowing that another web site will not be able to access it.

Opening a database is done via the open command. In basic usage you provide a name and a version. The version is _very_ important for reasons I'll cover more later. Here's a simple example:

```
var openRequest = indexedDB.open("test",1);
```

Opening a database is an asynchronous operation. In order to handle the result of this operation you'll need to add some event listeners. There's four different types of events that can be fired:

- success
- error
- upgradeneeded
- blocked

You can probably guess as to what success and error imply. The upgradeneeded event is used both when the user first opens the database as well as when you change the version. Blocked isn't something that will happen usually, but can fire if a previous connection was never closed.

Typically what should happen is that on the first hit to your site the upgradeneeded event will fire. After that - just the success handler. Let's look at a simple example (`test3.html`).

```
var idbSupported = false;
var db;

document.addEventListener("DOMContentLoaded", function(){

 if("indexedDB" in window) {
 idbSupported = true;
 }

 if(idbSupported) {
 var openRequest = indexedDB.open("test",1);

 openRequest.onupgradeneeded = function(e) {
 console.log("Upgrading...");
 }

 openRequest.onsuccess = function(e) {
 console.log("Success!");
 db = e.target.result;
 }

 openRequest.onerror = function(e) {
 console.log("Error");
 console.dir(e);
 }

 }

},false);
```

Once again we check to see if IndexedDB is actually supported, and if it is, we open a database. We've covered three events here - the upgrade needed event, the success event, and the error event. For now focus on the success event. The event is passed a handler via `target.result`. We've copied that to a global variable called `db`. This is something we'll use later to actually add data. If you run this in your browser (in one that supports IndexedDB of course!), you should see the upgrade and success message in your console the first time you run the script. The second, and so forth, times you run the script you should only see the success message.

## Object Stores

So far we've checked for IndexedDB support, confirmed it, and opened a connection to a database. Now we need a place to store data. IndexedDB has a concept of "Object Stores." You can think of this as a typical database table. (It is much more loose than a typical database table, but don't worry about that now.) Object stores have data (obviously) but also a keypath and an optional set of indexes. Keypaths are basically unique identifiers for your data and come in a few different formats. Indexes will be covered later when we start talking about retrieving data.

Now for something crucial. Remember the upgradeneeded event mentioned before? You can only create object stores during an upgradeneeded event. Now - by default - this will run automatically the first time a user hits your site. You can use this to create your object stores. The crucial thing to remember is that if you ever need to _modify_ your object stores, you're going to need to upgrade the version (back in that open event) and write code to handle your changes. Lets take a look at a simple example of this in action.

```
var idbSupported = false;
var db;

document.addEventListener("DOMContentLoaded", function(){

 if("indexedDB" in window) {
 idbSupported = true;
 }

 if(idbSupported) {
 var openRequest = indexedDB.open("test_v2",1);

 openRequest.onupgradeneeded = function(e) {
 console.log("running onupgradeneeded");
 var thisDB = e.target.result;

 if(!thisDB.objectStoreNames.contains("firstOS")) {
 thisDB.createObjectStore("firstOS");
 }

 }

 openRequest.onsuccess = function(e) {
 console.log("Success!");
 db = e.target.result;
 }

 openRequest.onerror = function(e) {
 console.log("Error");
 console.dir(e);
 }

 }

},false);
```

This example (`test4.html`) builds upon the previous entries so I'll just focus on what's new. Within the upgradeneeded event, I've made use of the database variable passed to it (`thisDB`). One of the properties of this variable is a list of existing object stores called `objectStoreNames`. For folks curious, this is not a simple array but a "DOMStringList." Don't ask me - but there ya go. We can use the `contains` method to see if our object store exists, and if not, create it. This is one of the few synchronous functions in IndexedDB so we don't have to listen for the result.

To summarize then - this is what would happen when a user visits your site. The first time they are here, the upgradeneeded event fires. The code checks to see if an object store, "firstOS" exists. It will not. Therefore - it is created. Then the success handler runs. The second time they visit the site, the version number will be the same so the upgradeneeded event is _not_ fired.

Now imagine you wanted to add a second object store. All you need to do is increment the version number and basically duplicate the contains/createObjectStore code block you see above. The cool thing is that your upgradeneeded code will support both people who are brand new to the site as well as those who already had the first object store. Here is an example of this (`test5.html`):

```
var openRequest = indexedDB.open("test_v2",2);

openRequest.onupgradeneeded = function(e) {
 console.log("running onupgradeneeded");
 var thisDB = e.target.result;

 if(!thisDB.objectStoreNames.contains("firstOS")) {
 thisDB.createObjectStore("firstOS");
 }

 if(!thisDB.objectStoreNames.contains("secondOS")) {
 thisDB.createObjectStore("secondOS");
 }

}
```

## Adding Data

Once you've got your object stores ready you can begin adding data. This is - perhaps - one of the coolest aspects of IndexedDB. Unlike traditional table-based databases, IndexedDB lets you store an object as is. What that means is you can take a generic JavaScript object and just store it. Done. Obviously there's some caveats here, but for the most part, that's it.

Working with data requires you to use a transaction. Transactions take two arguments. The first is an array of tables you'll be working with. Most of the time this will be one table. The second argument is the type of transaction. There are two types of transactions: readonly and readwrite. Adding data will be a readwrite operation. Let's start by creating the transaction:

```
//Assume db is a database variable opened earlier
var transaction = db.transaction(["people"],"readwrite");
```

Note the object store, "people", is just one we've made up in the example above. Our next full demo will make use of it. After getting the transaction, you then ask it for the object store you said you would be working with:

```
var store = transaction.objectStore("people");
```

Now that you've got the store you can add data. This is done via the - wait for it - `add` method.

```
//Define a person
var person = {
 name:name,
 email:email,
 created:new Date()
}

//Perform the add
var request = store.add(person,1);
```

Remember earlier we said that you can store any data you want (for the most part). So my person object above is completely arbitrary. I could have used firstName and lastName instead of just name. I could have used a gender property. You get the idea. The second argument is the key used to uniquely identify the data. In this case we've hard coded it to 1 which is going to cause a problem pretty quickly. That's ok - we'll learn how to correct it.

The add operation is ascynchronous, so lets add two event handlers for the result.

```
request.onerror = function(e) {
 console.log("Error",e.target.error.name);
 //some type of error handler
}

request.onsuccess = function(e) {
 console.log("Woot! Did it");
}
```

We've got an `onerror` handler for errors and `onsuccess` for good changes. Fairly obvious, but let's see a complete example. You can find this in the file `test6.html`.

> <!doctype html>

```
<body>

<script>
var db;

function indexedDBOk() {
 return "indexedDB" in window;
}

document.addEventListener("DOMContentLoaded", function() {

 //No support? Go in the corner and pout.
 if(!indexedDBOk) return;

 var openRequest = indexedDB.open("idarticle_people",1);

 openRequest.onupgradeneeded = function(e) {
 var thisDB = e.target.result;

 if(!thisDB.objectStoreNames.contains("people")) {
 thisDB.createObjectStore("people");
 }
 }

 openRequest.onsuccess = function(e) {
 console.log("running onsuccess");

 db = e.target.result;

 //Listen for add clicks
 document.querySelector("#addButton").addEventListener("click", addPerson, false);
 }

 openRequest.onerror = function(e) {
 //Do something for the error
 }

},false);

function addPerson(e) {
 var name = document.querySelector("#name").value;
 var email = document.querySelector("#email").value;

 console.log("About to add "+name+"/"+email);

 var transaction = db.transaction(["people"],"readwrite");
 var store = transaction.objectStore("people");

 //Define a person
 var person = {
 name:name,
 email:email,
 created:new Date()
 }

 //Perform the add
 var request = store.add(person,1);

 request.onerror = function(e) {
 console.log("Error",e.target.error.name);
 //some type of error handler
 }

 request.onsuccess = function(e) {
 console.log("Woot! Did it");
 }
}
</script>

<input type="text" id="name" placeholder="Name"><br/>
<input type="email" id="email" placeholder="Email"><br/>
<button id="addButton">Add Data</button>

</body>
</html>
```

The example above contains a small form with a button to fire off an event to store the data in IndexedDB. Run this in your browser, add something to the form fields, and click add. If you've got your browser dev tools open, you should see something like this.

![Data Entry Form](https://cdn.tutsplus.com/net/uploads/2013/09/s2.png)<br>
This is a great time to point out that Chrome has an excellent viewer for IndexedDB data. If you click on the Resources tab, expand the IndexedDB section, you can see the database created by this demo as well as the object just entered.

![Chrome Dev Tools and IndexedDB](https://cdn.tutsplus.com/net/uploads/2013/09/s3.png)<br>
For the heck of it, go ahead and hit that Add Data button again. You should see an error in the console:

![Error adding data again](https://cdn.tutsplus.com/net/uploads/2013/09/s4.png)<br>
The error message should be a clue. ConstraintError means we just tried to add data with the same key as one that already existed. If you remember, we hard coded that key and we _knew_ that was going to be a problem. It's time to talk keys.

## Keys

Keys are IndexedDB's version of primary keys. Traditional databases can have tables without keys, but every object store needs to have a key. IndexedDB allows for a couple different types of keys.

The first option is to simply specify it yourself, like we did above. We could use logic to generate unique keys.

Your second option is a keypath, where the key is based on a property of the data itself. Consider our people example - we could use an email address as a key.

Your third option, and in my opinion, the simplest, is to use a key generator. This works much like an autonumber primary key and is the simplest method of specifying keys.

Keys are defined when object stores are created. Here are two examples - one using a key path and one a generator.

```
thisDb.createObjectStore("test", { keyPath: "email" }); 
thisDb.createObjectStore("test2", { autoIncrement: true });
```

We can modify our previous demo by creating an object store with an autoIncrement key:

```
thisDB.createObjectStore("people", {autoIncrement:true});
```

Finally, we can take the Add call we used before and remove the hard coded key:

```
var request = store.add(person);
```

That's it! Now you can add data all day long. You can find this version in `test7.html`.

## Reading Data

Now let's switch to reading individual pieces of data (we'll cover reading larger sets of data later). Once again, this will be done in a transaction and will be asynchronous. Here's a simple example:

```
var transaction = db.transaction(["test"], "readonly");
var objectStore = transaction.objectStore("test");

//x is some value
var ob = objectStore.get(x);

ob.onsuccess = function(e) {

}
```

Note that the transaction is read only. The API call is just a simple get call with the key passed in. As a quick aside, if you think using IndexedDB is a bit verbose, note you can chain many of those calls as well. Here's the exact same code written much tighter:

```
db.transaction(["test"], "readonly").objectStore("test").get(X).onsuccess = function(e) {}
```

Personally I still find IndexedDB a bit complex so I prefer the 'broken out' approach to help me keep track of what's going on.

The result of the get's onsuccess handler is the object you stored before. Once you have that object you can do whatever you want. In our next demo (`test8.html`) we've added a simple form field to let you enter a key and print the result. Here is an example:

![Fetching data](https://cdn.tutsplus.com/net/uploads/2013/09/s5.png)<br>
The handler for the Get Data button is below:

```
function getPerson(e) {
 var key = document.querySelector("#key").value;
 if(key === "" || isNaN(key)) return;

 var transaction = db.transaction(["people"],"readonly");
 var store = transaction.objectStore("people");

 var request = store.get(Number(key));

 request.onsuccess = function(e) {

 var result = e.target.result;
 console.dir(result);
 if(result) {
 var s = "<h2>Key "+key+"</h2><p>";
 for(var field in result) {
 s+= field+"="+result[field]+"<br/>";
 }
 document.querySelector("#status").innerHTML = s;
 } else {
 document.querySelector("#status").innerHTML = "<h2>No match</h2>";
 } 
 } 
}
```

For the most part, this should be self explanatory. Get the value from the field and run a get call on the object store obtained from a transaction. Notice that the display code simply gets _all_ the fields and dumps them out. In a real application you would (hopefully) know what your data contains and work with specific fields.

## Reading More Data

So that's how you would get one piece of data. How about a _lot_ of data? IndexedDB has support for what's called a cursor. A cursor lets you iterate over data. You can create cursors with an optional range (a basic filter) and a direction.

As an example, the following code block opens a cursor to fetch all the data from an object store. Like everything else we've done with data this is asynchronous and in a transaction.

```
var transaction = db.transaction(["test"], "readonly");
var objectStore = transaction.objectStore("test");

var cursor = objectStore.openCursor();

cursor.onsuccess = function(e) {
 var res = e.target.result;
 if(res) {
 console.log("Key", res.key);
 console.dir("Data", res.value);
 res.continue();
 }
}
```

The success handler is passed a result object (the variable res above). It contains the key, the object for the data (in the value key above), and a continue method that is used to iterate to the next piece of data.

In the following function, we've used a cursor to iterate over all of the objectstore data. Since we're working with "person" data we've called this getPeople:

```
function getPeople(e) {

 var s = "";

 db.transaction(["people"], "readonly").objectStore("people").openCursor().onsuccess = function(e) {
 var cursor = e.target.result;
 if(cursor) {
 s += "<h2>Key "+cursor.key+"</h2><p>";
 for(var field in cursor.value) {
 s+= field+"="+cursor.value[field]+"<br/>";
 }
 s+="</p>";
 cursor.continue();
 }
 document.querySelector("#status2").innerHTML = s;
 }
}
```

You can see a full demo of this in your download as file `test9.html`. It has an Add Person logic as in the earlier examples, so simply create a few people and then hit the button to display all the data.

![List of Data](https://cdn.tutsplus.com/net/uploads/2013/09/s7.png)<br>
So now you know how to get one piece of data as well as how to get all the data. Let's now hit our final topic - working with indexes.

## They Call This IndexedDB, Right?

We've been talking about IndexedDB for the entire article but haven't yet actually done any - well - indexes. Indexes are a crucial part of IndexedDB object stores. They provide a way to fetch data based on their value as well as specifying if a value should be unique within a store. Later we'll demonstrate how to use indexes to get a range of data.

First - how do you create an index? Like everything else structural, they must be done in an upgrade event, basically at the same time you create your object store. Here is an example:

```
var objectStore = thisDb.createObjectStore("people", 
 { autoIncrement:true });
//first arg is name of index, second is the path (col);
objectStore.createIndex("name","name", {unique:false});
objectStore.createIndex("email","email", {unique:true});
```

In the first line we create the store. We take that result (an objectStore object) and run the `createIndex` method. The first argument is the name for the index and the second is the property that will be indexed. In most cases I think you will use the same name for both. The final argument is a set of options. For now, we're just using one, unique. The first index for name is not unique. The second one for email is. When we store data, IndexedDB will check these indexes and ensure that the email property is unique. It will also do some data handling on the back end to ensure we can fetch data by these indexes.

How does that work? Once you fetch an object store via a transaction, you can then ask for an index from that store. Using the code above, here is an example of that:

```
var transaction = db.transaction(["people"],"readonly");
var store = transaction.objectStore("people");
var index = store.index("name");

//name is some value
var request = index.get(name);
```

First we get the transaction, followed by the store, and then index. As we've said before, you could chain those first three lines to make it a bit more compact if you want.

Once you've got an index you can then perform a `get` call on it to fetch data by name. We could do something similar for email as well. The result of that call is yet another asynchronous object you can bind an onsuccess handler to. Here is an example of that handler found in the file `test10.html`:

```
request.onsuccess = function(e) {

 var result = e.target.result;
 if(result) {
 var s = "<h2>Name "+name+"</h2><p>";
 for(var field in result) {
 s+= field+"="+result[field]+"<br/>";
 }
 document.querySelector("#status").innerHTML = s;
 } else {
 document.querySelector("#status").innerHTML = "<h2>No match</h2>";
 } 
}
```

Note that an index `get` call may return multiple objects. Since our name is not unique we should probably modify the code to handle that, but it isn't required.

Now let's kick it up a notch. You've seen using the get API on the index to get a value based on that property. What if you want to get a more broad set of data? The final term we're going to learn today are Ranges. Ranges are a way to select a subset of an index. For example, given an index on a name property, we can use a range to find names that begin with A up to names that begin with C. Ranges come in a few different varieties. They can be "everything below some marker", "everything above some marker", and "something between a lower marker and a higher marker." Finally, just to make things interesting, ranges can be inclusive or exclusive. Basically that means for a range going from A-C, we can specify if we want to include A and C in the range or just the values between them. Finally, you can also request both ascending and descending ranges.

Ranges are created using a toplevel object called IDBKeyRange. It has three methods of interest: `lowerBound`, `upperBound`, and `bound`. `lowerBound` is used to create a range that starts at a lower value and returns all data "above" it. `upperBound` is the opposite. And - finally - `bound` is used to support a set of data with both a lower and upper bound. Let's look at some examples:

```
//Values over 39
var oldRange = IDBKeyRange.lowerBound(39);

//Values 40a dn over
var oldRange2 = IDBKeyRange.lowerBound(40,true);

//39 and smaller...
var youngRange = IDBKeyRange.upperBound(40);

//39 and smaller...
var youngRange2 = IDBKeyRange.upperBound(39,true);

//not young or old... you can also specify inclusive/exclusive
var okRange = IDBKeyRange.bound(20,40)
```

Once you have a range, you can pass it to an index's openCursor method. This gives you an iterator to loop over the values that match that range. As a practical manner, this isn't really a search per se. You can use this to search content based on the beginning of a string, but not the middle or end. Let's look at a full example. First we'll create a simple form to search people:

```
Starting with: <input type="text" id="nameSearch" placeholder="Name"><br/>
Ending with: <input type="text" id="nameSearchEnd" placeholder="Name"><br/>
<button id="getButton">Get By Name Range</button>
```

We're going to allow for searches that consist of any of the three types of ranges (again, a value and higher, a highest value, or the values within two inputs). Now let's look at the event handler for this form.

```
function getPeople(e) {
 var name = document.querySelector("#nameSearch").value;

 var endname = document.querySelector("#nameSearchEnd").value;

 if(name == "" && endname == "") return;

 var transaction = db.transaction(["people"],"readonly");
 var store = transaction.objectStore("people");
 var index = store.index("name");

 //Make the range depending on what type we are doing
 var range;
 if(name != "" && endname != "") {
 range = IDBKeyRange.bound(name, endname);
 } else if(name == "") {
 range = IDBKeyRange.upperBound(endname);
 } else {
 range = IDBKeyRange.lowerBound(name);
 }

 var s = "";

 index.openCursor(range).onsuccess = function(e) {
 var cursor = e.target.result;
 if(cursor) {
 s += "<h2>Key "+cursor.key+"</h2><p>";
 for(var field in cursor.value) {
 s+= field+"="+cursor.value[field]+"<br/>";
 }
 s+="</p>";
 cursor.continue();
 }
 document.querySelector("#status").innerHTML = s;
 }

}
```

From top to bottom - we begin by grabbing the two form fields. Next we create a transaction and from that get the store and index. Now for the semi-complex part. Since we have three different types of ranges we need to support we have to do a bit of conditional logic to figure out which we'll need. What range we create is based on what fields you fill in. What's nice is that once we have the range, we then simply pass it to the index and open the cursor. That's it! You can find this full example in `test11.html`. Be sure to enter some values first so you have data to search.

## What's Next?

Believe it or not - we've only begun our discussion on IndexedDB. In the next article, we'll cover additional topics, including updates and deletes, array based values, and some general tips for working with IndexedDB.
