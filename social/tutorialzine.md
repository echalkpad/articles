# tutorialzine

[Original URL](http://tutorialzine.com/2014/09/creating-your-first-node-js-command-line-application/)

> Martin Angelov September 25th, 2014 Command-line utilities are a must for every seasoned web developer. Node.js makes developing such tools extremely easy thanks to a wealth of open source modules...

**Martin Angelov** September 25th, 2014

Command-line utilities are a must for every seasoned web developer. Node.js makes developing such tools extremely easy thanks to a wealth of open source modules and a great package manager.

In this tutorial, we will convert our [Cute File Browser](http://tutorialzine.com/2014/09/cute-file-browser-jquery-ajax-php/ "Cute File Browser with jQuery and PHP"), which we published last week, from PHP to Node. Our goal is to create a command line utility that users can start on their machine and make any folder available on the local network. This can be used in a home or office setting to quickly share a folder with files to everybody on the same network.

## Quick start

The source code is available on [github](https://github.com/martinaglv/cute-files) and [npm](https://www.npmjs.org/package/cute-files). If you don't want to follow this tutorial and only want to play with it, you can install it directly with this command:

```
npm install -g cute-files
```

Then, navigate to a folder you wish to share in your terminal, and execute the command you just installed:

```
cute-files
```

Now that folder will be available on `<yourip>:3000` and will be accessible by anyone in your lan.

[![Cute file browser](http://cdn.tutorialzine.com/wp-content/uploads/2014/09/file-and-folder-grid.jpg)](http://cdn.tutorialzine.com/wp-content/uploads/2014/09/file-and-folder-grid.jpg) Cute file browser (last week's demo, but the frontend is the same)

## Let's build it step by step

Command line utilities are just regular node programs. They are available globally and can be called from any folder. So the steps for creating our command-line application are nearly identical to any other node-based project that you might have developed before. Create a new folder for your project and navigate to it in your terminal. We will refer to it as your **project folder**.

### Setting up package.json

Assuming that you have already installed [node](http://nodejs.org/), we must first create a **package.json** file. The npm utility can help you with that. Run this command in your project folder:

```
npm init
```

You will be prompted for information about your project. If you are not sure what to type just hit return for the default values. Fill in **cute-files.js** as the entry point. This will be the main file, which will be called by node. The package.json is not yet complete – we want to make our script available globally, so we need to to add an extra field – **bin** (see [my package.json](https://github.com/martinaglv/cute-files/blob/master/package.json#L29) as an example). This tells npm to make these scripts available as executables.

### Installing some packages

Now you will need to install some libraries that we need for the file browser. The **–save** flag will add them to your package.json automatically:

```
npm install commander content-disposition express --save
```

Here is what we will use them for:

- [commander](https://www.npmjs.org/package/commander) will parse command line arguments. We will only support one – for the **port**, but since attribute parsing is a fundamental part of writing command line apps it is good to know how to use this library.
- [content-disposition](https://www.npmjs.org/package/content-disposition) will tell us the correct attachment headers for each file. These headers are needed in order to force the files to get downloaded (otherwise the browser would just open them).
- [express](https://www.npmjs.org/package/express) will serve the files and handle the **/scan** route which sends a JSON of all files and directories to the frontend.

### Module for scanning files

In order to offer instant search and browsing, our application will send a JSON with all files and folder to the frontend on load. In the [original tutorial](http://tutorialzine.com/2014/09/cute-file-browser-jquery-ajax-php/ "Cute File Browser with jQuery and PHP"), we did this with PHP, but now we will do it with Node. Create a new file in the project folder – **scan.js** – and paste the following code:

```
var fs = require('fs'),
    path = require('path');


module.exports = function scan(dir, alias){

    return {
        name: alias,
        type: 'folder',
        path: alias,
        items: walk(dir, alias)
    };

};


function walk(dir, prefix){

    prefix = prefix || '';

    if(!fs.existsSync(dir)){
        return [];
    }

    return fs.readdirSync(dir).filter(function(f){

        return f && f[0] != '.'; // Ignore hidden files

    }).map(function(f){

        var p = path.join(dir, f),
            stat = fs.statSync(p);

        if(stat.isDirectory()){

            return {
                name: f,
                type: 'folder',
                path: path.join(prefix, p),
                items: walk(p, prefix)
            };

        }

        return {
            name: f,
            type: 'file',
            path: path.join(prefix, p),
            size: stat.size
        }

    });

};
```

This code uses node's [fs module](http://nodejs.org/api/fs.html), and recursively goes through all files and folders in a directory. It exports the **scan** function which gets called with a path and an alias. The path is the folder to be scanned, and the alias is the name with which it will be presented in the interface (we don't want to show the real directory name which is served). In our example, we replace it with "files".

### Main module

The main file of our app is **cute-files.js**. Create the file, and paste the following code:

```
#!/usr/bin/env node

var path = require('path');
var express = require('express');
var contentDisposition = require('content-disposition');
var pkg = require( path.join(__dirname, 'package.json') );

var scan = require('./scan');


// Parse command line options

var program = require('commander');

program
    .version(pkg.version)
    .option('-p, --port <port>', 'Port on which to listen to (defaults to 3000)', parseInt)
    .parse(process.argv);

var port = program.port || 3000;


// Scan the directory in which the script was called. It will
// add the 'files/' prefix to all files and folders, so that
// download links point to our /files route

var tree = scan('.', 'files');


// Ceate a new express app

var app = express();

// Serve static files from the frontend folder

app.use('/', express.static(path.join(__dirname, 'frontend')));

// Serve files from the current directory under the /files route

app.use('/files', express.static(process.cwd(), {
    index: false,
    setHeaders: function(res, path){

        // Set header to force files to download

        res.setHeader('Content-Disposition', contentDisposition(path))

    }
}));

// This endpoint is requested by our frontend JS

app.get('/scan', function(req,res){
    res.send(tree);
});


// Everything is setup. Listen on the port.

app.listen(port);

console.log('Cute files is running on port ' + port);
```

The first line is important. Although it is not valid JS, it is used on *nix systems to allow scripts to be executed as programs, which is exactly what we need in order to make the **cute-files** command available anywhere.

This script uses **express** and the other modules we installed previously, and our scan.js module. All that is left is to copy over the front end from the original tutorial.

### The frontend

Nick already did most of the work in last week's tutorial. Our scan.js script returns the same output as scan.php did, so no modifications to the frontend are necessary. We only need to copy over **index.html** and the **assets** folder from last time to a new folder – **frontend**, which will be served by express. You can see how your directories should look like from [the repo](https://github.com/martinaglv/cute-files).

### Linking the module

Your new module is ready! It should work when you run **node cute-files.js** in the project folder. But this is not very useful, as it will only serve the project folder – we want to be able to call the **cute-files** command from any directory, but it is not available unless you install the module globally. npm has a useful command which will help us. Run this in your terminal from the project folder:

```
npm link
```

This will make the module available globally, so now you can use the **cute-files** command anywhere!

## Publishing to npm

Where is the fun of writing a module and not sharing it with your friends? It is straightforward to publish your new module on npm (however I advice against doing it for this specific module – it will only be duplicate of cute-files):

1. Create an account on [npm's website](https://www.npmjs.org/)
2. Login from the npm command line utility with the `npm login` command
3. Choose a unique name for your module, and update package.json
4. cd into the project folder and run `npm publish .`

If everything went without a hitch, in a few seconds you will see your module on npm's website and everybody will be able to install it.

**Good job! You just created your first command-line application with node!**
