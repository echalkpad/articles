# Cross-platform applications in practice

[Original URL](http://blog.airtame.com/how-electron-helps-airtame-to-provide-a-crossplatform-software/)

> From the very beginning, cross-platform capability has been one of our standard requirements at Airtame. It is our ultimate goal for users to be able to stream from any platform at any time. But how...

From the very beginning, cross-platform capability has been one of our standard requirements at Airtame. It is our ultimate goal for users to be able to stream from any platform at any time.

But how can we actually accomplish cross-platform capability? Currently, we have 3 full-time engineers from the Airtame team dedicated to making a truly cross-platform streaming solution. Let's go over how we make it happen.

## Starter Kit

When we started this project 2 years ago, we had just one engineer working on this task. However, limited resources allocated to this project in an early phase have helped us to determine a solution that will be most efficient and proven out of all the tools available for this purpose.

During the selection process, we were driven by the following criteria in order to find the perfect framework for Airtame cross-platform capability:

- The framework has to be able to run another executable (the streaming binary)
- It has to be able to communicate with the discovery module (the piece of software that allows us to see all the Airtames on the network)
- It has to run on Windows 7+, MacOS X 10.8+ and as many UNIX systems as we can
- It has to have access to the filesystem of the OS
- Ability to design our own User Interface
- Proven track record (number of companies using it already)
- Active community and support for the framework

When we were making the initial choice 2 years ago - [QT](http://qt.io/) made most sense to us. It has a fairly good UI library and we could develop it using C++. However, the main drawback of this solution was that implementing the UI was quite cumbersome, especially if you are new to this framework.

In parallel, we noticed [nw.js](http://nwjs.io/) (formerly node-webkit) rising as a technology of choice when developing cross-platform UI application. Main advantage? You write your UI as you would write a website - in a controlled environment! No need for multi-browser compatibility.

We decided to try nw.js. It worked well for us. We managed to write everything for all platforms in only one (-ish) codebase, the whole thing being written in HTML/CSS/JS with [NodeJS](https://nodejs.org/en/). The nw.js's community was quite active and many other companies were using it, so it had a proven track record and it was fairly easy to troubleshoot.

However, we started to spot some limitations of nw.js over time. The project was not updated that often, we could see some bugs (many Linux issues, frameless window issues, etc.), and we were missing features that were important to us (e.g., auto-updater and balloon notification on Windows).

Around the end of June 2015, we had an important backlog of features and bugs that were hanging because of this.

That's when we decided to take a look at [Electron](http://electron.atom.io/) (formerly atom-shell).

## Electron

Electron is in fact very similar to nw.js. It uses the latest version of NodeJS and allows you to write cross-platform applications using web technologies (if you want to know more about the differences between Electron and nw.js, just follow [this link](http://electron.atom.io/docs/v0.35.0/development/atom-shell-vs-node-webkit/).)

Nevertheless, Electron is updated more frequently, it is heavily adopted, and apparent bugs can be fixed quickly by either support from the community or the official maintainers. Moreover, Electron has more functionalities than nw.js (platform specific or not). All of that brings us closer to implementing the best user experience.

One of the main advantages of the Electron framework is that it provides core functionalities useful to almost every software: Auto update, Crash reporter, installer creation, system-specific features, etc.

### Architecture

Today, the architecture of the Airtame software is designed as follows:

![Airtame widget architecture](http://blog.airtame.com/content/images/2015/12/airtame-widget-diagram.png)

_High level architecture of the Airtame software on computer. It highlights the separation in modules, which is at the heart of our software._ Main advantages of the above architecture are as follows:

- Have different release process for each "_module_"
- Have a clear division of responsibility in the code
- Be able to replace any module easily without impacting the rest of the application
- Provide an _HTTP API_ that other applications can talk to
- _Make the testing easier_. During the development phase we can test the front-end directly in Google Chrome if the server is running. On the other hand, it is easier to mock the API of the core functionalities of Electron (like window management and other system-related SDK)

### Testing

We have divided the automatic testing into 4 stages:

- Stage 1: All of the node modules tested separately (node-airtame and node-discovery)
- Stage 2: Front-end of the application
- Stage 3: Back-end of the application
- Stage 4: The application as a whole - we have integration and functional tests to verify the functionality of a full feature or scenario (still at an early stage)

We chose [Mocha](https://mochajs.org/) for the testing job and we are happy with it. Every time we submit a change, the tests are triggered in order to validate that there was no regression and that the application is still working as expected. If the test fails, you will be unable to merge the change.

When it comes to the manual testing, we have a "QA" corner in the office along with a set of physical machines and VMs allowing us to test the user case in a real environment. The QA corner is composed of a large variety of systems:

- Windows (7, 8.1, 10)
- MacOS (10.8, 10.9, 10.10.5, 10.11)
- Ubuntu (14.04, 15.04, 15.10)

We are currently working on having even more systems set up through Virtual Machine and other physical devices in order to make Airtame the most cross-platform friendly application.

![QA corner at Airtame](http://blog.airtame.com/content/images/2015/12/QA-Corner.jpg)

_Our QA corner where we manually test the software and dongle. We have different hardware and virtual machines in order to cover as many cases as we can._

## What's next

We will continue to follow the Electron evolution process. We are planning to take a proactive role by contributing to the development of the framework ourselves.

We will continue focusing on stability and usability of the application. The new architecture of the software will help us to write more tests and to divide even more clearly the responsibility of each component. We are also internally improving our QA process (which will be a part of a future post on our QA process).

We are also listening to our users and we are trying to adapt and fix the issues to always provide the best experience.

Finally, we plan to Open Source the application. We are thrilled about this idea and we will keep you posted about its development.

If you want to download the software, follow [this link](https://airtame.com/download/).

We're always on the lookout for talented engineers who are passionate about writing clean, maintainable code! If this sounds like you, get in touch with us at [tech-jobs@airtame.com](mailto:tech-jobs@airtame.com).
