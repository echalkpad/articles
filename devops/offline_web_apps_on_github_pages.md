# Offline Web Apps on GitHub Pages

[Original URL](https://hacks.mozilla.org/2015/11/offline-web-apps-on-github-pages/)

> Service Workers are a response to the problems of Application Cache, and they're a powerful and elegant way to offline your web app. But they're also more complex to implement and...

[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers) are a response to the [problems of Application Cache](http://alistapart.com/article/application-cache-is-a-douchebag), and they're a powerful and elegant way to offline your web app. But they're also more complex to implement and maintain.

Meanwhile, [GitHub Pages](https://pages.github.com/) is a great, simple static host for [offline-first apps](http://offlinefirst.org/). But deploying apps to GitHub Pages requires manual configuration, especially if you develop with a team, utilize some variant of the [GitHub Flow](https://guides.github.com/introduction/flow/), and want to set up continuous deployment.

[Oghliner](https://github.com/mozilla/oghliner) is an [npm](https://www.npmjs.com/) package that simplifies both offlining an app with Service Workers and deploying the app to GitHub Pages (including continuous deployment using [Travis CI](https://travis-ci.org/)). Oghliner's goal is to make it as simple as possible to offline and deploy a web app.

To start using Oghliner, install it globally:

```
> npm install --global oghliner
```

If you have an existing app in a GitHub repository, invoke the **integrate** command to configure it. That command copies an offline-manager.js script (which registers the service worker) into your app and reminds you to load that script in your app's page(s)/template(s) (one of the few steps that Oghliner doesn't yet automate).

```
> oghliner integrate
Integrating Oghliner into the app in the current directory…

✓ Copying offline-manager.js to ./… done!

Oghliner has been integrated into the app!

The app needs to load the script offline-manager.js in order to register
the service worker that offlines the app. To load the script, add this line to 
the app's HTML page(s)/template(s):

<script src="offline-manager.js"></script>

And commit the changes and push the commit to the origin/master branch:

git commit -m"integrate Oghliner" --all
git push origin master

Then you can offline and deploy the app using the offline and deploy commands.

ℹ For more information about offlining and deployment, see:
    https://mozilla.github.io/oghliner/
```

If you don't yet have an existing app, start a new app by [creating a new repository](https://github.com/new) on GitHub, cloning it to your local machine, and invoking the **bootstrap** command from its working directory.

```
> git clone git@github.com:mykmelez/offline-app.git
Cloning into 'offline-app'...
…
> cd offline-app/
> oghliner bootstrap
Bootstrapping current directory as Oghliner app…

Your app's configuration is:

Name: offline-app
Repository: git@github.com:mykmelez/offline-app.git
Description: A template app bootstrapped with oghliner.
License: Apache-2.0

Would you like to change its configuration (y/N)? 

Creating files…
✓ Creating README.md
✓ Creating .gitignore
✓ Creating gulpfile.js
✓ Creating package.json
✓ Creating app/favicon.ico
✓ Creating app/fonts
✓ Creating app/index.html
✓ Creating app/robots.txt
✓ Creating app/images/apple-touch-icon-114x114.png
✓ Creating app/images/apple-touch-icon-120x120.png
✓ Creating app/images/apple-touch-icon-144x144.png
✓ Creating app/images/apple-touch-icon-152x152.png
✓ Creating app/images/apple-touch-icon-57x57.png
✓ Creating app/images/apple-touch-icon-60x60.png
✓ Creating app/images/apple-touch-icon-72x72.png
✓ Creating app/images/apple-touch-icon-76x76.png
✓ Creating app/images/favicon-128x128.png
✓ Creating app/images/favicon-16x16.png
✓ Creating app/images/favicon-196x196.png
✓ Creating app/images/favicon-32x32.png
✓ Creating app/images/favicon-96x96.png
✓ Creating app/images/mstile-144x144.png
✓ Creating app/images/mstile-150x150.png
✓ Creating app/images/mstile-310x150.png
✓ Creating app/images/mstile-310x310.png
✓ Creating app/images/mstile-70x70.png
✓ Creating app/scripts/main.js
✓ Creating app/scripts/offline-manager.js
✓ Creating app/styles/stylesheet.css

✓ Creating files… done!
✓ Installing npm dependencies… done!

Your app has been bootstrapped! Just commit the changes and push the commit
to the origin/master branch:

git add --all && git commit -m"initial version of Oghliner app"
git push origin master

Then you can build, offline, and deploy the app using gulp commands.

ℹ For more information about building, offlining and deployment, see:
 https://mozilla.github.io/oghliner/
```

Finally, commit the changes to complete the configuration.

```
> git add --all && git commit -m"initial version of Oghliner app"
```

Now that your app is configured, you can build, offline, and deploy it. Bootstrapped apps include a gulpfile.js build script. To build them, install Gulp globally:

```
> npm install --global gulp
```

Then simply invoke gulp:

```
> gulp
```

To offline your app, invoke the **offline** command to generate the service worker that offlines your app, specifying the directory containing the files to offline.

```
> oghliner offline dist/
Offlining dist/ to dist/offline-worker.js…

✓ Caching "dist/favicon.ico" (384 B)
✓ Caching "dist/images/apple-touch-icon-114x114.png" (278 B)
✓ Caching "dist/images/apple-touch-icon-120x120.png" (285 B)
✓ Caching "dist/images/apple-touch-icon-144x144.png" (321 B)
✓ Caching "dist/images/apple-touch-icon-152x152.png" (320 B)
✓ Caching "dist/images/apple-touch-icon-57x57.png" (242 B)
✓ Caching "dist/images/apple-touch-icon-60x60.png" (242 B)
✓ Caching "dist/images/apple-touch-icon-72x72.png" (247 B)
✓ Caching "dist/images/apple-touch-icon-76x76.png" (247 B)
✓ Caching "dist/images/favicon-128x128.png" (298 B)
✓ Caching "dist/images/favicon-16x16.png" (216 B)
✓ Caching "dist/images/favicon-196x196.png" (380 B)
✓ Caching "dist/images/favicon-32x32.png" (232 B)
✓ Caching "dist/images/favicon-96x96.png" (269 B)
✓ Caching "dist/images/mstile-144x144.png" (323 B)
✓ Caching "dist/images/mstile-150x150.png" (316 B)
✓ Caching "dist/images/mstile-310x150.png" (411 B)
✓ Caching "dist/images/mstile-310x310.png" (610 B)
✓ Caching "dist/images/mstile-70x70.png" (246 B)
✓ Caching "dist/index.html" (3.08 kB)
✓ Caching "dist/robots.txt" (102 B)
✓ Caching "dist/scripts/main.js" (151 B)
✓ Caching "dist/scripts/offline-manager.js" (1.1 kB)
✓ Caching "dist/styles/stylesheet.css" (107 B)
Total precache size is about 10.41 kB for 24 resources.
```

To deploy the app (including the service worker) to GitHub Pages, invoke the **deploy** command, again specifying the directory containing your app's files.

```
> oghliner deploy dist/
Deploying "initial version of Oghliner app" to GitHub Pages…

✓ Cloning git@github.com:mykmelez/offline-app.git into .gh-pages-cache… done!
✓ Cleaning… done!
✓ Fetching origin… done!
✓ Checking out origin/gh-pages… done!
✓ Removing files… done!
✓ Copying files… done!
✓ Adding all… done!
✓ Committing… done!
✓ Pushing… done!
```

All Oghliner commands are also available via a module interface, so you can integrate them into your Node-based build scripts using tools like Grunt and Gulp. If you bootstrapped your app using Oghliner, its gulpfile.js already has **offline** and **deploy** tasks (which you can also use as an alternative to the previous commands).

Finally, invoke the **configure** command to configure an app to automatically deploy to GitHub Pages using Travis CI whenever you merge a change into your app's master branch (provided the build completes successfully and tests pass, of course!).

```
> oghliner configure

Configuring Travis to auto-deploy to GitHub Pages…

Your repository has a single remote, origin.
Ok, I'll configure Travis to auto-deploy the origin remote (mykmelez/offline-app).

To check the status of your repository in Travis and authorize Travis to push
to it, I'll create GitHub personal access tokens, for which I need your GitHub
username and password (and two-factor authentication code, if appropriate).

ℹ For more information about GitHub personal access tokens, see:
 https://github.com/settings/tokens

Username: mykmelez
Password: 

× Checking credentials… error!

You're using two-factor authentication with GitHub.
Please enter the code provided by your authentication software.

Auth Code: 123456

✓ Checking credentials… done!
✓ Creating temporary GitHub token for getting Travis token… done!
✓ Getting Travis token… done!
✓ Deleting temporary GitHub token for getting Travis token… done!
 Creating permanent GitHub token for Travis to push to the repository…
✓ Creating permanent GitHub token for Travis to push to the repository… done!

ℹ You had an existing token for this app, so we deleted and recreated it.

✓ Checking the status of your repository in Travis… done!

Good news, your repository is active in Travis!

✓ Encrypting permanent GitHub token… done!
✓ Writing configuration to .travis.yml file… done!

⚠ You didn't already have a .travis.yml file, so I created one for you.
 For more information about the file, see:
 http://docs.travis-ci.com/user/customizing-the-build/

You're ready to auto-deploy using Travis! Just commit the changes
in .travis.yml and push the commit to the origin/master branch:

git add .travis.yml
git commit -m"configure Travis to auto-deploy to GitHub Pages" .travis.yml
git push origin master

Then visit https://travis-ci.org/mykmelez/offline-app/builds to see the build status.
```

Travis will then deploy successful builds (on the master branch):

[![Travis auto-deploys to GitHub Pages](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/Screen-Shot-2015-11-16-at-16.34.53-500x110.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/Screen-Shot-2015-11-16-at-16.34.53.png)

A few caveats to note:

- Deployments sometimes take a few minutes to show up on GitHub Pages.
- Service Workers require that you load the app over an encrypted (HTTPS) connection. All GitHub Pages can be loaded over such a connection, even though GitHub does not officially support it.
- Service Workers are available in Chrome, Opera, and the Developer Edition of Firefox. They'll ship in Firefox 44.

Mozilla's [Web App Developer Initiative](https://wiki.mozilla.org/Apps#Web_App_Developer_Initiative) engineering team built Oghliner because we think Service Workers are a great way to offline web apps, and GitHub Pages are a great way to deploy them, so we wanted to see how much better the combination could be.

We've used Oghliner in our own projects, like [this presentation](https://mykmelez.github.io/offline-web-apps-on-github-pages/) and [Platatus](https://github.com/mozilla/platatus), and we hope you find it delightful. So [learn more about it](https://www.npmjs.com/package/oghliner), try it out, and let us know how it works for you!

Myk is a Principal Software Architect and in-house entrepreneur at Mozilla, where he nurtures nascent initiatives and bridges the gap between promising experiments and shipping products. He's been a Mozillian since 1999 and has contributed to PluotSorbet, Open Web Apps, Firefox OS Simulator, Jetpack, Raindrop, Snowl, Personas, Firefox, Thunderbird, and Bugzilla. He's just a cook. He's all out of bubblegum.

[More articles by Myk Melez...](https://hacks.mozilla.org/author/mykmozilla-com/)

[More articles by Marco Castelluccio...](https://hacks.mozilla.org/author/mcastellucciomozilla-com/)
