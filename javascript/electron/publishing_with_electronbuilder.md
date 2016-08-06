# Publishing with electron-builder

[Original URL](http://electron.rocks/electron-builder-explained/)

> This article is part of the Distributing Electron apps series. This article was updated to demonstrate working with electron-builder v5\. Go-to tool for building Electron apps is electron-builder....

_This article is part of the [Distributing Electron apps](http://electron.rocks/distributing-electron-apps/) series._

**This article was updated to demonstrate working with electron-builder v5.**

Go-to tool for building Electron apps is [electron-builder](https://www.npmjs.com/package/electron-builder). When I first tried to build app, documentation was pretty scarce so I had to do a bit of digging myself. Docs are a bit better now, but I'll try to explain it in more detail and point out some gotchas.

Electron-builder is using two `package.json` structure. This means you'll have one `package.json` in your root folder and one in your app folder. In my sample application I'm using following structure:

```
[ea-todo]
├── [app]
 ├── ...
 ├── main.js
 ├── package.json <- application package.json
├── ...
├── [build] <- build resources
├── [dist] <- generated distributions
├── package.json <- development package.json
```

As it is stated in electron-builder docs, two `package.json` structure is useful for separating your development and application dependencies. As of electron-builder v3, all build specification is now defined in development `package.json`.

_Note: Although it is said that you don't have to use two `package.json`, I had problems building when using single one. For now, I recommend going with the flow and using two files._

Your development `package.json` is going to be used to specify all metadata for building application for multiple platforms. But first, in your root let's add electron-builder:

```
npm install electron-builder --save-dev 
```

## Application package.json

In your application `package.json`, at minimum, you must have `name`, `description`, `version`

```
{
 "name": "eatodo",
 "description": "Simple TODO application built with Electron and AngularJS.",
 "version": "0.0.1"
 "author": "Vjekoslav Ratkajec <[email protected]>"
 "main": "main.js"
}
```

Now couple of **important** notes:

- Be sure to have simple `name` meaning no spaces or dashes (I had problems with packing for Windows when using minus in name).
- When building application and auto-update packages, it will read this `version` number and create packages based on it (be sure to update it when publishing new version of your app).
- Building for Linux failed when I didn't have email defined for `author`.

## Development package.json

In your development `package.json` you need to include all those dependencies that you need to run and build stuff.

```
{
 ...
 "devDependencies": {
 "electron-builder": "~5.12.X",
 "electron-prebuilt": "~1.2.X"
 },
 "build": {
 "appId": "hr.creaticon.eatodo",
 "app-category-type": "public.app-category.productivity",
 "dmg": {
 ...
 },
 "win": {
 "iconUrl": "http://eatodo.s3.amazonaws.com/icon.ico"
 }
 }
}
```

So, I've added build property and you need to at least define `appId` and `app-category-type`. This properties are used by `electron-packager`. [Here](https://www.npmjs.com/package/electron-packager#packageropts-callback) is the list of all possible properties.

**Beware** that `iconUrl` needs to be URL and not local path. This is because it is needed on runtime by auto-updater for Windows.

I'll be making separate posts on publishing for Windows and OS X, but I'll make couple of notes here:

- Under the hood, `electron-builder` uses `appdmg` to publish for OS X, so refer to [all possible options](https://www.npmjs.com/package/appdmg#json-specification).
- Under the hood, `electron-builder` uses `windows-installer` to publish for Windows, so refer to [all possible options](https://github.com/electron/windows-installer#usage).

### Build files

Lastly, to generate distribution files, `electron-builder` will have to get some UI resources. Electron-builder by default looks for this files in `build` directory:

```
[your-app]
├── ...
├── [build]
 ├── background.png
 ├── icon.icns
 ├── icon.ico
├── ...
```

**Watch out**:

- Resources need to be in this folder by these names, since `electron-builder` is expecting them.
- Make sure that you icons are at least 256x256px since building will fail for Windows if it is smaller.

Handy tool to convert your image to icons format: <https://iconverticons.com/online/>

## Running everything

All of the things should be configured correctly now. One thing that can ease running is defining npm scripts. Here is excerpt from my development `package.json`:

```
"scripts": {
 "postinstall": "install-app-deps",
 "start": "electron ./app --enable-logging",
 "dev": "NODE_ENV='development' npm run start",
 "dist": "build -mwl --x64 --ia32"
}
```

Couple of notes here:

- When you installed `electron-builder` in your `node_modules/.bin` folder you have `build` which is going to be used to run build process and since executing `npm run` command will look for binaries in that folder, we don't need to put explicit path to it.
- Build is made in parallel, so is best to define multiple targets and build at once.

## Finally

```
npm run dist 
```

All of your generated packages are available in `dist` folder. Although this will generate you files which you can install on Windows and OS X, we need to add some code to our app to integrate fetching and installing automatic updates.

_Next, read how to [publish for OS X](http://electron.rocks/publishing-for-os-x/)._
