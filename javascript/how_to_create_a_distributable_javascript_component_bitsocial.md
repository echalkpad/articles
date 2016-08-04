
[Original URL](http://blog.bitovi.com/distributable-javascript-component/)

> This article will show how to create a JavaScript component for sharing content through social media link (bit-social) and make it distributable to a wide audience using AMD, CommonJS, or even script...

This article will show how to create a JavaScript component for sharing content through social media link (bit-social) and make it distributable to a wide audience using AMD, CommonJS, or even script tags.

[JS Bin on jsbin.com](http://jsbin.com/misafataye/embed?output)

```
<bit-social url="http://cool.article.com" 
 image="http://cool.article.com/wow.jpg" 
 text="Check out this cool article!">
```

In the process of making this component, this article will introduce the DoneJS [plugin generator](http://blog.bitovi.com/create-reusable-components-for-your-donejs-applications/) that makes creating highly portable ES6-based components a snap.

The component that we'll review building will be:

- tested and continuously integrated
- loadable globally via a script tag, or as a module, either AMD, RequireJS, Webpack, or StealJS
- sharable as an npm package or compiled .js file
- editable via a hot-reloading dev server

...all without ever _needing_ to configure a module loader, preprocessor, task runner, test runner, or server. Using DoneJS makes it easy to go from concepts to complete, packaged, tested code without the fatigues of project plumbing.

## Table of Contents

### Try it

The bit-social component displays a set of links for sharing a page to multiple social networks. It requires the URL to be shared, a piece of descriptive text and an option image.

[JS Bin on jsbin.com](http://jsbin.com/jekobopofe/embed?output)

### Use it

After the bit-social script has been loaded the tag can be used in CanJS templates:

```
<bit-social url="http://cool.article.com" 
 image="http://cool.article.com/wow.jpg" 
 text="Check out this cool article!">
```

Where:

- `url` is a full url to the shared page
- `image` is a full url to an image depicting the subject of the shared page
- `text` is a summary of the subject of the shared page

If you want to start using the component right away, install it from npm in your own app with `npm install bit-social --save`. After that load the script via your module loader or by adding the global export via a script tag (and dependencies!) like the JSBin example does.

### Build it

#### Generating project

If you want to follow along with the complete code, checkout [this repo](http://github.com/bithubapp/bit-social).

To start out, I use the [DoneJS plugin generator](http://blog.bitovi.com/create-reusable-components-for-your-donejs-applications/) to build a project foundation. This will download initial Node project dependencies, ask a series of questions (in this example, I accept all the defaults), then initialize the new DoneJS plugin project skeleton with preconfigured module loader, running test stub, hot-reloading development server, etc.

```
donejs add plugin
```

#### Adding tests

I'll be taking a TDD approach on this component, so the first bit of coding will be adding the tests. To build the tests I'm using QUnit for assertions, Sinon for spies, 'faker' for test data and 'valid-url' for URL validation. QUnit has already been downloaded by the project generator, to add the other dependencies, use NPM:

```
# run from the plugin project root directory
npm install valid-url sinon faker --save-dev
```

I add a new file & use faker to get some basic test data:

##### bit-social/src/demo-data.js

```
import faker from 'faker'; 

export default new Array(10).fill(0).map(function() {
 return {
 text: faker.lorem.sentence(),
 url: faker.internet.url(),
 image: faker.image.imageUrl()
 };
}); 
```

Then I write my tests in the stubbed test file:

##### bit-social/src/bit-social_test.js

```
import can from 'can';
import QUnit from 'steal-qunit';
import plugin from './bit-social';
import faker from 'faker';
import sinon from 'sinon';
import validUrl from 'valid-url';
import data from './demo-data';
import 'can/view/stache/';

// reference to original window.open 
// we mock window.open during testing 
var windowOpen;

// the expected use of bit-social in a stache template
var simpleCase = can.stache(
 '<bit-social url="{url}" text="{text}" image="{image}"/>'
);

// define a test suite for 'bit-social' 
// replace window.open during tests and clean it up when finished
QUnit.module('bit-social', {
 beforeEach: function() {
 windowOpen = window.open;
 window.open = sinon.spy();
 },
 afterEach: function() {
 window.open = windowOpen;
 }
});

// define a test case, test basic functionality
QUnit.test('Initialized the plugin', function(){
 // test the bit-social module exports a constructor function
 QUnit.equal(typeof plugin, 'function',
 'imported constructor');

 var frag = simpleCase();

 // test we rendered the expected number of links
 QUnit.equal(can.$(frag).find('a').length, 6, 
 '6 links rendered by default');

 frag = simpleCase({image: faker.image.imageUrl()});

 QUnit.equal(can.$(frag).find('a').length, 7, 
 '7 links rendered when passed an image path');
});


// test our links trigger window.open on click
QUnit.test('Link click triggers popup', function(){
 // render template w/ sample data
 var frag = simpleCase(data[0]);

 // find first link in template & click
 can.$(frag).find('a:first').click();

 // test we've called window.open exactly open
 QUnit.ok(window.open.calledOnce, 'called window.open');

 // test window.open was called with a valid url
 QUnit.ok(validUrl.isWebUri(window.open.args[0][0]), 
 'called with valid url');
});
```

Next I run those tests headlessly and see that unsurprisingly, they fail.

```
# or 'npm test'
donejs test

 ․․․․․

 1 passing (3s)
 4 failing

# ... followed by specifics about failures
```

#### Adding demo page

I'll now add a demo page so I have something to interact with while I develop. Note this page includes the font-awesome package to add icons to the component links. To install this run:

```
npm install font-awesome --save
```

##### bit-social/src/bit-social.html

```
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <title>Bit Social Demo</title>

 <script src="/node_modules/steal/steal.js" 
 data-main="can/view/autorender/"></script>
</head>
<body>
<script type="text/stache" id="main" can-autorender>
 <can-import from="bit-social" />
 <can-import from="font-awesome/less/font-awesome.less!" />
 <can-import from="src/demo-data" {^value.default}='data' />

 <div id="examples">
 {{#each data}}
 <bit-social url='{url}' text='{text}' image='{image}' />
 {{/each}}
 </div>
</script>
</body>
</html> 
```

The page will by empty until we define that bit-social tag in the next step.

#### Implementing component

Since it's really just a list of links, the component code itself is quite simple:

- the DOM element it renders listens for click events on child nodes with the `data-network` attribute

  - `data-network` being the name of the social network link clicked

- that listener templates a URL for the given value of `data-network` with the provided link & description
- then opens that URL in a new window

To try out hot reloading, add the implementations of the methods below gradually while observing the demo page.

Note, this article doesn't include the simple template (bit-social.stache) or stylesheet (bit-social.less) loaded here. Please reference [the repo](http://github.com/bithubapp/bit-social) for these.

##### bit-social/src/bit-social.js

The file starts by with imports followed by some helper functions and data:

```
import can from "can";
// not listed in this article - download from repo
import template from "./bit-social.stache";
import "./bit-social.less";

// social network share urls
var URL_TEMPLATES = {
 googleplus : "https://plus.google.com/share?hl=en&url={url}",
 facebook : "http://www.facebook.com/sharer/sharer.php?u={url}",
 twitter: "https://twitter.com/intent/tweet?text={text}&url={url}&via={via}",
 delicious: "http://www.delicious.com/save?v=5&noui&jump=close&url={url}&title={text}",
 stumbleupon: "http://www.stumbleupon.com/badge/?url={url}",
 linkedin: "https://www.linkedin.com/cws/share?url={url}&token=&isFramed=true",
 pinterest: "http://pinterest.com/pin/create/button/?url={url}&media={image}&description={text}"
};

// omit undefined args from arg object & escape args for query string
function encodeArgs(args) {
 var ret = {};

 Object.keys(args).forEach(function(k) {
 if (args[k] !== undefined) {
 ret[k] = encodeURIComponent(args[k]);
 }
 });

 return ret;
}

// format a url template
function getShareUrl(network, opts){
 return can.sub(URL_TEMPLATES[network], encodeArgs(opts));
}

// return popup launcher helper for given social network
function getLauncher(id, windowOpt) {
 return function(urlOpt) {
 window.open(getShareUrl(id, urlOpt), id, windowOpt); 
 };
}

// launchers for different networks
var launchers = {
 googleplus: getLauncher("googleplus",
 "toolbar=0,status=0,width=900,height=500"),
 facebook: getLauncher("facebook",
 "toolbar=0,status=0,width=900,height=500"),
 twitter: getLauncher("twitter", 
 "toolbar=0,status=0,width=650,height=360"),
 delicious: getLauncher("delicious", 
 "toolbar=no,width=550,height=550"),
 stumbleupon: getLauncher("stumbleupon", 
 "toolbar=no,width=550,height=550"),
 linkedin: getLauncher('linkedin', 
 'toolbar=no,width=550,height=550'),
 pinterest: getLauncher('pinterest', 
 'toolbar=no,width=700,height=300')
};
```

Following is the component code proper. This is a CanJS component definition, using the imported template, a view model (composed of several strings and a computed boolean value), and a single event.

The event is bound to the component tag and is triggered by any click events on children with the `data-network` attribute. The value of that attribute will be read by the event and used to find a matching social network `launcher` function. The call to that function will format the share URL template with the text, link, and optionally, image path that are set in the data model to produce a complete URL. Finally that URL is opened in a popup window.

```
export default can.Component.extend({
 template: template,
 tag: 'bit-social',
 viewModel : {
 define: {
 text: {
 type: 'string'
 },
 url: {
 type: 'string'
 }, 
 image: {
 type: 'string'
 }
 },
 showPinterest: function() {
 // pinterest is enabled if we have an image to show
 return !!this.attr('image'); 
 }
 },
 events: {
 // on link click
 "[data-network] click" : function(el){
 var network = el.data('network'), // get clicked network id
 text = this.viewModel.text;

 // strip hashtags from pinterest & delicious text
 if(network === 'pinterest' || network === 'delicious'){
 text = text.replace(/#/g, '');
 }

 // activate popup for social network
 launchers[network]({
 text: text,
 image: this.viewModel.image,
 url: this.viewModel.url,
 via : "bithubapp"
 });
 }
 }
});
```

To confirm that the above is performing as I expect, I'll rerun my tests:

```
donejs test

 ․․․․․

 5 passing (27s)
```

**Note:** If your test still aren't passing make sure you've included the bit-social.stache & bit-social.less dependencies not included as part of this article. You can find them at [this repo](http://github.com/bithubapp/bit-social).

#### Compile

Considering the tests are passing, in this step I'll bundle the component for use in other loaders:

```
# build CommonJS, AMD and global versions in the /dist dir
donejs build
```

Lastly, a demo of that packaged widget, loadable in any sort of JS browser app.

[JS Bin on jsbin.com](http://jsbin.com/misafataye/embed?html,output)

### Wrapping Up

With the component now working in multiple environments, you can see how DoneJS can create distributable JavaScript components in just a few minutes. Thanks for joining me; please leave a comment & read [David's article](http://blog.bitovi.com/create-reusable-components-for-your-donejs-applications/) for more details on the plugin generator.
