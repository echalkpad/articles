# Create a Text-to-Speech Chrome Extension

[Original URL](https://www.sitepoint.com/create-text-to-speech-chrome-extension/)

> This article was peer reviewed by Marc Towler. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be! Converting text to speech, also known as Speech...

_This article was peer reviewed by [Marc Towler](https://twitter.com/marctowler). Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

Converting text to speech, also known as Speech Synthesis or TTS (text-to-speech), is an artificial way to produce human speech. It is not something new, as people have tried to create machines which produce human speech for at least a thousand years according to [Wikipedia](https://en.wikipedia.org/wiki/Speech_synthesis#History).

Today, TTS is becoming more and more ubiquitous in our lives and everybody can take advantage of it. We will demonstrate this by creating a Chrome extension which converts text to speech. HTML5 brought us the [Speech Synthesis API](https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html) which allows any Web application to convert some arbitrary text string to speech and play it to its users without a cost.

An extension for Chrome typically consists of the following things:

1. A manifest (a required file with metadata)
2. Images (such as the extension's icon)
3. HTML files (such as a popup that will be shown when the users click on the extension's icon)
4. JavaScript files (such as content and/or background scripts which will be explained in a bit)
5. Any other asset that may be used by your application (such as a stylesheet)

## About the Page to Speech Extension

Because of Chrome's popularity and the rise of TTS, we will be creating a Chrome extension which converts text to speech. The extension is going to wait until the user clicks on its icon or presses a special hotkey (`shift + Y`) and then it will try to either find what the user has highlighted on the page that they are currently viewing or it will try to find what is copied to their clipboard. If anything is there, it will convert it to speech by first trying to use the HTML5 Speech Synthesis API and if that is not available – by calling a third-party API.

## Chrome Extension Basics

Each Chrome extension needs to have a file called [manifest.json](https://developer.chrome.com/apps/manifest). The manifest is a JSON-formatted file which contains data crucial to the application ranging from things such as the name, description, icons, and the author of the extension to data defining the needs of the extension – which websites should the extension be able to run on (those would be the permissions which the user must grant) or what files to run when the user is browsing a particular website.

```json
{
  "manifest_version": 2,
  "name": "Page to Speech",
  "description": "This extension will produce English speech to whatever text you highlight on a webpage.Highlight text and click the extension's icon",
  "author": "Ivan Dimov",
  "version": "1.0",
  "icons": {
    "16": "icon16.png",
    "48": "icon48.png",
    "128": "icon128.png"
  }
}
```

Our manifest starts by documenting the name, the description, the author, the version and the icons of the extension. You can provide numerous icons that respond to different sizes in the `icons` object.

```json
 "background": {
 "scripts": ["background.min.js"]
 },
 "content_scripts": [
 {
 "matches": ["http://*/*", "https://*/*"],
 "js": [ "polyfill.min.js", "ext.min.js"],
 "run_at": "document_end"
 }],
```

Then, we have a background script called `background.min.js` (notice that we use minified files) defined in the `background` object. [Background scripts](https://developer.chrome.com/extensions/background_pages) are scripts which are long-running and will continue to run until the user's browser is closed or the extension is disabled.

Afterwards, we have a `content_scripts` array which instructs Chrome to load two JavaScript files on every website request due to the wild-cards `http://*/*` and `https://*/*`. [Content scripts](https://developer.chrome.com/extensions/content_scripts), unlike background scripts, have access to the DOM of the actual website which the user is visiting. Content scripts can both read and make modifications to the DOM of any webpage that they are embedded in. Therefore, our `polyfill.min.js` and `ext.min.js` would be able to read and modify all data on every webpage.

```json
 "browser_action": {
 "default_icon": "speech.png"
 },
 "permissions": [
 "activeTab",
 "clipboardRead"
 ]
}
```

Not so fast! We have another array called `permissions` with which we request to have access only to the webpage that is currently opened by the user (the active tab). We also ask for another permission called `clipboardRead` which would allow us to read the clipboard of the user (so we can convert its contents to speech).

## Coding the Page to Speech Chrome Extension

Firstly, we create our one and only background script which hooks up an event listener that will be fired when a user clicks on the extension's icon. When this happens we call the `sendMessage` function which sends a message to our content script (the content script can read the DOM and find out what the user has highlighted or/and what the user has placed on their clipboard) with the help of the `chrome.tabs.sendMessage(tabId, message, callback)` method. We send the message to the currently opened tab – as this is what interests us and it is what we have access to – with the help of the `chrome.tabs.query` method whose arguments involve a callback that will be called with an argument that contains the tabs that match the query.

```js
chrome.browserAction.onClicked.addListener(function (tab) {
 //fired when the user clicks on the ext's icon
 sendMessage();
});
function sendMessage() {
 chrome.tabs.query({active: true, currentWindow: true}, function(tabs){
 chrome.tabs.sendMessage(tabs[0].id, {action: "pageToSpeech"}, function(response) {});
 });
}
```

Now, the lengthier part is our content script. We create an object which will hold some of the data involved with the extension and then define our initialization method.

```js
initialize: function() {
  if (!pageToSpeech.hasText()) { return;}
  if (!pageToSpeech.trySpeechSynthesizer()) {
    pageToSpeech.trySpeechApi();
  }
},
```

The method checks if the user has not highlighted text or if they do not have anything on their clipboard and just returns in such a case. Otherwise, it tries to produce speech with the HTML5 Speech Synthesis API. If that fails too, it finally tries to use a third-party API.

The method that checks for text does several things. It tries to get an object with the highlighted text with the help of the built-in `getSelection()` method and convert it to a text string with `toString()`. Then, if no text is highlighted it attempts to find the text on the user's clipboard. It does this by adding an input element to the page, focusing it, firing up a paste event with the help of `execCommand('paste')` and then saving the pasted text inside that input in a property. Then it empties the input. In either case, it returns whatever it has found.

```js
  hasText: function() {

  this.data.highlightedText = window.getSelection().toString();

  if (!this.data.highlightedText) {
    var input = document.createElement("input");

    input.setAttribute("type", "text");
    input.id = "sandbox";

    document.getElementsByTagName("body")[0].appendChild(input);
    var sandbox = document.getElementById("sandbox");

    sandbox.value = "";
    sandbox.style.opacity = 0;
    sandbox.focus();

    if (document.execCommand('paste')) {
      this.data.highlightedText = sandbox.value;
    }
    sandbox.value = "";
  }
  return this.data.highlightedText;

 },
```

To enable the user to run the text to speech conversion with a hotkey (hardcoded to `shift + Y`) we initialize an array and set an event listener for the `onkeydown` and `onkeyup` events. In the listeners, we store an index corresponding to the `keyCode` of the pressed key whose value results from the comparison of the type of event `e.type` to `keydown` and is a boolean. Therefore, whenever a key is down, the corresponding key index's value will be set to `true` and whenever a key is released – the index's value will be changed to `false`. Thus, if both indices 16 and 84 are holding a truthy value – we know that the user is using our hotkeys so we initialize the text to speech conversion.

```js
addHotkeys: function() {

  var activeKeys = [];

  onkeydown = onkeyup = function(evt) {
    var e = evt || event;
    activeKeys[e.keyCode] = e.type == 'keydown';

    if (activeKeys[16] && activeKeys[84]) {
      pageToSpeech.initialize();
    }
  };
}
```

To convert the text to speech we rely on the `trySpeechSynthesizer()` method. If HTML5 Speech Synthesis exists in the user's browser (`window.speechSynthesis`) we know that the user is able to use it and so we check if a speech is currently running (we know if it is running through the `pageToSpeech.data.speechInProgress` boolean). We stop the current speech if it is in progress (as the `trySpeechSynthesizer` will start a new speech and we do not want two simultaneous sounds). Then, we set `speechInProgress` to `true` and whenever the speech finishes set the property to a falsy value again.

Now, I do not want to go into details as to why we are using `speechUtteranceChunker` but it is [a bug fix](http://stackoverflow.com/questions/21947730/chrome-speech-synthesis-with-longer-texts) related to Chrome stopping the speech synthesis while still in progress after 200-300 hundred words are uttered. Basically, it splits our text string into many smaller chunks (of 120 words in our case) and calls the Speech Synthesis API with one chunk after another.

```js
trySpeechSynthesizer: function() {
  if (window.speechSynthesis ) {
  //new speech is about to get started
  if (this.data.speechInProgress) {
    polyfills.speechUtteranceChunker.cancel = true;
  }
  this.data.speechInProgress = true;
  var msg = new SpeechSynthesisUtterance(this.data.highlightedText);
  //speechSynthesis.speak(msg);
  // Chrome Implementation BUG: http://stackoverflow.com/questions/21947730/chrome-speech-synthesis-with-longer-texts
  polyfills.speechUtteranceChunker(msg, {
    chunkLength: 120
  },function() {
    //speech has finished
    pageToSpeech.data.speechInProgress = false;
});
```

Finally, if the HTML5 Speech Synthesis API is not available, we try an API. We have the same property which is used to know if it is necessary to stop an already running audio. Then, we directly create a new `Audio` object and pass it the URL to the desired API endpoint since the API we have chosen for demonstration directly streams the audio. We just pass it our API key and the text to convert. We also check if the audio fires an error. In such a case, we just display an `alert` to the user saying that at this time we cannot help (this particular API, [Voice RSS](http://www.voicerss.org/), allows 300 requests on the free tier with which we tested the code).

```js
trySpeechApi: function() {
  if (this.data.speechInProgress) {
    this.data.fallbackAudio.pause();
  }
  this.data.speechInProgress = true;
  this.data.fallbackAudio = new Audio("http://api.voicerss.org/?key=your_api_key&src=" + this.data.highlightedText);
  this.data.fallbackAudio.addEventListener("error", function(evt) {
    alert("Sorry, we cannot produce speech right now. Try upgrading your Chrome browser!");
  })
  this.data.fallbackAudio.play();
  this.data.fallbackAudio.onended = function() {
    pageToSpeech.data.speechInProgress = false;
  }
},
```

In the end, outside of any local scope we call the `addHotkeys` method which will start waiting for the user to press the right hotkey and we set up a listener which will wait until a message is received from the background script. If the right message is received (_speakHighlight_) or the hotkey is pressed, we will initialize our text to speech conversion object.

```js
chrome.extension.onMessage.addListener(function(msg, sender, sendResponse) {
  if (msg.action == 'pageToSpeech') {
    pageToSpeech.initialize();
  }
});
pageToSpeech.addHotkeys();
```

## Conclusion

And voilà, we have a nice Chrome extension that converts text to speech. The concepts here can be used to create Chrome extensions for different purposes. Have you built any interesting Chrome extensions or do you want to built one? Let me know in the comments!

If you liked the idea and want to develop it further, you can find the complete code in our [GitHub repository](https://github.com/sitepoint-editors/page-to-speech). The production version of the extension can be found in the [Chrome Web Store](https://chrome.google.com/webstore/detail/page-to-speech/ncillngfchljkeoiaefmncdcgkpogbhh) if you want to test it out.

## References:

[https://en.wikipedia.org/wiki/Speech\_synthesis\#History](https://en.wikipedia.org/wiki/Speech\_synthesis\#History)

[http://stackoverflow.com/questions/21947730/chrome-speech-synthesis-with-longer-texts](http://stackoverflow.com/questions/21947730/chrome-speech-synthesis-with-longer-texts)
