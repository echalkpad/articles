# Building a desktop application with Electron -- Developers Writing

[Original URL](https://medium.com/developers-writing/building-a-desktop-application-with-electron-204203eeb658)

> A detailed guide on building your very own sound machineusing JavaScript, Node.js and Electron

**_A detailed guide on building your very own sound machine<br>
using JavaScript, Node.js and Electron_**

Desktop applications always had a special place in my heart. Ever since browsers and mobile devices got powerful, there's been a steady decline of desktop applications which are getting replaced by mobile and web applications. Still, there's are a lot of upsides to writing desktop applications -- they are always present once they're in your start menu or dock, they're _alt(cmd)-tabbable_ (I hope that's a word) and mostly connect better with the underlying operating system (with its shortcuts, notifications, etc) than web applications.

In this article, I'll try to guide you through the process of building a simple desktop application and touch on important concepts for building desktop application with JavaScript.

![](https://cdn-images-1.medium.com/max/400/1*GS-t3eNz9Jy7YWKIxxmJPg.png)

GitHub Electron The main idea behind developing desktop applications with JavaScript is that you build one codebase and package it for each operating system separately. This abstracts away the knowledge needed to build native desktop applications and makes maintenance easier. Nowadays, developing a desktop application with JavaScript relies on either [Electron](http://electron.atom.io/) or [NW.js](http://nwjs.io/). Although both tools offer more or less the same features, I went with Electron because it has [some advantages I found important](https://github.com/atom/electron/blob/master/docs/development/atom-shell-vs-node-webkit.md). At the end of the day, you can't go wrong with either.

I assume that you've got your basic text editor (or IDE) and [Node.js/npm installed](https://nodejs.org/download/). I'll also assume you've got HTML/CSS/JavaScript knowledge (Node.js knowledge with CommonJS modules would be great, but isn't crucial) so we can focus on learning Electron concepts without worrying about building the user interface (which, as it turns out, are just common web pages). If not, you'll probably feel somewhat lost and I recommend visiting [my previous blog post](https://medium.com/@bojzi/overview-of-the-javascript-ecosystem-8ec4a0b7a7be) to brush up on your basics.

In a nutshell, Electron provides a runtime to build desktop applications with pure JavaScript. The way it works is -- Electron takes a _main_ file defined in your _package.json_ file and executes it. This main file (usually named _main.js_) then creates application windows which contain rendered web pages with the added power of interacting with the native GUI (graphical user interface) of your operating system.

In detail, once you start up an application using Electron, a _main process_ is created. This _main process_ is responsible for interacting with the native GUI of your operating system and creates the GUI of your application (your application windows).

![](https://cdn-images-1.medium.com/max/600/1*EJETq7XOPz5RVY5IfF6NIg@2x.png)

Purely starting the _main process_ doesn't give the users of your application any application windows. Those are created by the _main process_ in the main file by using something called a _BrowserWindow_ module. Each browser window then runs its own _renderer process_. This _renderer process_ takes a web page (an HTML file which references the usual CSS files, JavaScript files, images, etc.) and renders it in the window. Your web pages are rendered with [Chromium](https://www.chromium.org/) so a very high level of compatibility with standards is guaranteed.

For example, if you only had a calculator application, your _main process_ would instantiate a window with a web page where your actual web page (calculator) is.

Although it is said that only the _main process_ interacts with the native GUI of your operating system, there are techniques to offload some of that work to _renderer processes (_we'll look into building a feature leveraging such a technique_)._

The _main process_ can access the native GUI through a series of modules [available directly in Electron](https://github.com/atom/electron/tree/master/docs/api). Your desktop application can access all Node modules like the excellent [_node-notifier_](https://github.com/mikaelbr/node-notifier) to show system notifications, [_request_](https://www.npmjs.com/package/request) to make HTTP calls, etc.

Let's get started with a traditional greeting and install all the necessary prerequisites.

This guide is accompanied by the [sound-machine-tutorial repository](https://github.com/bojzi/sound-machine-electron-guide).<br>
Use the repository to follow along or continue at certain points. Clone the repository to get started:

```
git clone https://github.com/bojzi/sound-machine-electron-guide.git
```

and then you can jump to a git tag in the _sound-machine-tutorial_ folder with:

```
git checkout <tag-name>
```

I'll let you know when a tag is available with a code block:

```
Follow along:
git checkout 00-blank-repository
```

Once you clone/checkout your desired tag, run:

```
npm install
```

so that you aren't missing any Node modules.

If you can't switch to another tag, it would be easiest to just reset your repository state and then do the checkout:

```
git add -A
git reset --hard

Follow along with the tag 00-blank-repository:
git checkout 00-blank-repository
```

In the project folder create a new _package.json_ file in it with the following contents:

This barebones _package.json:_

- sets up the name and version of the application,

- lets Electron know which script the _main process_ is going to run (_main.js_) and

- sets up a useful shortcut -- an _npm_ script to run the application easily by running "_npm start_" in your CLI (terminal or command prompt).

It's time to get _Electron_. The easiest way of accomplishing that is by installing a prebuilt binary for your operating system through _npm_ and save it as a development dependency in your _package.json (_that happens automatically with _–save-dev)_. Run the following in your CLI (in the project folder):

```
npm install --save-dev electron-prebuilt
```

The prebuilt binary is tailored to the operating system it's being installed on and allows the running of "_npm start_". We're installing it as a development dependency because we will only need it during development.

That's, more or less, everything you need to start developing with _Electron_.

Create an **_app_** folder and an _index.html_ file in that folder with the following contents:

In the root of the project create a _main.js_ file. That's the file that Electron's main process is going to spin up and allow the creation of our "Hello, world!" web page. Create the file with the following contents:

Nothing scary, right?<br>
The _app_ module controls your application lifecycle (for example -- reacting to the ready state of your application)_._ The _BrowserWindow_ module allows window creation.<br>
The _mainWindow_ object is going to be your main application window and is declared as _null_ because the window would otherwise be closed once JavaScripts garbage collection kicks in.

Once _app_ gets the _ready_ event, we create a new 800 pixels wide and 600 pixels high window using _BrowserWindow_.<br>
That window's _renderer process_ is going to render our _index.html_ file.

Run our "Hello, World!" application by running the following in your CLI:

```
npm start
```

and bask in the glory that is your app.

![](https://cdn-images-1.medium.com/max/800/1*rPsf_UzNNKUpH39yQX-q_Q.png)

Hello indeed.

First things first -- _what's a sound machine_?<br>
A sound machine is a little device that makes sounds when you press various buttons, mostly cartoon or reaction sounds. It's a funny little tool to lighten up the mood in an office and a great use case to develop a desktop application as we're going to explore quite a few concepts while developing it (and get a nifty sound machine to boot).

![](https://cdn-images-1.medium.com/max/800/1*El4nvvh3h3vjRgwh_wVO7A.png)

The. Sound. Machine. The features we're going to build and concepts we're going to explore are:

- basic sound machine (basic browser window instantiation),

- closing the sound machine (remote messages between _main_ and _renderer process),_

- playing sounds without having the application in focus (global keyboard shortcuts),

- creating a settings screen for shortcut modifier keys (Shift, Ctrl and Alt) (storing user settings in home folder),

- adding a tray icon (remotely creating native GUI elements and getting to know menus and tray icon) and

- packaging your application (packaging your application for Mac, Windows and Linux).

With a working "Hello, world!" application under your belt, it's high time to start building a sound machine.

A typical sound machine features several rows of buttons which respond to presses by making sounds. The sounds are mostly cartoonish and/or reaction based (laughter, clapping, glass breaking, etc.).

That's also the very first feature we'll build -- a basic sound machine that responds to clicks.

![](https://cdn-images-1.medium.com/max/400/1*CsKlT-mLLFJlRoYZnAk9Tg@2x.png)

Basic files and folders structure Our application structure is going to be very straightforward.

In the **_root_** of the application we'll keep the _package.json_ file, the _main.js_ file and any other application-wide files we need.

The **_app_** folder will house our HTML files of various types within folders like **_css_**, **_js_**, **_wav_** and **_img_**.

To make things easier, all the files needed for web page design have already been included in the initial state of the repository. Please check the tag 01-start-project out. If you followed along and created the "Hello, world!" application, you'll have to reset your repository and then do the checkout:

```
If you followed along with the "Hello, world!" example:
git add -A
git reset --hard

Follow along with the tag 01-start-project:
git checkout 01-start-project
```

To keep things simple, we're going to have only two sounds but extending it to the full 16 sounds is simply a matter of finding extra sounds, extra icons and modifying _index.html_.

Let's revisit _main.js_ to define the look of the sound machine. Replace the contents of the file with:

We're customising the window we're creating by giving it a dimension, making it non-resizable and frameless. It's going to look like a real sound machine hovering on your desktop.

The question now is -- how to move a frameless window (with no title bar) and close it?<br>
I'll talk about custom window (and application) closing very soon (and introduce a way of communicating between the _main process_ and a _renderer process_), but the dragging part is easy. If you look at the _index.css_ file (in **_app/css_**), you'll see the following:

_-webkit-app-region: drag;_ allows the whole _html_ to be a draggable object. There is a problem now, though -- you can't click buttons on a draggable object. The other piece of the puzzle is _-webkit-app-region: no-drag;_ which allows you to define undraggable (and thus clickable elements). Consider the following excerpt from _index.css_:

The _main.js_ file can now make a new window and display the sound machine. And really, if you start your application with _npm start,_ you'll see the sound machine come alive. It's not a surprise that nothing's happening right now because we just have a static web page.

Put the following in the _index.js_ file (located in **_app/js_**) to get the interactivity going:

This code is pretty simple. We:

- query for the sound buttons,

- iterate through the buttons reading out the _data-sound_ attribute,

- add a background image to each button

- and add a click event to each button that plays audio (using the [HTMLAudioElement interface](https://developer.mozilla.org/en/docs/Web/API/HTMLAudioElement))

Test your application by running the following in your CLI:

```
npm start
```

![](https://cdn-images-1.medium.com/max/800/1*Umzbl_QY7tatF_9Khm400Q@2x.png)

A working sound machine!

```
Follow along with the tag 02-basic-sound-machine:
git checkout 02-basic-sound-machine
```

To recap -- application windows (more exactly their _renderer process_) shouldn't be interacting with the GUI (and that's what closing a window is). The [official Electron quick start guide](https://github.com/atom/electron/blob/master/docs/tutorial/quick-start.md) says:

> In web pages, it is not allowed to call native GUI related APIs because managing native GUI resources in web pages is very dangerous and it is easy to leak resources. If you want to perform GUI operations in a web page, the renderer process of the web page must communicate with the main process to request the main process perform those operations.

Electron provides the [_ipc_ (inter-process communication) module](https://github.com/atom/electron/blob/master/docs/api/ipc-renderer.md) for that type of communication. _ipc_ allows subscribing to messages on a _channel_ and sending messages to subscribers of a _channel_. A channel is used to differentiate between receivers of messages and is represented by a string (for example "channel-1", "channel-2"...). The message can also contain data. Upon receiving a message, the subscriber can react by doing some work and can even answer. The biggest benefit of messaging is separation of concerns -- the _main process_ doesn't have to know which _renderer processes_ there are or which one sent a message.

![](https://cdn-images-1.medium.com/max/800/1*VkIt0RY92_fSiZDa9RC6Ng@2x.png)

You've got mail. That's exactly what we'll do here -- subscribe the _main process_ (_main.js_) to the "_close-main-window_" channel and send a message on that _channel_ from the _renderer process_ (_index.js_) when someone clicks the close button.

Add the following to _main.js_ to subscribe to a channel:

After requiring the module, subscribing to messages on a channel is very easy and involves using the _on()_ method with the channel name and a callback function.

To send a message on that channel, add the following to _index.js_:

Again, we require the _ipc_ module and bind a _click_ event to the element with the close button. On clicking the close button we send a message via the "close-main-window" channel with the _send()_ method.

There's one more detail that could bite you and we've talked about it already -- the _clickability_ of draggable areas. _index.css_ has to define the close button as non-draggable.

That's all, our application can now be closed via the close button. Communicating via _ipc_ can get complicated by examining the event or passing arguments and we'll see an example of passing arguments later.

```
Follow along with the tag 03-closable-sound-machine:
git checkout 03-closable-sound-machine
```

Our basic sound machine is working great. But we do have a usability issue -- what use is a sound machine that has to sit in front of all your windows the whole time and be clicked repeatedly?

This is where global keyboard shortcuts come in. Electron provides a [global shortcut](https://github.com/atom/electron/blob/master/docs/api/global-shortcut.md) module which allows you to listen to custom keyboard combinations and react. The keyboard combinations are known as [_Accelerators_](https://github.com/atom/electron/blob/master/docs/api/accelerator.md) and are string representations of a combination of keypresses (for example "Ctrl+Shift+1").

![](https://cdn-images-1.medium.com/max/800/1*xx7pP0czBfFTskrN5tmyZQ@2x.png)

Plug... er, press and play! Since we want to catch a native GUI event (global keyboard shortcut) and do an application window event (play a sound), we'll use our trusted _ipc_ module to send a message from the _main process_ to the _renderer process_.

Before diving into the code, there are two things to consider:

1. global shortcuts have to be registered after the _app "ready"_ event (the code should be in that block) and

2. when sending messages via _ipc_ from the _main process_ to a _renderer process_ you have to use the reference to that window (something like _"createdWindow.webContents.send('channel')_)

With that in mind, let's alter our _main.js_ and ** add the following code:

First, we require the _global-shortcut_ module. Then, once our application is ready, we register two shortcuts -- one that will respond to pressing Ctrl, Shift and 1 together and the other that will respond to pressing Ctrl, Shift and 2 together. Each of those will send a message on the "_global-shortcut_" channel with an argument. We'll use that argument to play the correct sound. Add the following to _index.js_:

To keep thing simple, we're going to simulate a button click and use the soundButtons selector that we've created while binding buttons to playing sounds. Once a message comes with an argument of 1, we'll take the _soundButtons[1]_ element and trigger a mouse click on it (_note_: in a production application, you'd want to encapsulate the sound playing code and execute that).

```
Follow along with the tag 04-global-shortcuts-bound:
git checkout 04-global-shortcuts-bound
```

With so many applications running at the same time, it could very well be that the shortcuts we've envisioned are already taken. That's why we're going to introduce a settings screen and store which modifiers (Ctrl, Alt and/or Shift) we're going to use.

To accomplish all of that, we'll need the following:

- a settings button in our main window,

- a settings window (with accompanying HTML, CSS and JavaScript files),

- _ipc_ messages to open and close the settings window and update our global shortcuts and

- storing/reading of a settings JSON file from the user system.

Phew, that's quite a list.

Similar to closing the main window, we're going to send messages on a _channel_ from _index.js_ when the settings button gets clicked. Add the following to _index.js_:

After clicking the settings button, a message on the channel "_open-settings-window_" gets sent. _main.js_ can now react to that event and open up the new window. Add the following to _main.js_:

Nothing new to see here, we're opening up a new window just like we did with the main window. The only difference is that we're checking if the settings window is already open so that we don't open up two instances.

Once that works, we need a way of closing that settings window. Again, we'll send a message on a channel, but this time from _settings.js_ (as that is where the settings close button is located). Create (or replace the contents of) _settings.js_ with the following:

And listen on that _channel_ in _main.js_. Add the following:

Our settings window is now ready to implement its own logic.

```
Follow along with the tag 05-settings-window-working:
git checkout 05-settings-window-working
```

The process of interacting with the setting windows, storing the settings and promoting them to our application will look like this:

- create a way of storing and reading user settings in a JSON file,

- use these settings to display the initial state of the settings window,

- update the settings upon user interaction and

- let the _main process_ know of the changes.

We could just implement the storing and reading of settings in our _main.js_ file but it sounds like a great use case for writing a little module that we can then include in various places.

**Working with a JSON configuration**

That's why we're going to create _configuration.js_ file ** and require it whenever we need it. Node.js uses the [CommonJS module pattern](https://nodejs.org/docs/latest/api/modules.html) which means that you export only your API and other files require/use the functions available on that API.

![](https://cdn-images-1.medium.com/max/800/1*YnvmoGoSwPb-EMDK5GJwzA@2x.png)

Both main.js and settings.js make use of configuration.js. To make storing and reading easier, we'll use the _nconf_ module which abstracts the reading and writing of a JSON file for us. It's a great fit. But first, we have to include it in the project with the following command executed in the CLI:

```
npm install --save nconf
```

This tells npm to install the _nconf_ module as an application dependency and it will be included and used when we package our application for an end user (in contrast to installing with the _save-dev_ argument which will only include modules for development purposes).

The _configuration.js_ file is pretty simple, so let's examine it fully. Create a _configuration.js_ file in the root of the project with the following contents:

_nconf_ only wants to know where to store your settings and we're giving it the location of the user home folder and a file name. Getting the user home folder is simply a matter of asking Node.js (_process.env_) and differentiating between various platforms (as observed in the _getUserHome()_ function).

Storing or reading settings is then accomplished with the built-in methods of _nconf_ (_set()_ for storing, _get()_ for reading with _save()_ and _load()_ for file operations) and exporting the API by using the standard CommonJS _module.exports_ syntax.

**Initialising default shortcut key modifiers**

Before moving on with settings interaction, let's initialise the settings in case we're starting the application for the first time. We'll store the modifier keys as an array with the key "_shortcutKeys_" and initialise it in _main.js_. For all of that to work, we must first require our _configuration_ module:

We try reading if there's anything stored under the setting key "_shortcutKeys_". If not, we set an initial value.

As an additional thing in _main.js_, we'll rewrite the registering of global shortcut keys as a function that we can call later when we update our settings. Remove the registering of shortcut keys from _main.js_ and alter the file this way:

The function resets the global shortcuts so that we can set new ones, reads the modifier keys array from settings, transforms it to a [Accelerator-compatible](https://github.com/atom/electron/blob/master/docs/api/accelerator.md) string and does the usual global shortcut key registration.

**Interaction in the settings window**

Back in the _settings.js_ file, we need to bind click events which are going to change our global shortcuts. First, we'll iterate through the checkboxes and mark the active ones (reading the values from the configuration module):

And now we'll bind the checkbox behaviour. Take into consideration that the settings window (and its _renderer process)_ are not allowed to change GUI binding. That means that we'll need to send an _ipc_ message from _settings.js_ (and handle that message later)_:_

It's a bigger piece of code but still pretty simple.<br>
We iterate through all the checkboxes, bind a click event and on each click check if the settings array contains the modifier key or not -- and according to that result, modify the array, save the result to settings and send a message to the _main process_ which should update our global shortcuts.

All that's left to do is subscribe to the _ipc_ channel "_set-global-shortcuts_" in _main.js_ and update our global shortcuts:

Easy. And with that, our global shortcut keys are configurable!

```
Follow along with the tag 06-shortcuts-configurable:
git checkout 06-shortcuts-configurable
```

Another important concept in desktop applications are menus. There's the always useful context menu (AKA right-click menu), tray menu (bound to a tray icon), application menu (on OS X), etc.

![](https://cdn-images-1.medium.com/max/400/1*HinA-91xPYoCKel_obvuug@2x.png)

Megamenu. In this guide we'll add a tray icon with a menu. We'll also use this opportunity to explore an other way of inter-process communication -- [the _remote_ module](https://github.com/atom/electron/blob/master/docs/api/remote.md).

The _remote_ module makes RPC style calls from the _renderer process_ to the _main process_. You require modules and work with them in the _renderer process_ but they're being instantiated in the _main process_ and methods that you call on them are being executed in the _main process_. In practice, it means that you remotely request native GUI modules in _index.js_ and call methods on them but they get executed in _main.js_. In that way, you could require the BrowserWindow module from _index.js_ and instantiate a new browser window. Behind the scenes, that's still a synchronous call to the _main process_ which actually creates that new browser window.

![](https://cdn-images-1.medium.com/max/800/1*vLP63PA79c_s3ZdbJCe-yg@2x.png)

Call me maybe? Let's see how we'd create a menu and bind it to a tray icon while doing it in a _renderer process_. Add the following to _index.js_:

The native GUI modules (_menu_ and _tray_) were required remotely and that way it's safe to use them here.

A tray icon is defined through its icon. OS X supports image templates (by convention, an image is considered a template image if its filename ends with "Template") which makes it easy to work with the dark and light themes. Other OSes get a regular icon.

There are multiple ways of building a menu in Electron. This way creates a menu template (a simple array with menu items) and builds a menu from that template. At the end, the new menu is attached to the tray icon.

```
Follow along with the tag 07-ready-for-packaging:
git checkout 07-ready-for-packaging
```

What's the use of an application which you can't let people download and use?

![](https://cdn-images-1.medium.com/max/400/1*O-ZI4nwQON9kNosiyfEfMw@2x.png)

Wrap it up. Packaging your application for all platforms is easy [using _electron-packager_](https://github.com/maxogden/electron-packager). In a nutshell, electron-packager abstracts away all work going into wrapping your app with Electron and generates all platforms for which you're going to publish.

It can be used as a CLI application or as part of a build process. Building a more complicated build scenario is not in the scope of this article, but we'll leverage the power of _npm_ scripts to make packaging easier. Using electron-packager is trivial, the general form when packaging an application is:

```
electron-packager <location of project> <name of project> <platform> <architecture> <electron version> <optional options>
```

where:

- location of project points to the folder where your project is,

- name of project defines the name of your project,

- platform decides for which platforms to build (_all_ to build for Windows, Mac and Linux),

- architecture decides for which architectures to build (x86 or x64, _all_ for both) and

- electron version lets you choose which Electron version to use.

The first package is going to take a while because all the binaries for all platforms have to be downloaded. Subsequent packages are much faster.

I package the sound machine typically like this (on a Mac):

```
electron-packager ~/Projects/sound-machine SoundMachine --all --version=0.30.2 --out=~/Desktop --overwrite --icon=~/Projects/sound-machine/app/img/app-icon.icns
```

The new options included in the command are self-explanatory. To get a nice icon, you'll first have to convert it to .icns (for Mac) and/or .ico (for Windows). Just search for a tool to convert your PNG file to these formats like [this one](https://iconverticons.com/online/) (be sure to download the file with the _.icns_ extension and not _.hqx_). If packaging for Windows from a non-Windows OS, you'll need wine on your path (Mac users can use brew, while Linux users can use apt-get).

It doesn't make sense to run that big command every time. We can add another script to our _package.json_. First of all, install electron-packager as a development dependency:

```
npm install --save-dev electron-packager
```

Now we can add a new script to our _package.json_ file:

Packaging just got a lot easier. And then run the following in CLI:

```
npm run-script package
```

The package command starts the electron-packager, looks in the current directory and build to Desktop. The script should be changed if you are using Windows, but that is trivial.

The sound machine in its current state ends up weighing a whopping 100 MB. Don't worry, once you archive it (zip or an archive type of your choice), it'll lose more than half its size.

If you really want to go to town, take a look at [_electron-builder_](https://github.com/loopline-systems/electron-builder) which takes the packages produced by _electron-packager_ and creates automated installers.

With the application packaged and ready to go, you can now start developing your own features.

Here are some ideas:

- a help screen with info about the app, its shortcuts and author,

- adding an icon and a menu entry to open the info screen,

- build a nice packaging script for faster builds and distribution,

- add notifications using [node-notifier](https://github.com/mikaelbr/node-notifier) to let users know which sound they're playing,

- use _lodash_ to a greater extent for a cleaner code base (like iterating through arrays),

- minify all your CSS and JavaScript with a build tool before packaging,

- combine the aforementioned node-notifier with a server call to check for new versions of your app and notify users...

For a nice challenge -- try extracting your Sound machine browser windows logic and using something like browserify to create a web page with the same sound machine you've just created. One code base -- two products (desktop application and web application). Nifty!

We've only scratched the surface of what Electron brings to the table. It's pretty easy to do things like watching for power events on the host machine or getting various information on the screen (like cursor position).

For all of those built-in utilities (and generally while developing applications with Electron), [check out the Electron API docs](https://github.com/atom/electron/tree/master/docs/api).

These Electron API docs are a part of the docs folder at the Electron GitHub repository and that folder is well worth checking out.

Sindre Sorhus maintans an [awesome list of Electron resources](https://github.com/sindresorhus/awesome-electron) on which you can find really cool projects and information like an excellent overview of a typical Electron application architecture which can serve as a refresher on the code we've been developing up until now.

In the end, Electron is based on io.js (which is going to be merged back into Node.js) and most of Node.js modules are compatible and can be used to extend your application. Just browse [npmjs.com](https://www.npmjs.com/) and grab what you need.

Not by a long shot.

Now, it's time to build a bigger application. I've mostly skipped on using extra libraries or build tools in this guide to concentrate on important issues but you can easily write your app in ES6 or Typescript, use Angular or React and simplify your build with gulp or Grunt.

With your favorite language, framework and build tool, why not build a Flickr sync desktop application using the Flickr API and node-flickrapi or a GMail client using Google's officiall Node.js client library?

Pick an idea that's going to motivate you, init a repository and just do it.
