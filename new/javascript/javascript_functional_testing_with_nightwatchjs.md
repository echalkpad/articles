# JavaScript Functional Testing with Nightwatch.js

[Original URL](https://www.sitepoint.com/javascript-functional-testing-nightwatch-js/)

> A while back, Eric Elliott wrote JavaScript Testing: Unit vs Functional vs Integration Tests, in which he explained the different types of test and when to use which. In today's article, I...

A while back, Eric Elliott wrote [JavaScript Testing: Unit vs Functional vs Integration Tests](https://www.sitepoint.com/javascript-testing-unit-functional-integration/), in which he explained the different types of test and when to use which.

In today's article, I would like to go into JavaScript functional testing in a little more depth. To do so, we will explore and use the [Nightwatch.js](http://nightwatchjs.org/) library.

But before getting started, allow me to remind you what a functional test is, and why it matters. Roughly speaking, functional testing is a process aimed at ensuring that an application is working as expected from the user's perspective.

We are not talking about technical tests, such as unit or integration tests. Here, the goal is to make sure that a user can seamlessly execute a certain scenario, such as sign in to a platform, buy a product, and so on.

## Introducing Nightwatch.js

[Nightwatch.js](http://nightwatchjs.org/) describes itself an a Node.js powered end-to-end testing framework. It relies on [Selenium](https://github.com/SeleniumHQ/selenium), a project aimed at facilitating web browser automation.

Through a human-friendly syntax, Nightwatch.js makes it possible to "script" scenarios, which are then automatically played by a browser (not necessarily headless).

## Installing Nightwatch

Nightwatch is itself a Node.js module, which means you will need Node installed on your machine. The easiest way to do this is using a [version manager such as nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/). Nightwatch is distributed on npm, so you would install it like any other module--either globally with `-g`, or inside the current project with `--save-dev`.

```
npm install --save-dev nightwatch
```

Nightwatch relies on the Selenium WebDriver API and consequently needs a Selenium WebDriver server. This runs on Java, which means, you also have to install the Java Development Kit (JDK 7+) on your environment. You can [download the JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) from the Oracle website.

Once downloaded and installed, you can make sure that Java is correctly available on your machine with `java -version`. The last step is to download the Selenium standalone server packaged as a jar from the [Selenium downloads page](http://selenium-release.storage.googleapis.com/index.html). I recommend you put it inside a `bin` folder inside your project.

```
your_project/
|
|– bin/
| |– selenium-server-standlone-2.53.1.jar
|
`– package.json
```

Okay, we're all set now. Let's get started.

## Configuring Nightwatch

As you can imagine, Nightwatch has a lot of configuration. Fortunately, we don't have to know everything to get started. The configuration can either live in a `nightwatch.json` file or in a `nightwatch.conf.js` file at the root of your project. I would recommend the later as it is a little more flexible, as well as giving you the ability to add comments.

```
var SELENIUM_CONFIGURATION = {
 start_process: true,
 server_path: 'bin/selenium-server-standalone-2.53.0.jar',
 host: '127.0.0.1',
 port: 4444
};

var FIREFOX_CONFIGURATION = {
 browserName: 'firefox',
 javascriptEnabled: true,
 acceptSslCerts: true
};

var DEFAULT_CONFIGURATION = {
 launch_url: 'http://localhost',
 selenium_port: 4444,
 selenium_host: 'localhost',
 desiredCapabilities: FIREFOX_CONFIGURATION
};

var ENVIRONMENTS = {
 default: DEFAULT_CONFIGURATION
};

module.exports = {
 src_folders: ['tests'],
 selenium: SELENIUM_CONFIGURATION,
 test_settings: ENVIRONMENTS
};
```

_Note: I personally find easier to read a configuration file when it is split into smaller configuration objects, which a JSON file does not allow._

In our case, we tell Nightwatch that our tests will live in a `tests` folder, using a certain Selenium configuration, and certain test settings. Let's go through each chunk:

```
var SELENIUM_CONFIGURATION = {
 start_process: true,
 server_path: 'bin/selenium-server-standalone-2.53.0.jar',
 host: '127.0.0.1',
 port: 4444
};
```

With this configuration object, we tell Selenium to run on `127.0.0.1:4444`, which happens to be the default value for Nightwatch. We also make sure that it boots automatically using the Selenium server we downloaded and stored in our `bin` folder.

_Note: for more advanced usage, be sure to check the [list of all Selenium options](http://nightwatchjs.org/guide#selenium-settings)._

Moving on to the actual testing setup:

```
var DEFAULT_CONFIGURATION = {
 launch_url: 'http://localhost',
 selenium_port: 4444,
 selenium_host: 'localhost',
 desiredCapabilities: FIREFOX_CONFIGURATION
};

var ENVIRONMENTS = {
 default: DEFAULT_CONFIGURATION
};
```

The `test_settings` option from Nightwatch expects an object whose keys are the names of each environment, mapped to a further configuration object. In our case, we haven't set up a custom environment (yet) so we use `default`. Later on, we could have a `staging` or `production` testing environment.

In the environment configuration, we tell Nightwatch which URL to open (which would be different for staging for instance), and what browser should be used to run the tests.

_Note: for more advanced usage, be sure to check the [list of all test options](http://nightwatchjs.org/guide#full-list-of-settings)._

```
var FIREFOX_CONFIGURATION = {
 browserName: 'firefox',
 javascriptEnabled: true,
 acceptSslCerts: true
};
```

In our scenario, we will use Firefox without JavaScript enabled, allowing SSL certificates. We could go further and specify a specific browser version (with `version`) or OS (with `platform`).

_Node: for more advanced usage, be sure to check the [list of all capabilities options](https://github.com/SeleniumHQ/selenium/wiki/DesiredCapabilities)._

Alright, we now have a proper configuration. Time to write the first test!

## Writing a Nightwatch Test

For our test, we will consider a login page at `/login`, containing an email field, a password field, and a submit button. When submitting the form, the user should be redirect to a page saying "News feed".

In our configuration, we specified that the tests are located in a folder named `tests`. Let's create this `tests` folder, as well as a file named `login.js`.

```
your_project/
|
|– bin/
| |– selenium-server-standlone-2.53.1.jar
|
|– tests/
| |– login.js
|
|- nightwatch.conf.js
`– package.json
```

This file will export an object that describes our scenario. Each key (if several) is the name of the test, mapped to a function containing the steps to execute.

```
module.exports = {
 'Login test': function (client) {
 // Steps to execute
 }
};
```

The test function exposes an object providing the API needed to describe a scenario. The first thing to do would be to navigate to the login URL. Then, fill the fields and press the button. Finally, check whether we can spot the "News feed" text.

```
module.exports = {
 'Login test': function (client) {
 client
 .url('http://foobar.qux/login')
 .setValue('input[name="email"]', 'foo@bar.com')
 .setValue('input[name="password]', 'p455w0rdZ')
 .click('button[type="submit"]')
 .assert.containsText('main', 'News feed')
 .end();
 }
};
```

_Note: always use `.end()` to terminate a list of instructions in order to properly shut down the Selenium session._

That was quite straightforward! We can now run our test to see if it works:

```
./node_modules/.bin/nightwatch
```

This should give us something like this:

![Implementing JavaScript functional testing with Nightwatch.js](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1469386795login-test-with-nightwatch-js.png)

_Note: With the release of Firefox 47, [the extension based version FirefoxDriver stopped working](http://www.theautomatedtester.co.uk/blog/2016/selenium-webdriver-and-firefox-47.html). This has been [fixed in Firefox 47.1 and Selenium 2.53.1](https://github.com/eviltester/startUsingSeleniumWebDriver). To run tests using a different browser, consult the [project's wiki](https://github.com/nightwatchjs/nightwatch/wiki)._

One final thing we could do to avoid reaching the Nightwatch binary everytime is create a small npm script in `package.json` to alias it:

```
{
 "scripts": {
 "test": "nightwatch"
 }
}
```

## Improving Nightwatch Tests

Having a lot of functional tests can lead to a lot of duplicated information that makes maintenance (yes, test suites also need to be maintained) difficult. To prevent that, we can use _Page Objects_.

In the world of end-to-end testing, the Page Objects methodology is a popular pattern consisting of wrapping tested pages (or page fragments) into objects. The goal is to abstract away the underlying HTML and general configuration to simplify scenarios.

Fortunately, Nightwatch has a simple way to handle page objects. The first thing we need to do is to add the `page_objects_path` option to the configuration. I feel `tests/pages` makes sense; you can specify any folder you want though.

```
module.exports = {
 src_folders: ['tests'],
 page_objects_path: 'tests/pages',
 selenium: SELENIUM_CONFIGURATION,
 test_settings: ENVIRONMENTS
};
```

Now, we can create a `login.js` file in this folder. The file name will later be used as a key to retrieve all the configuration specified in this file, so I suggest to giving it a sensible name.

In this file, we'll specify a URL and alias some HTML elements with a friendly name to make writing future scenarios easier.

```
module.exports = {
 url: function () {
 return this.api.launch_url + '/login';
 },
 elements: {
 emailField: 'input[name="email"]',
 passwordField: 'input[name="password"]',
 submitButton: 'button[type="submit"]'
 }
};
```

Note that we do not hardcode the URL. Instead, we make it rely on the `launchUrl` option defined in the environment configuration. In this way, our page object is context-agnostic and will work no matter what the environment.

It is now pretty straight forward to modify our test to use the page object. First we need to retrieve the page through the `page` object from the client. Each page object is being exposed as a function named after the name of the page object file (e.g. `login()`).

Then we can replace our CSS selectors with our aliases, prefixed with the `@` symbol to indicate that we are referring to a custom name. That's it.

```
module.exports = {
 'Login test': (client) => {
 const page = client.page.login();

 page.navigate()
 .setValue('@emailField', 'foo@bar.com')
 .setValue('@passwordField', 'p455w0rdZ')
 .click('@submitButton')
 .assert.containsText('main', 'News feed');

 client.end();
 }
};
```

_Note how we terminate the session on the client itself rather than the page._

## Working with Multiple Environments

Being able to run functional tests in different environments is useful to make sure the local work has not broken any user paths, or that staging and production are working the same for instance.

To run the tests in a specific environment, we can use the `--env` option in the CLI. The `default` environment (already in our configuration) is used when we omit the option.

Let's add a staging environment to our configuration.

```
var STAGING_CONFIGURATION = Object.assign({}, DEFAULT_CONFIGURATION, {
 launch_url: 'http://staging.foobar.qux'
});

var ENVIRONMENTS = {
 default: DEFAULT_CONFIGURATION,
 staging: STAGING_CONFIGURATION
};
```

Now when running our tests, the `launch_url` option will be different according to the environment.

```
npm test --env staging
```

## Wrapping Things Up

Let's sum up all this. Nightwatch.js is a JavaScript framework used for writing end-to-end functional tests. It relies on the Selenium WebDriver API, and is able to automatically run different browsers.

Writing tests mostly consists of defining a typical user scenario. There is a simple yet very complete API for this purpose.

From there, I'll leave it to you and encourage you to start writing functional tests for your largest projects in order to make sure never you break a user feature again!
