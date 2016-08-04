# How to Create Cross-Platform Desktop Apps with NW.js

[Original URL](https://www.codementor.io/javascript/tutorial/how-to-create-cross-platform-desktop-apps-with-nwjs)

> After creating two successful apps based on NW.js, Messenger for Desktop and WhatsApp for Desktop, I was told that I should create a tutorial. So here I am, telling you (almost) all there is to know...

After creating two successful apps based on NW.js, [Messenger for Desktop](http://messengerfordesktop.com/) and [WhatsApp for Desktop](https://github.com/Aluxian/WhatsApp-Desktop), I was told that I should create a tutorial. So here I am, telling you (almost) all there is to know about creating apps like mine.

But first, let me introduce you to the hero: [NW.js](http://nwjs.io/), formerly known as node-webkit. From their GitHub [repository](https://github.com/nwjs/nw.js):

> NW.js is an app runtime based on Chromium and node.js. You can write native apps in HTML and JavaScript with NW.js. It also lets you call Node.js modules directly from the DOM and enables a new way of writing native applications with all Web technologies.

## 0\. The Plan

The first step is to find an app to port. Maybe it's your favourite website, or your startup's dashboard, or just a [random website](http://mostexclusivewebsite.com/). Whatever it is, it will work.

I chose to create a desktop app for [Klout](https://klout.com/), an online service that uses social media analytics to measure online influence. I signed up today, and I'm curious whether it will become my go-to sharing tool. I like the elegant UI and the colours they chose, so that's a plus.

It doesn't seem to have HTML5 desktop notifications. I think we could inject some code into the dashboard page to check for notifications if we really wanted to.

Another neat thing I'm thinking of is adding a "Share with Klout" integration on OS X. I'm not sure it's possible to do with NW.js, but with Electron it's possible for sure.

## 1\. Getting Started

We'll start by cloning my [nwjs-starter](https://github.com/Aluxian/nwjs-starter) repository, which should help us get up and running quickly:

```
$ git clone https://github.com/Aluxian/nwjs-starter.git klout
$ cd klout
```

I'm going to replace Starter, which is the default name of the app, with Klout:

```
$ sed -i.bak "s/Starter/Klout/g" **/*.{sh,json,md,js,html,nsi,coffee,desktop}
$ sed -i.bak "s/starter/klout/g" **/*.{sh,json,md,js,html,nsi,coffee,desktop}
$ rm **/*.bak # remove backup files created by sed
```

This looks for `Starter` and `starter` and replaces them with `Klout` and `klout`, respectively. We also need to rename some files:

```
$ cd assets-linux
$ mv icons/256/starter.png icons/256/klout.png
$ mv icons/48/starter.png icons/48/klout.png
$ mv icons/scalable/starter.svg icons/scalable/klout.svg
$ mv starter.desktop klout.desktop
```

Now go to `src/app.html` and replace the `h1` line with this iframe:

```
<iframe src="https://klout.com/login" nwdisable nwfaketop></iframe>
```

The `nwdisable` attribute disables node.js support inside the iframe. `nwfaketop` will replace the default `window` global object with a custom one. I chose to show the login page directly, and it will never know it's running inside an iframe.

Let's add some styling rules too. Remove everything inside `src/app.css` and paste this:

```
html, body, iframe {
 margin: 0;
 padding: 0;
 border: none;
 width: 100%;
 height: 100%;
 overflow: hidden;
}
```

## 2\. Create Assets

Because [Sketch](http://bohemiancoding.com/sketch/) is my favourite design tool, I'll use it to create some icons for the app. I'll use the `design.sketch` file as a starting point.

You can skip this step, and the app will use a generic icon, or you can download the assets I created from [here](http://cl.ly/2s3S1U3l0i3y).

![Klout assets](http://blog.aluxian.com/content/images/2015/07/Screen-Shot-2015-07-07-at-00-31-36.png)

## 3\. Build It

Make sure you have [node.js](https://nodejs.org/) installed:

```
$ node --version
$ npm --version
```

If you want to create packages for Linux, you need [fpm](https://github.com/jordansissel/fpm). If you are not on a Windows machine, you need to install [wine](https://www.winehq.org/) to build for Windows.

To create the Windows installer, you need to have the [makensis](http://nsis.sourceforge.net/Main_Page) command in your PATH.

On OS X, you can use gem and brew:

```
$ sudo gem install fpm
$ brew install wine
$ brew install makensis
```

Finally, it's time to build the app and test it. Install gulp and the node dependencies:

```
$ npm install -g gulp
$ npm install 
```

Run the build task for the platform you want to run it on:

```
$ gulp build:osx64
$ gulp build:linux32
$ gulp build:linux64
$ gulp build:win32
```

I also did a minor tweak. In `src/package.json`, I changed the width to 1080 and the height to 768.

This is what the login screen should look like:

![](http://blog.aluxian.com/content/images/2015/07/klout-app-login.jpg)

And the dashboard:

![](http://blog.aluxian.com/content/images/2015/07/klout-app-dashboard.jpg)

Great job so far! :)

## 4\. Packaging and Upload

Again, depending on what platform you want to build for, run one of these tasks:

```
$ gulp pack:osx64 # dist/Klout.dmg
$ gulp pack:linux32:deb # dist/Klout_linux32.deb
$ gulp pack:linux64:deb # dist/Klout_linux64.deb
$ gulp pack:win32 # dist/KloutSetup.exe
```

If you're on OS X, edit `gulpfile.coffee` and make the `pack:osx64` task depend on `build:osx64` instead of `sign:osx64`. Otherwise, Gulp will try to sign the app with an identity that most probably (or at least I hope so) is not in your keychain.

## 5\. Next Steps

You might be happy with your app as it is, and you'll probably just keep it for yourself or share it with the close ones. But what if, one day, you decide to publish it?

Every time the app checks for updates, it looks in `src/package.json` for `manifestUrl`. That URL should point to a JSON file with a `version` field and an object that looks like this:

```
"packages": {
 "osx64": "https://github.com/.../v0.1.0/Klout.dmg",
 "win32": "https://github.com/.../v0.1.0/KloutSetup.exe",
 "linux32": "https://github.com/.../v0.1.0/Klout_linux32.deb",
 "linux64": "https://github.com/.../v0.1.0/Klout_linux64.deb"
}
```

The app will download that file and compare the versions. If it's a newer version, it will ask the user whether they want to update. If they confirm, the app opens the download link in the browser so the user can download and install it.

### Electron

For the past weeks I've been working on [electron-superkit](https://github.com/Aluxian/electron-superkit). It's just like [nwjs-starter](https://github.com/Aluxian/nwjs-starter): it helps you get up and running fast while offering plenty of features, but it's based on [Electron](http://electron.atom.io/).

Electron seems to be a newer, more performant and featureful version of NW.js. It's being actively worked on by the awesome team that created [Atom](https://atom.io/).

I like it more, and as soon as I finish Electron SuperKit I will port both Messenger and WhatsApp for Desktop to it. I recommend new users to try to learn and use Electron.

I created a repository on my GitHub account and now I'm running `gulp release` to upload the app there. You can download the app from the [releases](https://github.com/Aluxian/klout-desktop/releases) page and you can get all the source code [here](https://github.com/Aluxian/klout-desktop).

Let me know if I missed any steps. If you get stuck, just ask for help below. Happy coding!

This article first appeared on my [blog](http://blog.aluxian.com/). Please subscribe for more!
