# Build a JavaScript Command Line Interface (CLI) with Node.js

[Original URL](https://www.sitepoint.com/javascript-command-line-interface-cli-node-js/)

> This article was peer reviewed by Dan Prince. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! As great as Node.js is for...


This article was peer reviewed by [Dan Prince](https://www.sitepoint.com/author/dprince). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!

As great as Node.js is for "traditional" web applications, its potential uses are far broader. Microservices, REST APIs, tooling, working with the Internet of Things and even desktop applications--it's got your back.

Another area where Node.js is really useful is for building command-line applications--and that's what we're going to be doing today. We're going to start by looking at a number of third-party packages designed to help work with the command-line, then build a real-world example from scratch.

What we're going to build is a tool for initializing a Git repository. Sure, it'll run `git init` under the hood, but it'll do more than just that. It will also create a remote repository on Github right from the command line, allow the user to interactively create a `.gitignore` file and finally perform an initial commit and push.

As ever, the code accompanying this tutorial can be found on our [GitHub repo](https://github.com/sitepoint-editors/ginit).

## Why Build a Command-line Tool with Node.js?

Before we dive in and start building, it's worth looking at why we might choose Node.js to build a command-line application.

The most obvious advantage is that if you're reading this, you're probably already familiar with it--and indeed, with JavaScript.

Another key advantage, as we'll see as we go along, is that the strong Node.js ecosystem means that among the hundreds of thousands of packages available for all manner of purposes, there are a number which are specifically designed to help build powerful command-line tools.

Finally, we can use `npm` to manage any dependencies, rather than have to worry about OS-specific package managers such as Aptitude, Yum or Homebrew.

That said, that's not necessarily true, in that your command-line tool may have other external dependencies.

## What We're Going to Build--Introducing ginit

![Ginit, our Node CLI in action](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467718020Ginit-in-action.png)

For this tutorial, We're going to create a command-line utility which I'm calling **ginit**. It's `git init`, but on steroids.

You're probably wondering what on earth that means.

As you no doubt already know, `git init` initializes a git repository in the current folder. However, that's usually only one of a number of repetitive steps involved in the process of hooking up a new or existing project to Git. For example, as part of a typical workflow, you might well:

1. Initialise the local repository by running `git init`
2. Create a remote repository, for example on Github or Bitbucket; typically by leaving the command-line and firing up a web browser
3. Add the remote
4. Create a `.gitignore` file
5. Add your project files
6. Commit the initial set of files
7. Push up to the remote repository

There are often more steps involved, but we'll stick to those for the purposes of our app. Nevertheless, these steps are pretty repetitive. Wouldn't it be better if we could do all this from the command-line, with no copying-and-pasting of Git URLs and such-like?

So what ginit will do is create a Git repository in the current folder, create a remote repository--we'll be using Github for this--and then add it as a remote. Then it will provide a simple interactive "wizard" for creating a `.gitignore` file, add the contents of the folder and push it up to the remote repository. It might not save you hours, but it'll remove some of the initial friction when starting a new project.

With that in mind, let's get started.

## The Application Dependencies

One thing is for certain--in terms of appearence, the console will never have the sophistication of a graphical user interface. Nevertheless, that doesn't mean it has to be plain, ugly, monochrome text. You might be surprised by just how much you can do visually, while at the same time keeping it functional. We'll be looking at a couple of libraries for enhancing the display: [chalk](https://www.npmjs.com/package/chalk) for colorizing the output and [clui](https://www.npmjs.com/package/clui) to add some additional visual components. Just for fun, we'll use [figlet](https://www.npmjs.com/package/clui) to create a fancy ASCII-based banner and we'll also use [clear](https://www.npmjs.com/package/clear) to clear the console.

In terms of input and output, the low-level [Readline](https://nodejs.org/api/readline.html) Node.js module could be used to prompt the user and request input, and in simple cases is more than adequate. But we're going to take advantage of a third-party package which adds a greater degree of sophistication--[Inquirer](https://www.npmjs.com/package/inquirer). As well as providing a mechanism for asking questions, it also implements simple input controls; think radio buttons and checkboxes, but in the console.

We'll also be using [minimist](https://www.npmjs.com/package/minimist) to parse command-line arguments.

Here's a complete list of the packages we'll use specifically for developing on the command-line:

- [chalk](https://www.npmjs.com/package/chalk) – colorizes the output
- [clear](https://www.npmjs.com/package/clear) – clears the terminal screen
- [clui](https://www.npmjs.com/package/clui) – draws command line tables, gauges and spinners
- [figlet](https://www.npmjs.com/package/figlet) – creates ASCII art from text
- [inquirer](https://www.npmjs.com/package/inquirer) – creates interactive command line user interface
- [minimist](https://www.npmjs.com/package/minimist) – parses argument options
- [preferences](https://www.npmjs.com/package/preferences) – manage CLI application encrypted preferences

Additionally, we'll also be using the following:

- [github](https://www.npmjs.com/package/github) – Node wrapper for the GitHub API
- [lodash](https://www.npmjs.com/package/lodash) – JavaScript utility library
- [simple-git](https://www.npmjs.com/package/simple-git) – runs Git commands in a Node.js application
- [touch](https://www.npmjs.com/package/touch) – implementation of the *Nix touch command

## Getting Started

Although we're going to create the application from scratch, don't forget that you can also grab a copy of the code from the [repository which accompanies this article](https://github.com/sitepoint-editors/ginit).

Create a new directory for the project. You don't have to call it `ginit`, of course.

```sh
mkdir ginit
cd ginit
```

Create a new `package.json` file:

```sh
npm init
```

Follow the simple wizard, for example:

```
name: (ginit)
version: (1.0.0)
description: "git init" on steroids
entry point: (index.js)
test command:
git repository:
keywords: Git CLI
author: [YOUR NAME]
license: (ISC)
```

Now install the depenencies:

```sh
npm install chalk clear clui figlet inquirer minimist preferences github lodash simple-git touch --save
```

Alternatively, simply copy-and-paste the following `package.json` file--modifying the `author` appropriately--or grab it from the [repository which accompanies this article](https://github.com/sitepoint-editors/ginit/blob/master/package.json):

```json
{
  "name": "ginit",
  "version": "1.0.0",
  "description": "\"git init\" on steroids",
  "main": "index.js",
  "keywords": [
    "Git",
    "CLI"
  ],
  "author": "Lukas White <hello@lukaswhite.com>",
  "license": "ISC",
  "dependencies": {
    "chalk": "^1.1.3",
    "clear": "0.0.1",
    "clui": "^0.3.1",
    "figlet": "^1.1.2",
    "github": "^2.1.0",
    "inquirer": "^1.1.0",
    "lodash": "^4.13.1",
    "minimist": "^1.2.0",
    "preferences": "^0.2.1",
    "simple-git": "^1.40.0",
    "touch": "^1.0.0"
  }
}
```

Now create an `index.js` file in the same folder and `require` all of the dependencies:

```js
var chalk = require('chalk');
var clear = require('clear');
var CLI = require('clui');
var figlet = require('figlet');
var inquirer = require('inquirer');
var Preferences = require('preferences');
var Spinner = CLI.Spinner;
var GitHubApi = require('github');
var _ = require('lodash');
var git = require('simple-git')();
var touch = require('touch');
var fs = require('fs');
```

Note that the simple-git package exports a function which needs to be called.

## Adding Some Helper Methods

In the course of the application, we'll need to do the following:

- Get the current directory (to get a default repo name)
- Check whether a directory exists (to determine whether the current folder is already a Git repository by looking for a folder named `.git`).

This sounds straight forward, but there are a couple of gotchyas to take into consideration.

Firstly, you might be tempted to use the `fs` module's [realpathSync](https://nodejs.org/api/fs.html#fs_fs_realpathsync_path_options) method to get the current directory:

```js
path.basename(path.dirname(fs.realpathSync(__filename)));
```

This will work when we are calling the application from the same directory (e.g. using `node index.js`), but bear in mind that we're going to be making our console application available globally. This means we'll want the name of the directory we're working in, not the directory where the application resides. For this purpose it's better to use [process.cwd](https://nodejs.org/api/process.html#process_process_cwd):

```js
path.basename(process.cwd());
```

Secondly, the preferred method of checking whether a file or directory exists [keeps changing](http://stackoverflow.com/a/4482701)--the current way is to use `fs.stat` / `fs.statSync`. These throw an error if there's no file, so we need to use a `try..catch` block.

Finally, it's worth noting that when you're writing a command line application, using the synchronous version of these sorts of methods is just fine.

Putting that all together, let's create a utilty package in `lib/files.js`:

```js
var fs = require('fs');
var path = require('path');

module.exports = {
 getCurrentDirectoryBase : function() {
 return path.basename(process.cwd());
 },

 directoryExists : function(filePath) {
 try {
 return fs.statSync(filePath).isDirectory();
 } catch (err) {
 return false;
 }
 }
};
```

Go back to `index.js` and ensure you `require` the new file:

```js
var files = require('./lib/files');
```

With this in place, we can start developing the application.

## Initializing the Node CLI

Now let's implement the start-up phase of our console application.

In order to demonstrate some of the packages we've installed to enhance the console output, let's clear the screen and then display a banner:

```js
clear();
console.log(
 chalk.yellow(
 figlet.textSync('Ginit', { horizontalLayout: 'full' })
 )
);
```

The output from this is shown below.

![The welcome banner on our Node CLI, created using Chalk and Figlet](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467718857Ginit-logo.png)

Next up, let's run a simple check to ensure that the current folder isn't already a Git repository. That's easy--we just check for the existence of a `.git` folder using the utility method we just created:

```js
if (files.directoryExists('.git')) {
 console.log(chalk.red('Already a git repository!'));
 process.exit();
}
```

Notice we're using the [chalk module](https://www.npmjs.com/package/chalk) to show a red-colored message.

## Prompting the User for Input

The next thing we need to do is create a function which will prompt the user for their Github credentials.

We can use [Inquirer](https://www.npmjs.com/package/inquirer) for this. The module includes a number of methods for various types of prompts, which are roughly analogous to HTML form controls. In order to collect the user's Github username and password, we're going to use the `input` and `password` types respectively. Here is the code:

```js
function getGithubCredentials(callback) {
  var questions = [
  {
    name: 'username',
    type: 'input',
    message: 'Enter your Github username or e-mail address:',
    validate: function( value ) {
      if (value.length) {
        return true;
      } else {
        return 'Please enter your username or e-mail address';
      }
    }
  }, {
    name: 'password',
    type: 'password',
    message: 'Enter your password:',
    validate: function(value) {
      if (value.length) {
        return true;
      } else {
        return 'Please enter your password';
      }
    }
  }
  ];

  inquirer.prompt(questions).then(callback);
}
```

As you can see, `inquirer.prompt()` asks the user a series of questions, provided in the form of an array as the first argument. Each question is made up of an object which defines the `name` of the field, the `type` (we're just using `input` and `password` respectively here, but later we'll look at a more advanced example), the prompt (`message`) to display and a validation callback (`validate`).

The input the user provides will be passed in to the callback, so we'll end up with simple object with two properties; `username` and `password`.

You can test all of this out by adding the following to `index.js`:

```js
getGithubCredentials(function(){
 console.log(arguments);
});
```

And then running the script using `node index.js`.

![Getting user input with Inquirer](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467718862Prompting-the-user-for-input.png)

## Dealing With GitHub Authentication

The next step is to create a function to retrieve an OAuth token for the Github API. Essentially we're going to "exchange" the username and password for a token.

Of course we don't want users to have to enter their credentials every time they use the tool; instead, we'll store the OAuth token for subsequent requests. This is where the [preferences](https://www.npmjs.com/package/preferences) package comes in.

### Storing Preferences

Storing preferences is outwardly quite straightforward; you can simply read and write to/from a JSON file, without the need for a third-party package. However the preferences package provides a few key advantages:

1. It determines the most appropriate location for the file for you, taking into account your operating system and the current user.
2. There's no need to explicitly read or write to the file, you simply modify a preferences object and that's taken care of for you in the background.
3. The preferences data is encrypted. That's significant in the context of this example, since we're going to be storing sensitive user data.

To use it, simply create an instance, passing it an application identifier, for example:

```js
var prefs = new Preferences('ginit');
```

If the preferences file doesn't exist, it'll return an empty object and create the file in the background. If there's already a preferences file, the contents will be decoded into JSON and made available to your application. You can now use `prefs` as a simple object, getting or setting properties as required. As mentioned above, you don't need to worry about saving it afterwards--that gets taken care of for you.

On Mac OSX/Linux, you'll find the file in `/Users/[YOUR-USERNME]/.config/preferences/ginit.pref`

## Communicating with the GitHub API

Let's create an instance of the Github API. We'll be using this in a couple of places, so we'll make it available to our script globally:

```js
var github = new GitHubApi({
 version: '3.0.0'
});
```

Now comes the function which checks whether we've already got an access token:

```js
function getGithubToken(callback) {
 var prefs = new Preferences('ginit');

 if (prefs.github && prefs.github.token) {
 return callback(null, prefs.github.token);
 }

 // Fetch token
 getGithubCredentials(function(credentials) {
 ...
 });
}
```

If a `prefs` object exists and it has `github` and `github.token` properties, this means that there is already a token in storage. In this case the callback function (passed in as an argument) is executed and control returns to the invoking function. We'll get to that later on.

If no token is detected, we need to fetch one. Of course getting an OAuth token involves a network request, which means a short wait for the user. This gives us an opportunity to look at the [clui](https://www.npmjs.com/package/clui) package which provides some enhancements for console-based applications, among them an animated spinner.

Creating a spinner is easy:

```js
var status = new Spinner('Authenticating you, please wait...');
status.start();
```

Once you're done, simply stop it and it will disappear from the screen:

```js
status.stop();
```

You can also set the caption dynamically using the `update` method. This could be useful if you have some indication of progress, for example displaying the percentage complete.

Here's the code to authenticate with Github:

```js
getGithubCredentials(function(credentials) {
  var status = new Spinner('Authenticating you, please wait...');
  status.start();

  github.authenticate(
  _.extend(
  {
  type: 'basic',
  },
  credentials
  )
  );

  github.authorization.create({
    scopes: ['user', 'public_repo', 'repo', 'repo:status'],
    note: 'ginit, the command-line tool for initalizing Git repos'
  }, function(err, res) {
    status.stop();
    if ( err ) {
      return callback( err );
    }
    if (res.token) {
      prefs.github = {
        token : res.token
      };
      return callback(null, res.token);
    }
    return callback();
  });
});
```

Let's step through this:

1. We prompt the user for their credentials using the `getGithubCredentials` method we defined earlier.
2. We use [basic authentication](https://github.com/mikedeboer/node-github#authentication) prior to trying to obtain an OAuth token.
3. We attempt to [create an access token for our application](https://github.com/mikedeboer/node-github#creating-tokens-for-your-application).
4. If we manage to get an access token, we set it in the preferences for next time.
5. We then return the token.

Any access tokens you create, whether manually or via the API as we're doing here, you'll be able to [see them here](https://github.com/settings/tokens). During the course of development, you may find you need to delete ginit's access token--identifiable by the `note` parameter supplied above--so that you can re-generate it.

If you have two-factor authentication enabled on your Github account, the process is slightly more complicated. You'll need to request the confirmation code--for example one sent via SMS--then supply it using the `X-Github-OTP` header. See [the documentation](https://developer.github.com/v3/auth/#working-with-two-factor-authentication) for further information.

If you've been following along and would like to try out what we have so far, ensure that you still have the following to the bottom of `index.js` and run the code as before:

## Creating a Repository

Once we've got an OAuth token, we can use it to create a remote repository with Github.

Again we can use Inquirer to ask a series of questions. We need a name for the repo, we'll ask for an optional description and we also need to know whether it should be public or private.

We'll use [minimist](https://www.npmjs.com/package/minimist) to grab defaults for the name and description from optional command-line arguments. For example:

```
ginit my-repo "just a test repository"
```

This will set the default name to `my-repo` and the description to `just a test repository`.

The following line will place the arguments in an array indexed by an underscore:

```
var argv = require('minimist')(process.argv.slice(2));
// { _: [ 'my-repo', 'just a test repository' ] }
```

This only really scratches the surface of the minimist package. You can also use it to intepret flags, switches and name/value pairs. Check out the documentation for more information.

Here's the code, to parse the command line arguments and ask a series of questions:

```
function createRepo(callback) {
 var argv = require('minimist')(process.argv.slice(2));

 var questions = [
 {
 type: 'input',
 name: 'name',
 message: 'Enter a name for the repository:',
 default: argv._[0] || files.getCurrentDirectoryBase(),
 validate: function( value ) {
 if (value.length) {
 return true;
 } else {
 return 'Please enter a name for the repository';
 }
 }
 },
 {
 type: 'input',
 name: 'description',
 default: argv._[1] || null,
 message: 'Optionally enter a description of the repository:'
 },
 {
 type: 'list',
 name: 'visibility',
 message: 'Public or private:',
 choices: [ 'public', 'private' ],
 default: 'public'
 }
 ];

 inquirer.prompt(questions).then(function(answers) {
 var status = new Spinner('Creating repository...');
 status.start();

 var data = {
 name : answers.name,
 description : answers.description,
 private : (answers.visibility === 'private')
 };

 github.repos.create(
 data,
 function(err, res) {
 status.stop();
 if (err) {
 return callback(err);
 }
 return callback(null, res.ssh_url);
 }
 );
 });
}
```

Once we have that information, we can simply use the github package to [create a repo](https://mikedeboer.github.io/node-github/#api-repos-create), which will give us a URL for the newly created repository. We can then set that up as a remote in our local Git repository. First, however, let's interactively create a `.gitignore` file.

## Creating a .gitignore File

For the next step, we'll to create a simple command-line "wizard" to generate a `.gitignore` file. If the user is running our application in an existing project directory, let's show them a list of files and directories already in the current working directory, and allow them to select which ones to ignore.

The Inquirer package provides a `checkbox` input type for just that.

![Inquirer's checkboxes in action](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467718853Inquirers-checkboxes-.png)

The first thing we need to do is scan the current directory, ignoring the `.git` folder and any existing `.gitignore` file (we do this by making use of lodash's [without](https://lodash.com/docs#without) method):

```
function createGitignore(callback) {
 var filelist = _.without(fs.readdirSync('.'), '.git', '.gitignore');
 ...
}
```

If there's nothing to add, there's no point in continuing so let's simply `touch` the current `.gitignore` file and bail out of the function:

```
function createGitignore(callback) {
 var filelist = _.without(fs.readdirSync('.'), '.git', '.gitignore');

 if (filelist.length) {
 ...
 } else {
 touch( '.gitignore' );
 return callback();
 }
}
```

Finally let's utilize Inquirer's checkbox "widget" to list the files. Once "submitted" we then generate a `.gitignore` by joining up the selected list of files, separated with a newline:

```
function createGitignore(callback) {
 var filelist = _.without(fs.readdirSync('.'), '.git', '.gitignore');

 if (filelist.length) {
 inquirer.prompt(
 [
 {
 type: 'checkbox',
 name: 'ignore',
 message: 'Select the files and/or folders you wish to ignore:',
 choices: filelist,
 default: ['node_modules', 'bower_components']
 }
 ]
 ).then(function( answers ) {
 if (answers.ignore.length) {
 fs.writeFileSync( '.gitignore', answers.ignore.join( '\n' ) );
 } else {
 touch( '.gitignore' );
 }
 return callback();
 }
 );
 } else {
 touch('.gitignore');
 return callback();
 }
}
```

Notice that we can also provide a list of defaults--in this case we're pre-selecting `node_modules` and `bower_components`, should they exist.

Our function now pretty much guarantees we've got a `.gitignore` file, so we can proceed with initializing a Git repository.

## Interacting with Git From Within the App

There are a number of ways to interact with Git but perhaps the simplest is to use the [simple-git](https://www.npmjs.com/package/simple-git) package. This provides a set of chainable methods which, behind the scenes, run the Git executable.

These are the repetitive tasks we will use it to automate:

1. Run `git init`
2. Add the `.gitignore` file
3. Add the remaining contents of the working directory
4. Perform an initial commit
5. Add the newly-created remote repository
6. Push the working directory up to the remote

Here's the code:

```
function setupRepo( url, callback ) {
 var status = new Spinner('Setting up the repository...');
 status.start();

 git
 .init()
 .add('.gitignore')
 .add('./*')
 .commit('Initial commit')
 .addRemote('origin', url)
 .push('origin', 'master')
 .then(function(){
 status.stop();
 return callback();
 });
}
```

## Putting it all together

Finally, we'll create a function to obtain the token and authenticate the user:

```
function githubAuth(callback) {
 getGithubToken(function(err, token) {
 if (err) {
 return callback(err);
 }
 github.authenticate({
 type : 'oauth',
 token : token
 });
 return callback(null, token);
 });
}
```

Before moving on to the code which handles the main logic of the app.

```
githubAuth(function(err, authed) {
 if (err) {
 switch (err.code) {
 case 401:
 console.log(chalk.red('Couldn\'t log you in. Please try again.'));
 break;
 case 422:
 console.log(chalk.red('You already have an access token.'));
 break;
 }
 }
 if (authed) {
 console.log(chalk.green('Sucessfully authenticated!'));
 createRepo(function(err, url){
 if (err) {
 console.log('An error has occured');
 }
 if (url) {
 createGitignore(function() {
 setupRepo(url, function(err) {
 if (!err) {
 console.log(chalk.green('All done!'));
 }
 });
 });
 }
 });
 }
});
```

As you can see, it ensures the user is authenticated, before calling all of our other functions (`createRepo`, `createGitignore`, `setupRepo`) sequentially. It also handles any errors and offers the user appropriate feedback.

You can check out the completed [index.js](https://github.com/sitepoint-editors/ginit/blob/master/index.js) file on our GitHub repo.

## Making the ginit Command Available Globally

The one remaining thing to do is to make our command available globally. To do this, we'll need to add a [shebang](https://en.wikipedia.org/wiki/Shebang_%28Unix%29) line to the top of `index.js`:

```
#!/usr/bin/env node
```

Next we need to add a `bin` property to our `package.json` file. This maps the command name (`ginit`) to the name of the file to be executed (relative to `package.json`).

```
"bin": {
 "ginit": "./index.js"
}
```

After that install the module globally and you will have a working shell command:

```
npm install -g
```

This will also work on Windows, as [npm will helpfully install a cmd wrapper alongside your script](http://stackoverflow.com/a/10398567/1136887).

## Taking it Further

We've got a fairly nifty, albeit simple command-line app for initialising Git repositories. But there's plenty more you could do to enhance it further.

If you're a Bitbucket user, you could adapt the program to use the Bitbucket API to create a repository--there's a [Node.js API wrapper available](https://www.npmjs.com/package/bitbucket-api) to help you get started. You may wish to add an additional command-line option or prompt to ask the user whether they want to use Github or Bitbucket--Inquirer would be perfect for just that--or merely replace the Github specific code with a Bitbucket alternative.

You could also provide the facility to specify your own set of defaults for the `.gitgnore` file, instead of hard-coded list. The preferences package might be suitable here, or you could provide a set of "templates"--perhaps prompting the user for the type of project. You might also want to look at integrating it with the [.gitignore.io](https://www.gitignore.io/) command-line tool / API.

Beyond all that you may also wish to add additional validation, provide the ability to skip certain sections, and more--if you have any other ideas, do let me know in the comments.
