# Auto-updating apps for Windows and OSX using Electron: The complete guide

[Original URL](https://medium.com/@svilen/auto-updating-apps-for-windows-and-osx-using-electron-the-complete-guide-4aa7a50b904c)

> You've made an amazing desktop app using Electron in no time, and you've just shipped it to your users. While you're chilling like a villain, you may realise that you have missed one important bit:...

You've made an amazing desktop app using [Electron](http://electron.atom.io) in no time, and you've just shipped it to your users. While you're chilling like a villain, you may realise that you have missed one important bit: how are users going to get the next version of that amazing piece of software that you've just created? Yes, of course they can uninstall/install the app, but that's lame, isn't it?

A quick glance at [Electron's documentation](http://electron.atom.io/docs), and you will notice that the framework ships with an [**_auto-updater_**](http://electron.atom.io/docs/v0.36.7/api/auto-updater/) module, which is simply an interface to another framework -- Squirrel. Squirrel will check for new versions in the background (or when you tell it to), download the new version, and handle the upgrade for you automagically when you start or restart the app.

Sadly things are not as easy as they look on paper -- there are differences in how auto-updating works on OSX and Windows (there is no support for Linux as of now) and documentation is scattered across multiple repositories. I've spent a fair amount of time trying to make everything work, so I decided it's worth putting what I've learnt into a little guide, that should (hopefully) save some of your time.

Although everything described should work on both Windows and OSX, for the sake of clarity--I am running everything on Mac OSX 10.11, with the exception of building the installer for Windows.

Suggestions to improve or update this guide? [Drop me a tweet](http://twitter.com/svileng)!

Before we look at implementing auto-updating, there's one important step -- packaging. I assume that the majority of people have this working, using [electron-packager](https://github.com/maxogden/electron-packager), but there are a couple of things which are easy to miss.

package.json Notice the extra **_package.json_** **fields -- ***productName**_,_** author **_and_** description _*_-- which are not necessary for packaging, but will be picked up by the Squirrel Installer for Windows.

Code-signing the application is not required for auto-updating, but is highly desirable. For OSX, you'll need an Apple Developer Certificate, and then you just replace this parameter for the **_pack:osx_** script:

```
--sign='Developer ID Application: My Company Ltd (ABCDEFGH10)'
```

with whatever you have in **_Keychain Access > My Certificates_** in OSX.

I haven't got code-signing working for Windows yet, but you can have a look at [this excellent tutorial](http://zed.one/code-signing-a-windows-application.html) on the topic.

Specifically for Windows, it's good to pass all the optional version-string parameters to electron-packager, such as company name and product name and so on. Once we generate the Squirrel installer for Windows, the app will show up with the right metadata in Windows' start menu, instead of Atom's default data.

So, let's get started!

Auto-updating for OSX is handled by [Squirrel.Mac](https://github.com/Squirrel/Squirrel.Mac), which is built-in Electron. Which means that you just have to package your app, and you're good to go!

Well, not quite.

The way Squirrel.Mac works is by checking a given API endpoint to see if there is a new version of the app. If there is no new version, the endpoint should return **_HTTP_** **_204_.** If there is a new version, however, it will expect a **_HTTP 200 JSON-formatted_** response, containing **a url to a .zip file:**

```
{
 "url": "http://mysite.com/path/to/zip/MyApp.zip"
}
```

Squirrel will make an **_application/zip_** request using that url, download the file, and fire up the final event to let you know that the update is ready to install. Everything is handled automatically for you.

If you're not quite sure how the server should like, here's a super-minimal Node.js/Express app, which assumes the following directory structure:

```
└── releases
 ├── darwin
 │ ├── 1.0.0
 │ ├── 1.0.2
 │ └── 1.0.3
 └── win32
```

Node update server package.json Simple Express server for testing Squirrel.Mac auto-updating It will serve files from the local filesystem, which is not ideal -- I suggest getting those files on Amazon S3 instead.

You can call the endpoint from Electron in development like so:

```
http://localhost:3000/updates/latest?v=1.0.1
```

Where **_?v=1.0.1_** is your current app version.

Now that you have the server and endpoint in place, handling updating in the app is quite simple.

In your **main Electron application** file, require the auto-updater module, grab the current OS and your app version:

```
const autoUpdater = require('auto-updater');
const appVersion = require('./package.json').version;
const os = require('os').platform();
```

And configure the endpoint, which is going to be different for Windows and OSX (you'll see why when we get to the Windows part):

Telling Electron where to look at for new versions There are a number of events fired by the **_autoUpdater_** module, which you can implement and send to the renderer process: check [auto-updater documentation page](http://electron.atom.io/docs/v0.36.7/api/auto-updater/#events) for more info. Depending on how you decide to handle the event and notify the user, all you need to do in the end is

```
autoUpdater.quitAndInstall();
```

in the main application file, and the app will restart with the new version in place. Boom!

As you would expect, auto-updating on Windows is done via [Squirrel.Windows](https://github.com/Squirrel/Squirrel.Windows). The process, however, is completely different to the one for OSX.

Unlike Squirrel.Mac, Squirrel.Windows does not require an API endpoint to check for new versions -- all it needs is a file server, so you can simply drop your files in an Amazon S3 bucket. The Squirrel Updater, however, is not bundled in Electron, and is a 3rd party dependency. This means that you'll need to generate an installer for your packaged Windows app, that will include the Squirrel Updater as well.

The good news is that the installer and updater for Windows is quite slick--it installs and launches the app on-the-fly, when you launch the Setup.exe. No boring installer wizards and clicking Next, Next, Next, Finish, like most installers for Windows. It also generates delta packages, so you don't have to download the whole app when you do an update, which is ace.

The bad news (for Mac users, at least) is that I couldn't build the installer properly on OSX, so I suggest you [download a Windows VM](https://dev.windows.com/en-us/microsoft-edge/tools/vms/mac/) for [VirtualBox](https://www.virtualbox.org/), and install Node.js on it.

Assuming that you've configured and set the correct update feed, using the code for OSX above, the only thing left code-wise is to handle a few Squirrel.Windows events, which are different than the ones for OSX. You can [see an example here](https://github.com/atom/grunt-electron-installer#handling-squirrel-events). There is an easier way, however -- just install the [**electron-squirrel-startup**](https://github.com/mongodb-js/electron-squirrel-startup) ****** npm module:

```
npm install electron-squirrel-startup --save-dev
```

And place that line at the top of your main Electron application file:

```
if (require('electron-squirrel-startup')) return;
```

Squirrel.Windows events should be handled as early as possible, apparently, so that's the way to go.

Finally, to generate the installer, we're going to use Atom's [**grunt-electron-installer**](https://github.com/atom/grunt-electron-installer)**_._** Why this is a grunt plugin, instead of a simple command-line utility -- I don't know, but that's how it is.

**Update: Turns out the Electron team are working on a stand-alone installer package called** [**electron-winstaller**](https://github.com/electronjs/windows-installer)**, which has the same API as the grunt task.**

Zip the win32 folder, produced by electron-packager, and copy it over to the Virtual Machine. Outside that folder, you'll need to configure the grunt task that will build the installer, so you should install all dependencies first:

```
npm install -g grunt-cli
npm install grunt grunt-electron-installer --save-dev
```

Assuming that the Windows compiled package is in a folder called **MyApp-win32-ia32**, here's how the Gruntfile could look like:

Note that if you want to code-sign your files and the installer, you should supply all params to the task config, too.

Running the grunt task should produce a bunch of files in the ./**dist** folder:

```
grunt create-windows-installer
```

You expect to see something similar to

```
└── dist
 ├── MyApp.1.0.0.nupkg
 ├── MyApp-1.0.0-full.nupkg
 ├── RELEASES
 ├── Setup.exe
```

With the next release, the installer will generate a delta packages automatically, too.

Now the easiest part -- grab all those files, and dump them in an S3 bucket. The url pointing to that folder, containing the **RELEASES** and **nupkg** files should be the **updateFeed** url given to the autoUpdater, when running on Windows (we've done that earlier for OSX, so go back to the main application file and update it).

Note: there is currently an issue with the installer related to node-rcedit -- it throws an error when it tries to modify the .exe file to place some of the metadata and replace the default icon. You can [see the issue here](https://github.com/atom/grunt-electron-installer/issues/96). Unfortunately if you want to change the icon or put the real data onto the installer file, you'll have to do it manually with [ResHacker](http://www.angusj.com/resourcehacker/).

Hope this helps and serves as a good starting point to anyone who's working on auto-updating with Electron. If you spot anything that I've missed, or have any suggestions for improvement (there's definitely room for that) -- feel free to [drop me a tweet](http://twitter.com/svileng) to let me know! Also, keep in mind that Electron is a fast-evolving framework, so always make sure you're reading the right documentation for your version, since Electron APIs changes quite often.

I'm Svilen -- a freelance software developer & consultant based in London, UK, focusing mostly on Node.js and front-end development. You can find out more about me at [s2g.io](http://s2g.io) or [contact me on twitter](http://twitter.com/svileng).
