# Building and deploying application

[Original URL](http://electron.rocks/building-and-deploying-application/)

> Article updated to use electron-builder v4+ This article is part of the Distributing Electron apps series. Goal Make one-liner to build and deploy your app! Cut to the chase Put this in development...

**Article updated to use electron-builder v4+**

_This article is part of the [Distributing Electron apps](http://electron.rocks/distributing-electron-apps/) series._

## Goal

Make **one-liner** to build and deploy your app!

## Cut to the chase

Put this in development (root) `package.json`:

```
"scripts": {
 "dist": "build -mwl --x64 --ia32",
 "prerelease:osx": "rm -rf release/osx && mkdirp release/osx",
 "release:osx": "cp -rv dist/osx/*.{dmg,zip} release/osx && PACKAGE_VERSION=$(cat app/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]') && echo $PACKAGE_VERSION >> ./release/osx/VERSION",
 "prerelease:win32": "rm -rf release/win32 && mkdirp release/win32",
 "release:win32": "cp -v dist/win-ia32/{RELEASES,*.nupkg,*.exe} release/win32",
 "prerelease:win64": "rm -rf release/win64 && mkdirp release/win64",
 "release:win64": "cp -v dist/win/{RELEASES,*.nupkg,*.exe} release/win64",
 "release": "npm run release:osx && npm run release:win32 && npm run release:win64",
 "deploy": "PACKAGE_VERSION=$(cat app/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]') && aws s3 sync release/ s3://eatodo/updates/$PACKAGE_VERSION/ --acl public-read && aws s3 sync s3://eatodo/updates/$PACKAGE_VERSION/ s3://eatodo/updates/latest/ --delete --acl public-read",
 "all": "npm run dist && npm run release && npm run deploy",
 }
```

_You can make your build with `Gulp`, `Grunt`, `npm` or whatever you'd like. I've decided to keep things simple and use `npm script` ([awesome post that I had to link to](http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/))._

## Creating dist files

First off, we need to actually pack up our app using `electron-builder`. In development `package.json`, I've created following scripts:

```
"dist": "build -owl --x64 && build -wl --ia32"
```

Electron-builder can take care of packing for multiple platforms in parallel. Once this is done, you should see your `dist` folder filled with goodies.

## Prepare for deploying

Now that we've created distributables, we have to put them in specific structure that will be uploaded to S3\. After some testing, I've came up to conclusion that the best structure for my projects is following:

```
[folder-version]
├── [osx]
 ├── eatodo-0.0.1.dmg
 ├── eatodo-0.0.1-mac.zip
 ├── VERSION
├── [win32]
 ├── eatodo-0.0.1-ia32-full.nupkg
 ├── eatodo Setup 0.0.1-ia32.exe
 ├── RELEASES
├── [win64]
 ├── eatodo-0.0.1-full.nupkg
 ├── eatodo Setup 0.0.1.exe
 ├── RELEASES
```

For OS X, relevant files are:

- `.dmg` - Installer for user
- `.zip` - App update package
- `VERSION` - File containing string representation of application version (read from your application `package.json`)

File `VERSION` is needed since our server which will be used to provide updates to applications need to see which is the latest version. Be patient and read further.

For Windows, it's basically c/p of `electron-builder` output.

To make this, I've created some scripts again:

```
"prerelease:osx": "rm -rf release/osx && mkdirp release/osx",
"release:osx": "cp -rv dist/osx/*.{dmg,zip} release/osx && PACKAGE_VERSION=$(cat app/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]') && echo $PACKAGE_VERSION >> ./release/osx/VERSION",
"prerelease:win32": "rm -rf release/win32 && mkdirp release/win32",
"release:win32": "cp -v dist/win-ia32/{RELEASES,*.nupkg,*.exe} release/win32",
"prerelease:win64": "rm -rf release/win64 && mkdirp release/win64",
"release:win64": "cp -v dist/win/{RELEASES,*.nupkg,*.exe} release/win64",
"release": "npm run release:osx && npm run release:win32 && npm run release:win64"
```

Scripts `prerelease` are run automatically by npm and are just used to clean up and prepare folder structure. Somewhat complex step is `release:osx` where version from your application `package.json` is read and saved as `VERSION` file.

## Deploy

We have all the files ready in `release` folder. Awesome! Now to push it to S3\. I suggest [installing](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) `aws` and using it to transfer files to S3\. I tried `s3cli` for the same purpose, but it had problems handling files this big (remember that its min ~40MB).

So here is the `npm script` for that as well:

```
"deploy": "PACKAGE_VERSION=$(cat app/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]') && aws s3 sync release/ s3://eatodo/updates/$PACKAGE_VERSION/ --acl public-read && aws s3 sync s3://eatodo/updates/$PACKAGE_VERSION/ s3://eatodo/updates/latest/ --delete --acl public-read"
```

Again, it will read the package version, and upload contents of `release` folder to S3 and will create folder with name of the version. After that it will copy contents from that new folder to `latest` folder. This `latest` folder is key folder which is targeted for auto updating.

Since contents are changed, next time application is started on Windows, it will automatically pick up new files from `RELEASES` file. On OS X, your server needs to refresh and read contents of `VERSIONS` file so that next requests will compare with new version.

## As I promised...

```
"all": "npm run dist && npm run release && npm run deploy"
```

**One-liner**: `npm run all`.

I would love to hear some improvements or how you're handling your projects...

***Note:** You can't use this to deploy for Linux. Automatic updates doesn't work for it. See note about Linux in [Electron documentation](http://electron.atom.io/docs/api/auto-updater/#linux).*

Take care!
