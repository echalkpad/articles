# Building & Maintaining OUI (Optimizely's UI Library): Part 2/2

[Original URL](https://css-tricks.com/optimizelys-ui-library-oui-2-of-2/)

> The following is a guest post by Daniel O'Connor. Daniel shares more about OUI, the UI library by Optimizely Tom Genoni introduced in Part 1\. Over a year ago we set out on a mission at Optimizely to...

_The following is a guest post by [Daniel O'Connor](https://danoc.me/). Daniel shares more about OUI, the UI library by Optimizely [Tom Genoni](http://atomeye.com/) introduced in [Part 1](https://css-tricks.com/optimizelys-ui-library-oui-1-of-2/)._

Over a year ago we set out on a mission at Optimizely to unify our product design and get a handle on our ever-increasing CSS payload. Fellow UI Engineer Tom Genoni spearheaded this effort in 2014 and created [our Sass framework called OUI](https://github.com/optimizely/oui).

We first integrated OUI into a small part of the Optimizely application and gradually added it to the entire A/B Testing product in the months that followed. We have since developed an entire new product, and a handful of smaller ones, using the framework. This increase in scope presented unique challenges and required us to improve our implementation strategy.

In [Part 1](https://css-tricks.com/optimizelys-ui-library-oui-1-of-2/), Tom wrote about the high-level steps it took to build and evangelize OUI. In this post I'll discuss the Sass architecture decisions we made and processes we added that have allowed us to scale OUI.

## The Anatomy of OUI

OUI was created outside of the Optimizely codebase and lives in its own repository on GitHub. The architecture allows developers to easily modify the default styles by overriding variables and to create partials (components and objects) that play nicely with the framework.

![](https://cdn.css-tricks.com/wp-content/uploads/2016/01/optimizely_oui.png)<br>
The root `my_app.scss` file combines variables and partials from both OUI and the local project. This figure above shows how we typically integrate OUI into an application. In practice, the `my_app.scss` file typically looks like this:

```
// [1] Import OUI and app functions and mixins
@import 'oui/partials/elements/functions';
@import 'oui/partials/elements/mixins';
@import 'my_app/partials/elements/functions';
@import 'my_app/partials/elements/mixins';

// [2] Import OUI and app variables
@import 'oui/oui-variables';
@import 'my_app/my_app-variables'; 

// [3] Import OUI and app partials
@import 'oui/oui-partials';
@import 'my_app/my_app-partials'; 

// [4] Import OUI trumps
@import 'oui/partials/trumps/background';
@import 'oui/partials/trumps/borders';
@import 'oui/partials/trumps/layo   ut';
@import 'oui/partials/trumps/margin';
@import 'oui/partials/trumps/padding';
@import 'oui/partials/trumps/sizing';
@import 'oui/partials/trumps/type';
```

A few notes about the SCSS above:

1. We first import OUI's mixins and functions followed by any custom ones we need.
2. OUI's variables load right before our app's variables. This allows us to amend or override existing variables and introduce custom ones.
3. This is the meat of the file. The first partial imports all of OUI's base rules, components, and objects whereas the the second imports code custom to our product. By listing them here we ensure they pick up all default variables along with any added or changed by our app's variables.
4. Trumps, our utility classes, are loaded last because they perform a specific job and should not be overwritten.

## Version 1: Basic Integration of OUI with npm

Hosting OUI on GitHub gives us the freedom to easily integrate it into projects using [npm](https://www.npmjs.com/), and at first we only used it to pull in the most-recent OUI commit from GitHub that would work in the Optimizely application:

```
npm install --save git://github.com/optimizely/oui.git#commit-hash
```

In fact, any GitHub repository can be installed as a dependency using this method. And specifying the commit hash is a lightweight way to prevent breaking changes in OUI from automatically being pulled in. Using this process, references to OUI in `my_app.scss` are pointed to the `node_modules/` directory, the default install location for npm.

```
// Before:
@import 'oui/partials/elements/functions';

// After:
@import 'path/to/node_modules/oui/partials/elements/functions';
```

Alternatively we could symlink `oui/` to `path/to/node_modules/oui/`.

This basic approach worked for a few months, but questions arose as we began adding OUI to other projects. How can applications _automatically_ pull in OUI bug fixes and other non-breaking changes? How can we provide context when upgrading to a new version of OUI? And how can we keep documentation up-to-date while moving quickly? We knew we could do better!

## Version 2: Advanced Implementation of OUI

To make OUI truly robust and developer friendly, we built on our previous work and took advantage of a number technical tools and best practices. Though these steps are more advanced, we found them to be indispensable.

1. Using npm and Semantic Versioning
2. Adding a change log
3. Implementing a build system
4. Creating a living documentation solution

### Versioning

OUI has been in development for over a year, but breaking changes are still common. A breaking change, such as [renaming flexbox classes](https://github.com/optimizely/oui/pull/108), can require hours of careful find and replaces to support in a large codebase.

As previously mentioned, we initially pulled in OUI from GitHub using npm and included a commit hash to prevent breaking changes from automatically getting fetched. This solution is slow because it requires updating the commit hash in an application's `package.json` for each new change. Ideally, backwards compatible changes such as bug fixes and new components should automatically be pulled in.

We accomplished this by publishing [OUI on npm](https://www.npmjs.com/package/optimizely-oui/), following [Semantic Versioning](http://semver.org/) in [our version numbers](https://github.com/optimizely/oui/releases), and [configuring NPM to accept minor and patch changes](https://docs.npmjs.com/misc/semver). Contributors to OUI identify breaking changes and change version numbers accordingly.

### Keeping a Change Log

Upgrading to the latest version of OUI within a project can be difficult without context or clean release notes. We created [a change log](https://github.com/optimizely/oui/blob/devel/CHANGELOG.md) (based on [Keep a Changelog](http://keepachangelog.com/)) to track all commits and pull requests. This makes it easy to generate helpful [release notes](https://github.com/optimizely/oui/releases) that help developers implementing OUI know exactly what they have to fix or change when bumping up a full version.

### Linting and Compiling SCSS with Travis CI

We use [scss-lint](https://github.com/brigade/scss-lint) to maintain a consistent style and run [node-sass](https://github.com/sass/node-sass) to ensure the SCSS compiles. We have integrated the basic lint and compile checks into GitHub pull requests using GitHub's Travis CI integration.

This ensures the code is clean and prevents glaring bugs from being merged. It also has the added benefit of automating part of the code review process, allowing reviewers to focus on the important parts.

### Creating Living Documentation

We have dozens of engineers that use OUI. To ensure everyone can use the framework, we must have well-documented code. We use ScribeSass, a node.js module built in-house, to auto-generate a documentation website (not live yet) based on comments in the SCSS files code. We'll be releasing it as an open-source project soon.

We introduced OUI to the Optimizely codebase over a year ago. The architecture decisions that make OUI flexible and the investment in tooling and processes has allowed us to integrate it in five repositories, easily onboard new contributors, and enable a team of software engineers to write HTML without introducing new CSS.

We encourage you to poke around the repository on GitHub, read our [CONTRIBUTING.md](https://github.com/optimizely/oui/blob/devel/CONTRIBUTING.md) and [README.md](https://github.com/optimizely/oui/blob/devel/README.md) files, and reach out with questions!

[Twitter](https://twitter.com/intent/tweet?text=Building%20&%20Maintaining%20OUI%20(Optimizely’s%20UI%20Library):%20Part%202/2&url=https://css-tricks.com/optimizelys-ui-library-oui-2-of-2/&via=real_css_tricks) [Facebook](https://www.facebook.com/sharer/sharer.php?u=https://css-tricks.com/optimizelys-ui-library-oui-2-of-2/) [Google+](https://plus.google.com/share?url=https://css-tricks.com/optimizelys-ui-library-oui-2-of-2/)
