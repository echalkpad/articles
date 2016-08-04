
[Original URL](https://blog.risingstack.com/node-hero-node-js-debugging-tutorial/)

> This article is the 10th part of the tutorial series called Node Hero - in these chapters, you can learn how to get started with Node.js and deliver software products using it. In this tutorial, you...

This article is the 10th part of the tutorial series called Node Hero - in these chapters, you can learn how to get started with Node.js and deliver software products using it.

**In this tutorial, you are going to learn debugging your Node.js applications using the debug module, the built-in Node debugger and Chrome's developer tools.**

Upcoming and past chapters:

## Bugs, debugging

The term **bug** and **debugging** have been a part of engineering jargon for many decades. One of the first written mentions of bugs is as follows:

> It has been just so in all of my inventions. The first step is an intuition, and comes with a burst, then difficulties arise -- this thing gives out and [it is] then that "Bugs" -- as such little faults and difficulties are called -- show themselves and months of intense watching, study and labor are requisite before commercial success or failure is certainly reached.

> **_Thomas Edison_**

## Debugging Node.js Applications

One of the most frequently used approach to find issues in Node.js applications is the heavy usage of `console.log` for debugging.

Let's take a look at them!

### The `debug` module

Some of the most popular modules that you can `require` into your project come with the `debug` module. With this module, you can enable third-party modules to log to the standard output, `stdout`. To check whether a module is using it, take a look at the `package.json` file's dependency section.

To use the `debug` module, you have to set the `DEBUG` environment variable when starting your applications. You can also use the `*` character to wildcard names. The following line will print all the `express` related logs to the standard output.

```
DEBUG=express* node app.js 
```

The output will look like this:

![Logfile made with the Node debug module](https://risingstack-blog.s3.amazonaws.com/2016/Jun/logfile_made_with_the_node_debug_module-1467025068908.png)

### The Built-in Node.js Debugger

> Node.js includes a full-featured out-of-process debugging utility accessible via a simple TCP-based protocol and built-in debugging client.

To start the built-in debugger you have to start your application this way:

```
node debug app.js 
```

Once you have done that, you will see something like this:

![using the built-in node.js debugger](https://risingstack-blog.s3.amazonaws.com/2016/Jun/using_the_built_in_node_js_debugger-1467025131358.png)

#### Basic Usage of the Node Debugger

**To navigate this interface, you can use the following commands:**

- `c` => continue with code execution
- `n` => execute this line and go to next line
- `s` => step into this function
- `o` => finish function execution and step out
- `repl` => allows code to be evaluated remotely

You can add breakpoints to your applications by inserting the `debugger` statement into your codebase.

```
function add (a, b) { 
 debugger
 return a + b
}

var res = add('apple', 4) 
```

#### Watchers

> It is possible to watch expression and variable values during debugging. On every breakpoint, each expression from the watchers list will be evaluated in the current context and displayed immediately before the breakpoint's source code listing.

To start using watchers, you have to define them for the expressions you want to watch. To do so, you have to do it this way:

```
watch('expression') 
```

To get a list of active watchers type `watchers`, to unwatch an expression use `unwatch('expression')`.

> **Pro tip**: you can switch running Node.js processes into debug mode by sending the `SIGUSR1` command to them. After that you can connect the debugger with `node debug -p <pid>`.

_To understand the full capabilities of the built-in debugger, check out the official API docs: <https://nodejs.org/api/debugger.html>._

### The Chrome Debugger

When you start debugging complex applications, something visual can help. Wouldn't be great to use the familiar UI of the Chrome DevTools for debugging Node.js applications as well?

![node.js chrome developer tools debugging](https://risingstack-blog.s3.amazonaws.com/2016/Jun/node_js_chrome_developer_tools_debugging-1467026261217.png)

Good news, the Chrome debug protocol is already ported into a Node.js module and can be used to debug Node.js applications.

To start using it, you have to install `node-inspector` first:

```
npm install -g node-inspector 
```

Once you installed it, you can start debugging your applications by starting them this way:

```
node-debug index.js --debug-brk 
```

_(the `--debug-brk` pauses the execution on the first line)_

It will open up the Chrome Developer tools and you can start to debug your Node.js applications with it.

## Next up

Debugging is not that hard after all, is it?

In the next chapter of Node Hero, you are going to learn how to secure your Node.js applications.

If you have any questions or recommendations for this topic, write them in the comments section.

Get early access to our posts
