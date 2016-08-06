# How To Make Your Electron App Sexy

[Original URL](https://blog.dcpos.ch/how-to-make-your-electron-app-sexy)

> Electron is excellent.There's a long history of ways to package HTML and Javascript into an installed desktop app. The result usually feels like a web app detached from the rest of the OS.Electron...

**Electron is excellent.

There's a long history of ways to package HTML and Javascript into an installed desktop app. The result usually feels like a web app detached from the rest of the OS.

**Electron makes it easy to do better.**

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1721700/NsPg9NIh2pBqFyQSAPpn4y1U9H8/medium_Screen_Shot_2016-06-11_at_8.53.38_PM.png)

Electron exposes lots of deep OS integrations thru simple Javascript APIs, so you can have a single clean codebase instead of having to code against three different C++ and Objective C libraries for Windows, Linux, and Mac.

Using **npm** and **electron-prebuilt**, you can also keep your build simple and clean. No node-gyp, no native compilation at all. Things that are a pain in most environments, like installers and automatic updates for multiple platforms, are easy here.

[Feross](https://feross.org) and I used Electron to make WebTorrent Desktop recently. We were surprised by Electron's quality and attention to detail.

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1722691/HxMEI2g3ZOhnJwAWNLQx0jl68HM/medium_Screen_Shot_2016-06-13_at_7.42.07_PM.png)

**Here's a list of things you can do to make your Electron app feel native and pro.**

(**If you're new to Electron, check out the [Quick Start](https://github.com/electron/electron/blob/master/docs/tutorial/quick-start.md).** First things first! This post is for people who already know Electron, but want to make their apps even better.)

## **The List**

- Dock and tray integration
- Notifications
- Menus
- Shortcuts
- Drag and drop
- Crash reporting
- Signed installers for all three platforms
- Automatic updaters for Mac and Windows
- Fast startup
- One-step build

**WebTorrent Desktop implements 10 / 10.**

**How does your app score?**

## **Dock and tray integration**

**On Windows and Linux, you can minimize to tray.**

(You can do it on Mac too, but you probably don't need to since Mac has the dock.)

This is great for running in the background or running automatically on system startup.****

**If you're making a decentralized app, you probably want to do this to keep your network healthy.**

**On a Mac, integrate with the dock.**

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1721676/G6j4ddmxtya2Ny9qYP9ekh2f_ZU/medium_Screen_Shot_2016-06-11_at_7.00.52_PM.png)

Show a progress bar when the user might be waiting for something to finish.

Show a badge when work finishes while your app is in the background.

**Caveat:** only some Linux distros support the tray correctly. Check that you're on one of them–otherwise, your users will have no way to quit your program if you hide the window and your tray icon doesn't show up. See [checkElectronTraySupport](https://github.com/feross/webtorrent-desktop/blob/8ac42078d49adfa4b9821632e6a1b4a622ac69e2/main/tray.js#L57) for a workaround.

## **Notifications**

Desktop notifications work on all three platforms. They're really easy to use.

_**_Stay concise. Don't go over 256 characters, or your message will be truncated on Mac OS.

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1721681/2zw7Mnlgs6YvAM6niXYSmjiPYZw/medium_Screen_Shot_2016-06-11_at_7.18.05_PM.png)

Here's an example with custom sounds: a satisfying "ding!" whenever a file finishes downloading.

Play sounds using the normal web audio API. You'll want to preload them. [Here's a nice way to do that](https://github.com/feross/webtorrent-desktop/blob/f0315f7f77d03a5accc368c4bb35067eb15e6d04/renderer/lib/sound.js).

## **Menus**

Electron gives you nice declarative menus on all three platforms.

You can use them in lots of places: context menus, dock icon menus, tray menus. Most are optional but the one you'll always want to implement is the window menu.

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1721701/-_AAFp0N7hFJb6fFZ6Jnr7koGjM/medium_Screen_Shot_2016-06-11_at_7.02.21_PM.png)

**Follow each platform's conventions for what goes where.** For example, if you have Preferences, Mac users will expect to click YourApp > Preferences while Windows users expect Window > Preferences and Linux users expect File > Preferences.

If you have a button for something, give it a menu item anyway. Two advantages: it makes your keyboard shortcuts discoverable, and it makes actions searchable under Help > Search on a Mac.

[See it in action here: menu.js](https://github.com/feross/webtorrent-desktop/blob/4a3ca5459da995c15af36952c07701479e69c472/main/menu.js).

## **Shortcuts**

Electron supports two kinds of shortcuts: menu shortcuts and global shortcuts.

Menu shortcuts are great. New users can click around and learn what's available. Power users can use your app very efficiently.

**Follow each platform's keyboard shortcut conventions.** Electron makes this easy: for example, you can specify "CmdOrCtrl+O" as the accelerator for Open, and it'll be Cmd+O on Mac and Ctrl+O on Windows and Linux. Global shortcuts work even when your app is not focused. For example, if you're running WebTorrent Desktop in the background, playing an audiobook, while using Chrome in the foreground, you can still use the play/pause button on your keyboard (F8 on Mac) to control WebTorrent.

## **Drag and drop**

If you want to let users drag files into your app, you'll need to handle two separate cases.

When someone drags files onto the window of your running app, you'll get the regular HTML5 drag-and-drop events.

When someone drags files onto the icon while your app is running, you'll get a special Electron on-file event.

When someone drags files onto the icon while your app is not running, the OS will run your main process with special command-line arguments. You'll have to handle those.

## **Crash Reporting**

Electron has built-in Crashpad support so that you can get a report when a process crashes.

You might also want to be notified of uncaught Javascript exceptions. You can do this:

- In the main process with **process.on('uncaughtException')**
- In the renderer process using **window.onerror**

Your server will need an API endpoint to save the crash reports. Check out the WebTorrent website code for an [example of how to make one](https://github.com/feross/webtorrent-www/blob/a8fd5b8a353bde867890b56a0e1491d12ed5c67a/server/web.js#L138).

## **Signed Installers**

**You must sign your installers.** Otherwise, you'll get a scary full-page red warning on Windows that says your app is "untrusted", and modern Macs in their stock configuration will refuse to run your app altogether.

Here's a build script that does this for [Mac](https://github.com/feross/webtorrent-desktop/blob/12500dfb6415a665ea412319eef953d842dd2f04/bin/package.js#L250-L278) and for [Windows](https://github.com/feross/webtorrent-desktop/blob/12500dfb6415a665ea412319eef953d842dd2f04/bin/package.js#L365-L369).

**Getting certs.**

To get a Mac signing certificate, sign up for an Apple Developer account. It costs $100 a year.

To get a Windows signing certificate, we recommend Digicert. The documentation for Windows app signing is surprisingly bad. If you go with the wrong vendor, they'll ask you to mail them notarized paperwork. That makes it a slow and annoying process to get the cert. Digicert is easier: they just send you a password via Certified Mail, you go to the post office, show your ID to pick it up, and bam, you get your signing certificate.

**You do not have to go thru the Mac App Store, unless you want to.** If you do, your app will be sandboxed and you may have to change the UX slightly to accommodate the extra restrictions and permission prompts.

**You definitely don't need the Windows App Certification Kit.** WACK is wack, and also kind of obsolete.

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1722666/kYZbRNfF3tkRxSwJsPRSmtPSJFE/medium_Screen_Shot_2016-06-13_at_6.45.31_PM.png)

**Consider starting an organization to own your project's domain and certs.** It looks a lot more legit if a user downloads your app and sees "Do you want to run this file? ... Publisher: Webtorrent LLC", than if they see "Publisher: Jim Bob". There are other advantages as well. In California, starting an LLC costs just a few hundred dollars and a few hours of time.

**Keep your signing certificates safe.** At a very minimum, they must never be sent via email or checked into a Github repo, even a private one. In fact, certs should never ever be online at all. Store them offline, passphrase-protected. Back them up onto a thumb drive, preferably an encrypted thumb drive, and keep it safe.

**Once you get your first million users, your auto updater is basically a botnet with a million nodes.** With great power comes great responsibility.

## **Automatic Updaters**

Your app is getting better every week. Remember Flash back in the day, nagging you to Please Upgrade To The Latest Version? Don't be that guy.

Ever since Chrome popularized autoupdaters eight years ago, users have come to expect software to just continuously get better and fix bugs automatically.

Writing your own reliable auto updater is hard. Fortunately, Electron has already integrated with Squirrel, which makes it easy.

Squirrel only works on Windows and Mac.

For Linux, I recommend checking for updates as you would on the other two platforms, and simply popping up a notification if a new version is available:

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1722693/1oKuG6KVhqM4Mnlz1A1s_q8VNPA/medium_webtorrent-linux-update.png)

Here's a bit of code that handles all three platforms: [updater.js](https://github.com/feross/webtorrent-desktop/blob/62cb304971cb867e5923044df9b7afa2c5f35e78/main/updater.js)

Your server will need an API endpoint to tell the app which version is the latest. This can be really lightweight, and you can conveniently host all of your binaries to Github Releases.

Here's [our server code for the updater API](https://github.com/feross/webtorrent-www/blob/master/server/web.js#L159-204 "Link: https://github.com/feross/webtorrent-www/blob/master/server/web.js#L159-204").

## **One-Step Build**

16 years ago, [a smart guy named Joel Spolsky invented the Joel Test](http://www.joelonsoftware.com/articles/fog0000000043.html "Link: http://www.joelonsoftware.com/articles/fog0000000043.html") for whether a software project has its act together.

#2 on his list: Can You Make A Build In One Step?

Yes, you can! Electron makes it pretty easy to automate your build. And you can do it without any fancy tools like Grunt or Bower.

Check out WebTorrent Desktop's [build script](https://github.com/feross/webtorrent-desktop/blob/master/bin/package.js). With one command, **npm run package**, we can:

- Run the linter and tests
- Package the app for all three platforms
- Create signed installers for Mac and Windows*
- Create binary deltas for the auto updater

* (Almost. Right now we still need to do the Windows code signing on a separate Windows machine, but [there's a bug](https://github.com/electron/windows-installer/issues/27) that should be fixed in the next few weeks that will allow us to build an entire release in a single command on a Mac.)

## **Fast Startup**

You want your app to start quickly and smoothly. If it doesn't, it won't feel native.

Check out Spotify, for example. After clicking the dock icon, the window takes a long time to appear. Once it does, it first flashes grey, then some DOM elements appear, then the style changes, then more elements appear. Each time, it reflows, so the elements bounce around.

It feels like a web page loading over slow internet, not like a native app. (Spotify's UI is built with HTML and Javascript, but it doesn't use Electron.)

**Make your app load quickly.**

**Step 1\. Measure**

Right at the start of our main process index.js, we call console.time('init')

Then, once the window (renderer process) has started and sends us an IPC message saying it's ready, we call console.timeEnd('init')

That gives us a bottom-line number to get as low as possible: the total startup time.

**Step 2\. Get your DOM right the first time**

If you use functional reactive programming, this i easy. What you see is a function of your state object. The state object should be correct and ready to go the first time you render your DOM--otherwise, the DOM might have to change immediately and your app first renders, and the elements will jank around.

In our case, WebTorrent Desktop loads a JSON config file _before_ the first render. This only adds a few milliseconds to our couple-hundred-millisecond startup time.

**Step 3\. Defer loading of big modules**

We bisected using console.time() calls to find out which requires() were taking the longest, and cut our startup time almost in half by loading those lazily. They are loaded either the first time we need them or five seconds after app startup, whichever comes first.

**Step 4\. Colors and CSS**

Make sure your window background color, which electron sends down to the OS, matches your CSS background color. Otherwise, you'll see flashes of white when the app is starting and again when you resize the window quickly.

--

Now we're already doing a lot better than something like Spotify. The window shows up quickly and with the correct background color, then a fraction of a second later the UI shows up.

One final optimization: by adding a CSS fade-in, the window shows up and the UI smoothly but quickly fades in, instead of popping up suddenly. Try it both ways--we think this feels better:

## Conclusion

**1\. Make It Native**

When on Mac, your app should look and feel like a Mac app. When on Windows, it should feel like a Windows app.

**2\. Make It Fast**

Measure your startup speed. Keep it well under a second.

**3\. Keep It Simple**

Your users don't care if you're using Flux and Redux and React and Bower and Grunt and Less and Coffeescript. Plain NPM, plain Javascript, and plain CSS go a long way. Electron supports require() natively, so you don't need Browserify.

WebTorrent Desktop uses no preprocessors at all and no build system except NPM. Spend your energy on things that give your users pleasure!

Bruce Lee said it best–

> The height of cultivation always runs to simplicity.

> Art is the expression of the self. The more complicated and restricted the method, the less the opportunity for expression of one's original sense of freedom.

> To me a lot of this fancy stuff is not functional.

![](https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1721699/rNl29HOvBbNhSfq8PwKyTUnBedY/medium_bruce.jpeg)

Happy Hacking!
