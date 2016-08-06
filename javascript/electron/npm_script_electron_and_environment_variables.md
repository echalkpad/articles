# Npm script, Electron and environment variables

[Original URL](http://electron.rocks/npm-script-electron-and-environment-variables/)

> In app: var isDev = process.env.TODO_DEV ? process.env.TODO_DEV.trim() == "true"; In package.json let's say we have: "start": "electron . –enable-logging –remote-debugging-port=9222" With cross-env...

In app:

```
var isDev = process.env.TODO_DEV ? process.env.TODO_DEV.trim() == "true"; 
```

In `package.json` let's say we have:

```
"start": "electron . --enable-logging --remote-debugging-port=9222"
```

With [cross-env](https://www.npmjs.com/package/cross-env) package:

```
"dev": cross-env TODO_DEV=true npm run start
```

or without dependencies:

```
"dev:mac": TODO_DEV=true npm run start,
"dev:win": SET TODO_DEV=true && npm run start
```

If you're working with npm scripts (or planning to) and passing environment variables to Electron app, you have to be aware of the platform that you're working on.

Let's try to pass development flag `TODO_DEV` to our sample TODO application. In out app we'll get value from environment variable and see if it is `true`.

```
var isDev = process.env.TODO_DEV == "true"; 
```

On Mac OS it would look like this:

```
"dev:mac": TODO_DEV=true npm run start
```

On Windows you have to use `SET` as a separate command and chain it.

```
"dev:win": SET TODO_DEV=true && npm run start
```

But this **will NOT** work. And it is because `process.env.TODO_DEV` value is not "`true`" but "`true`". So you can either remove space from your script...

```
"dev:win": SET TODO_DEV=true&& npm run start
```

...or (IMHO) better, modify your app code to trim whitespace. Be sure to check if value exist before:

```
var isDev = process.env.TODO_DEV ? (process.env.TODO_DEV.trim() == "true") : false; 
```

If you don't mind dependency and want to have things clean, grab [cross-env](https://www.npmjs.com/package/cross-env) package and use it as a single target:

```
"dev": cross-env TODO_DEV=true npm run start
```

_Thanks William for letting me know about cross-env._

Cheers!
