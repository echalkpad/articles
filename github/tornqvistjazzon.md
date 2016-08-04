# tornqvist/jazzon

[Original URL](https://github.com/tornqvist/jazzon)

> Working with static JSON files for mocking data or whatever other purposes can be a real bore. Jazzon is a convenience utility for generating, concatenating and streamlining the handling of static...

Working with static JSON files for mocking data or whatever other purposes can be a real bore. Jazzon is a convenience utility for generating, concatenating and streamlining the handling of static JSON files.

## [](https://github.com/tornqvist/jazzon#installation)Installation

```
$ npm install --save jazzon
```

## [](https://github.com/tornqvist/jazzon#usage)Usage

In and of it self jazzon does nothing, _really_. It's sole purpose is to call registered plugins with the current state and identified helpers. Helpers can be chained passing the current state from one to the next.

To illustrate a most basic scenario, this is how one might use jazzon together with [jazzon-uuid](https://github.com/tornqvist/jazzon-uuid)

```
const jazzon = require('jazzon');
const uuid = require('jazzon-uuid');

let data = { id: "@{ uuid }" };

jazzon.use(uuid());

jazzon
 .compile(data)
 .then(result => console.log(result)); // => {id: "6c84fb90-12c4-11e1-840d-7b25c5ee775a"}
```

In this scenario, jazzon encounters the helper `uuid` and calls each registered plugin (in this case `jazzon-uuid`) on it.

Helpers can also be chained using the pipe (`|`) symbol. Each chained helper gets the output (state) of the previous helper to operate on. To illustrate a more complex scenario, take these two models:

```
// user.json

{
 "id": "@{ uuid }",
 "name": "@{ name.findName }",
 "email": "@{ internet.email }",
 "username": "@{ internet.userName }"
}

// users.json

{
 "total": 3,
 "users": "@{ import(user.json) | pick(id, username) | repeat(3) }"
}
```

Running `users.json` through jazzon would produce something like this:

```
{
 "total": 3,
 "users": [{
 "id": "a76f535f-cbc6-4c09-8151-573e200c1dbf",
 "username": "Doug.Simonis28"
 }, {
 "id": "0a512648-c418-40a6-90ac-1bb5ef1e7fab",
 "username": "Virgil_Kunze"
 }, {
 "id": "88d6903f-d13b-4d16-877e-f906461c69aa",
 "username": "Grady.Koelpin"
 }]
}
```

## [](https://github.com/tornqvist/jazzon#syntax)Syntax

The syntax of helpers are very similar to [JavaScript template strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings) to clearly illustrate their purpose. But do not confuse them as the pipe separator is not a valid JavaScript operator.

The string must start with `@{` and end with `}`. Each helper is separated with a `|` symbol. Encountering an invalid template-ish string will throw an error.

Use [this regexr](http://regexr.com/3bsnl) to experiment with the template strings.

## [](https://github.com/tornqvist/jazzon#plugins)Plugins

Plugins should export a function that get's called once for every helper encountered by jazzon. The convention is to export a factory function that returns the plugin.

A plugin really is just a reducer that jazzon uses to process all the helpers. Therefore a plugin should _always_ return a state, even if it does not manipulate the state. A switch statement does the job as so:

```
// myplugin.js

module.exports = function (otions) {
 return function (state, helper, args) {
 switch (helper) {
 case 'name':
 return args[0] || options.default;
 case 'wrap':
 return `Hello ${ state }!`;
 default:
 return state;
 }
 }
};

// myprogram.js

jazzon
 .use(myplugin({
 default: 'world'
 }))
 .compile({
 "first": "@{ name | wrap }",
 "second": "@{ name(Joe) | wrap }"
 })
 .then(result => console.log(result)); // => {"first": "Hello world!", "second": "Hello Joe!"}
```

Jazzon also supports async plugins. Under the hood, jazzon is using [co](https://github.com/tj/co) so anything that co can handle, jazzon can handle. As so, other than just plain strings, a plugin may return a Promise, generator, generator function, function, object or array. Due to the awesome nature of co, objects and arrays may contain nestled objects/arrays containing Promises or any of the other supported types. See some of the plugins for examples of how this is achieved.

### [](https://github.com/tornqvist/jazzon#availible-plugins)Availible plugins

To add your own plugin, add it to the list and make a pull request.

## [](https://github.com/tornqvist/jazzon#api)API

- `jazon.create(/*plugins*/)` Creates a new instance of jazzon with optional list of plugins.
- `jazzon.use(plugin)` Adds a plugin to be used when transforming template strings.
- `jazzon.compile(object)` Iterates over the object looking for template strings to transform.
- `jazzon.plugins` An list of registered helpers on this instance.

  - _Returns an Array_
  - _Is immutable_

## [](https://github.com/tornqvist/jazzon#todo)TODO

- Add documentation
- Add wrapper for non-Promises returned from plugins
- Add test for plugins (generator/non-generator)
- Better error handling
- Add CLI
