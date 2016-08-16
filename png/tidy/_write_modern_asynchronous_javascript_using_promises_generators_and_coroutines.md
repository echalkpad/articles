# Write Modern Asynchronous Javascript Using Promises, Generators, and Coroutines

[Original URL](http://blog.originate.com/blog/2016/06/29/write-modern-asynchronous-javascript-using-promises-generators-and-coroutines/)

> Over the years, "Callback Hell" has been cited as one of the most common anti-patterns in Javascript to manage concurrency. Just in case you've forgotten what that looks like,...

Over the years, "Callback Hell" has been cited as one of the most common anti-patterns in Javascript to manage concurrency. Just in case you've forgotten what that looks like, here is an example of verifying and processing a transaction in Express:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14``` | ```
app.post("/purchase", (req, res) => {
 user.findOne(req.body, (err, userData) => {
 if (err) return handleError(err);
 permissions.findAll(userData, (err2, permissions) => {
 if (err2) return handleError(err2);
 if (isAllowed(permissions)) {
 transaction.process(userData, (err3, confirmNum) => {
 if (err3) return handleError(err3);
 res.send("Your purchase was successful!");
 });
 }
 });
 });
});```
--------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Promises were supposed to save us...

I was told that promises would allow us Javascript developers to write asynchronous code as if it were synchronous by wrapping our async functions in a special object. In order to access the value of the Promise, we call either `.then` or `.catch` on the Promise object. But what happens when we try to refactor the above example using Promises?

```
1
2
3
4
5
6
7
8
9
10
11
12
13``` | ```
// all asynchronous methods have been promisified
app.post("/purchase", (req, res) => {
 user.findOneAsync(req.body)
 .then( userData => permissions.findAllAsync(userData) )
 .then( permissions => {
 if (isAllowed(permissions)) {
 return transaction.processAsync(userData);
 // userData is not defined! It's not in the proper scope!
 }
 })
 .then( confirmNum => res.send("Your purchase was successful!") )
 .catch( err => handleError(err) )
});```
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Since each function inside of the callback has its own scope, we cannot access the user object inside of the second `.then` callback. So after a little digging, I couldn't find an elegant solution, but I did find a frustrating one:

> Just indent your promises so that they have proper scoping.

Indent my promises!? So its back to the Pyramid of Doom now?

```
1
2
3
4
5
6
7
8
9
10
11
12
13``` | ```
app.post("/purchase", (req, res) => {
 user.findOneAsync(req.body)
 .then( userData => {
 return permissions
 .findAllAsync(userData)
 .then( permissions => {
 if (isAllowed(permissions)) {
 return transaction.processAsync(userData);
 }
 });
 }).then( confirmNum => res.send("Your purchase was successful!"))
 .catch( err => handleError(err) )
});```
------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

I would argue that the nested callback version looks cleaner and is easier to reason about than the nested promise version.

## Async Await Will Save Us!

The `async` and `await` keywords will finally allow us to write our javascript code as though it is synchronous. Here is code written using those keywords coming in ES7:

```
1
2
3
4
5
6
7
8``` | ```
app.post("/purchase", async function (req, res) {
 const userData = await user.findOneAsync(req.body);
 const permissions = await permissions.findAllAsync(userData);
 if (isAllowed(permissions)) {
 const confirmNum = await transaction.processAsync(userData);
 res.send("Your purchase was successful!")
 }
});```
---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Unfortunately the majority of ES7 features including `async/await` have not been implemented in Javascript runtimes and therefore, require the use of a transpiler. However, you can write code that looks exactly like the code above using ES6 features that have been implemented in most modern browsers as well as Node version 4+.

## The Dynamic Duo: Generators and Coroutines

Generators are a great metaprogramming tool. They can be used for things like lazy evaluation, iterating over memory intensive data sets and on demand data processing from multiple data sources using a library like [RxJs](https://github.com/Reactive-Extensions/RxJS). However, we wouldn't want to use generators alone in production code because it forces us to reason about a process over time and each time we call next, we jump back to our generator like a GOTO statement. Coroutines understand this and remedy this situation by wrapping a generator and abstracting away all of the complexity.

## The ES6 version using Coroutines

Coroutines allow us to use `yield` to execute our asynchronous functions line by line, making our code look synchronous. It's important to note that I am using the Co library. Co's coroutine will execute the generator immediately where as Bluebird's coroutine will return a function that you must invoke to run the generator.

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
import co from 'co';
app.post("/purchase", (req, res) => {
 co(function* () {
 const person = yield user.findOneAsync(req.body);
 const permissions = yield permissions.findAllAsync(person);
 if (isAllowed(permissions)) {
 const confirmNum = yield transaction.processAsync(user);
 res.send("Your transaction was successful!")
 }
 }).catch(err => handleError(err))
});```
------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If there is an error at any step in the generator, the coroutine will stop execution and return a rejected promise. Let's establish some basic rules to using coroutines:

- Any function to the right of a yield must return a Promise.
- If you want to execute your code now, use `co`.
- If you want to execute your code later, use `co.wrap`.
- Make sure to chain a `.catch` at the end of your coroutine to handle errors. Otherwise, you should wrap your code in a try/catch block.
- Bluebird's `Promise.coroutine` is the equivalent to Co's `co.wrap` and not `co` on it's own.

## What if I want to run multiple processes concurrently?

You can either use objects or arrays with the yield keyword and then destructure the result.

With the Co library:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18``` | ```
import co from 'co';
// with objects
co(function_() { const {user1, user2, user3} = yield { user1: user.findOneAsync({name: "Will"}), user2: user.findOneAsync({name: "Adam"}), user3: user.findOneAsync({name: "Ben"}) }; ).catch(err => handleError(err)) // with arrays co(function_() {
 const [user1, user2, user3] = yield [
 user.findOneAsync({name: "Will"}),
 user.findOneAsync({name: "Adam"}),
 user.findOneAsync({name: "Ben"})
 ];
).catch(err => handleError(err))```
--------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

With the Bluebird library:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20``` | ```
// with the Bluebird library
import {props, all, coroutine} from 'bluebird';
// with objects
coroutine(function_() { const {user1, user2, user3} = yield props({ user1: user.findOneAsync({name: "Will"}), user2: user.findOneAsync({name: "Adam"}), user3: user.findOneAsync({name: "Ben"}) }); )().catch(err => handleError(err)) // with arrays coroutine(function_() {
 const [user1, user2, user3] = yield all([
 user.findOneAsync({name: "Will"}),
 user.findOneAsync({name: "Adam"}),
 user.findOneAsync({name: "Ben"})
 ]);
)().catch(err => handleError(err))```
--------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Libraries that you can use today:

- - [Bluebird](http://bluebirdjs.com/docs/api/promise.coroutine.html)

- [Babel](https://babeljs.io/)

- [Asyncawait](https://www.npmjs.com/package/asyncawait)
