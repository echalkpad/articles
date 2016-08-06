# Using the Web Speech API

[Original URL](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API)

> The Web Speech API provides two distinct areas of functionality -- speech recognition, and speech synthesis (also know as text to speech, or tts) -- which open up interesting new...

The Web Speech API provides two distinct areas of functionality -- speech recognition, and speech synthesis (also know as text to speech, or tts) -- which open up interesting new possibilities for accessibility, and control mechanisms. This article provides a simple introduction to both areas, along with demos.

## Speech recognition

Speech recognition involves receiving speech through a device's microphone, which is then checked by a speech recognition service against a list of grammar (basically, the vocabulary you want to have recognised in a particular app.) When a word or phrase is successfully recognised, it is returned as a result (or list of results) as a text string, and further actions can be initiated as a result.

The Web Speech API has a main controller interface for this -- [`SpeechRecognition`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition "The SpeechRecognition interface of the Web Speech API is the controller interface for the recognition service; this also handles the SpeechRecognitionEvent sent from the recognition service.") -- plus a number of closely-related interfaces for representing grammar, results, etc. Generally, the default speech recognition system available on the device will be used for the speech recognition -- most modern OSes have a speech recognition system for issuing voice commands. Think about Dictation on Mac OS X, Siri on iOS, Cortana on Windows 10, Android Speech, etc.

## Demo

To show simple usage of Web speech recognition, we've written a demo called [Speech color changer](https://github.com/mdn/web-speech-api/tree/master/speech-color-changer). When the screen is tapped/clicked, you can say an HTML color keyword, and the app's background color will change to that color.

The UI of an app titled Speech Color changer. It invites the user to tap the screen and say a color, and then it turns the background of the app that colour. In this case it has turned the background red.

![](https://mdn.mozillademos.org/files/11975/speech-color-changer.png)

To run the demo, you can clone (or [directly download](https://github.com/mdn/web-speech-api/archive/master.zip)) the Github repo it is part of, open the HTML index file in a supporting desktop browser, navigate to the [live demo URL](http://mdn.github.io/web-speech-api/speech-color-changer/) in a supporting mobile browser like Chrome, or load it onto a Firefox OS phone as an app via [WebIDE](https://developer.mozilla.org/en-US/docs/Tools/WebIDE) (permissions are required to run the API on Firefox OS, see below.)

## Browser support

Support for Web Speech API speech recognition is still getting there across mainstream browsers, and is currently limited to the following:

- Firefox desktop and mobile support it in Gecko 44+, without prefixes, and it can be turned on by flipping the `media.webspeech.recognition.enable` flag to `true` in `about:config`. The permissions settings/UI haven't yet been sorted out however, so permission can't be granted to use it by the user, so it can't be used. This will be fixed soon.

- Firefox OS 2.5+ supports it, but as a privileged API that requires permissions. You therefore need to set the following in the [manifest.webapp](https://developer.mozilla.org/en-US/docs/Web/Apps/Build/Manifest) (and either install it via WebIDE, or get your app verified and made available on the [Firefox Marketplace](https://marketplace.firefox.com/)):

```json
"permissions": {
 "audio-capture" : {
 "description" : "Audio capture"
 },
 "speech-recognition" : {
 "description" : "Speech recognition"
 }
}

"type": "privileged"
```

- Chrome for Desktop and Android have supported it since around version 33 but with prefixed interfaces, so you need to include prefixed versions of them, e.g. `webkitSpeechRecognition`.

### HTML and CSS

The HTML and CSS for the app is really trivial. We simply have a title, instructions paragraph, and a div into which we output diagnostic messages.

```js
<h1>Speech color changer</h1>
<p>Tap/click then say a color to change the background color of the app.</p>
<div>
 <p class="output"><em>...diagnostic messages</em></p>
</div>
```

The CSS provides a very simple responsive styling so that it looks ok across devices.

## JavaScript

Let's look at the JavaScript in a bit more detail.

### Chrome support

As mentioned earlier, Chrome currently supports speech recognition with prefixed properties, therefore at the start of our code we include these lines to feed the right objects to Chrome, and non-prefix browsers, like Firefox:

```js
var SpeechRecognition = SpeechRecognition || webkitSpeechRecognition
var SpeechGrammarList = SpeechGrammarList || webkitSpeechGrammarList
var SpeechRecognitionEvent = SpeechRecognitionEvent || webkitSpeechRecognitionEvent
```

### The grammar

The next part of our code defines the grammar we want our app to recognise. The following variable is defined to hold our grammar:

```js
var grammar = '#JSGF V1.0; grammar colors; public <color> = aqua | azure | beige | bisque | black | [LOTS MORE COLOURS] ;'
```

The grammar format used is [JSpeech Grammar Format](http://www.w3.org/TR/jsgf/) (**JSGF**) -- you can find a lot more about it at the previous link to its spec. However, for now let's just run through it quickly:

- The lines are separated by semi-colons, just like in JavaScript.
- The first line -- `#JSGF V1.0;` -- states the format and version used. This always needs to be included first.
- The second line indicates a type of term that we want to recognise. `public` declares that it is a public rule, the string in angle brackets defines the recognised name for this term (`color`), and the list of items that follow the equals sign are the alternative values that will be recognised and accepted as appropriate values for the term. Note how each is separated by a pipe character.
- You can have as many terms defined as you want on separate lines following the above structure, and include fairly complex grammar definitions. For this basic demo, we are just keeping things simple.

### Plugging the grammar into our speech recognition

The next thing to do is define a speech recogntion instance to control the recognition for our application. This is done using the [`SpeechRecognition()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/SpeechRecognition "The SpeechRecognition() constructor creates a new SpeechRecognition object instance.") constructor. We also create a new speech grammar list to contain our grammar, using the [`SpeechGrammarList()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechGrammarList/SpeechGrammarList "The SpeechGrammarList() constructor creates a new SpeechGrammarList object instance.") constructor.

```js
var recognition = new SpeechRecognition();
var speechRecognitionList = new SpeechGrammarList();
```

We add our `grammar` to the list using the [`SpeechGrammarList.addFromString()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechGrammarList/addFromString "The addFromString() method of the SpeechGrammarList interface takes a grammar present in a specific DOMString within the code base (e.g. stored in a variable) and adds it to the SpeechGrammarList as a new SpeechGrammar object.") method. This accepts as parameters the string we want to add, plus optionally a weight value that specifies the importance of this grammar in relation of other grammars available in the list (can be from 0 to 1 inclusive.) The added grammar is available in the lst as a [`SpeechGrammar`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechGrammar "The SpeechGrammar interface of the Web Speech API represents a set of words or patterns of words that we want the recognition service to recognize.") object instance.

```js
speechRecognitionList.addFromString(grammar, 1);
```

We then add the [`SpeechGrammarList`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechGrammarList "The SpeechGrammarList interface of the Web Speech API represents a list of SpeechGrammar objects containing words or patterns of words that we want the recognition service to recognize.") to the speech recognition instance by setting it to the value of the [`SpeechRecognition.grammars`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/grammars "The grammars property of the SpeechRecognition interface returns and sets a collection of SpeechGrammar objects that represent the grammars that will be understood by the current SpeechRecognition.") property. We also set a few other properties of the recognition instance before we move on:

- [`SpeechRecognition.lang`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/lang "The lang property of the SpeechRecognition interface returns and sets the language of the current SpeechRecognition. If not specified, this defaults to the HTML lang attribute value, or the user agent's language setting if that isn't set either."): Sets the language of the recognition. Setting this is good practice, and therefore recommended.
- [`SpeechRecognition.interimResults`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/interimResults "The interimResults property of the SpeechRecognition interface controls whether interim results should be returned (true) or not (false.) Interim results are results that are not yet final (e.g. the SpeechRecognitionResult.isFinal property is false.)"): Defines whether the speech recognition system should return interim results, or just final results. Final results are good enough for this simple demo.
- [`SpeechRecognition.maxAlternatives`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/maxAlternatives "The maxAlternatives property of the SpeechRecognition interface sets the maximum number of SpeechRecognitionAlternatives provided per SpeechRecognitionResult."): Sets the number of alternative potential matches that should be returned per result. This can sometimes be useful, say if a result is not completely clear and you want to display a list if alternatives for the user to choose the correct one from. But it is not needed for this simple demo, so we are just specifying one (which is actually the default anyway.)

```js
recognition.grammars = speechRecognitionList;
//recognition.continuous = false;
recognition.lang = 'en-US';
recognition.interimResults = false;
recognition.maxAlternatives = 1;
```

**Note**: [`SpeechRecognition.continuous`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/continuous "The continuous property of the SpeechRecognition interface controls whether continuous results are returned for each recognition, or only a single result.") controls whether continuous results are captured, or just a single result each time recognition is started. It is commented out because currently it is not implemented in Gecko, so setting this was breaking the app. You can get a similar result by simply stopping the recognition after the first result is received, as you'll see later on.

### Starting the speech recognition

After grabbing references to the output [`<div>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div "The HTML <div> element (or HTML Document Division Element) is the generic container for flow content, which does not inherently represent anything. It can be used to group elements for styling purposes (using the class or id attributes), or because they share attribute values, such as lang. It should be used only when no other semantic element (such as <article> or <nav>) is appropriate.") and the HTML element (so we can output diagnostic messages and update the app background color later on), we implement an onclick handler so that when the screen is tapped/clicked, the speech recognition service will start. This is achieved by calling [`SpeechRecognition.start()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/start "The start() method of the Web Speech API starts the speech recognition service listening to incoming audio with intent to recognize grammars associated with the current SpeechRecognition.").

```js
var diagnostic = document.querySelector('.output');
var bg = document.querySelector('html');

document.body.onclick = function() {
 recognition.start();
 console.log('Ready to receive a color command.');
}
```

### Receiving and handling results

Once the speech recognition is started, there are many event handlers than can be used to retrieve results, and other pieces of surrounding information (see the [`SpeechRecognition` event handlers list](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition#Event_handlers).) The most common one you'll probably use is [`SpeechRecognition.onresult`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/onresult "The onresult property of the SpeechRecognition interface represents an event handler that will run when the speech recognition service returns a result — a word or phrase has been positively recognized and this has been communicated back to the app (when the result event fires.)"), which is fired once a successful result is received:

```js
recognition.onresult = function(event) {
 var color = event.results[0][0].transcript;
 diagnostic.textContent = 'Result received: ' + color + '.';
 bg.style.backgroundColor = color;
 console.log('Confidence: ' + event.results[0][0].confidence);
}
```

The second line here is a bit complex-looking, so let's explain it step by step. The [`SpeechRecognitionEvent.results`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionEvent/results "The results read-only property of the SpeechRecognitionEvent interface returns a SpeechRecognitionResultList object representing all the speech recognition results for the current session.") property returns a [`SpeechRecognitionResultList`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionResultList "The SpeechRecognitionResultList interface of the Web Speech API represents a list of SpeechRecognitionResult objects, or a single one if results are being captured in continuous mode.") object containing [`SpeechRecognitionResult`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionResult "The SpeechRecognitionResult interface of the Web Speech API represents a single recognition match, which may contain multiple SpeechRecognitionAlternative objects.") objects. It has a getter so it can be accessed like an array -- so the first `[0]` returns the `SpeechRecognitionResult` at position 0\. Each `SpeechRecognitionResult` object contains [`SpeechRecognitionAlternative`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionAlternative "The SpeechRecognitionAlternative interface of the Web Speech API represents a single word that has been recognised by the speech recognition service.") objects that contain individual recognised words. These also have getters so they can be accessed like arrays -- the second `[0]` therefore returns the `SpeechRecognitionAlternative` at position 0\. We then return its transcript property to get a string containing the individual recognised result as a string, set the background color to that color, and report the color recognised as a diagnostic message in the UI.

We also use a [`SpeechRecognition.onspeechend`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/onspeechend "The onspeechend property of the SpeechRecognition interface represents an event handler that will run when speech recognised by the speech recognition service has stopped being detected (when the speechend event fires.)") handler to stop the speech recognition service from running (using [`SpeechRecognition.stop()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/stop "The start() method of the Web Speech API stops the speech recognition service from listening to incoming audio, and attempts to return a SpeechRecognitionResult using the audio captured so far.")) once a single word has been recognised and it has finished being spoken:

```js
recognition.onspeechend = function() {
 recognition.stop();
}
```

### Handling errors and unrecognised speech

The last two handlers are there to handle cases where speech was recognised that wasn't in the defined grammar, or an error occured. [`SpeechRecognition.onnomatch`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/onnomatch "The onnomatch property of the SpeechRecognition interface represents an event handler that will run when the speech recognition service returns a final result with no significant recognition (when the nomatch event fires.)") seems to be supposed to handle the first case mentioned, although note that at the moment it doesn't seem to fire correctly in Firefox or Chrome; it just returns whatever was recognised anyway:

```js
recognition.onnomatch = function(event) {
 diagnostic.textContent = 'I didnt recognise that color.';
}
```

[`SpeechRecognition.onerror`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition/onerror "The onerror property of the SpeechRecognition interface represents an event handler that will run when a speech recognition error occurs (when the error event fires.)") handles cases where there is an actual error with the recognition successfully -- the [`SpeechRecognitionError.error`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognitionError/error "The error read-only property of the SpeechRecognitionError interface returns the type of error raised.") property contains the actual error returned:

```js
recognition.onerror = function(event) {
 diagnostic.textContent = 'Error occurred in recognition: ' + event.error;
}
```

## Speech synthesis

Speech synthesis (aka text-to-speech, or tts) involves receiving synthesising text contained within an app to speech, and broadcasting it out of a device's microphone.

The Web Speech API has a main controller interface for this -- [`SpeechSynthesis`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis "The SpeechSynthesis interface of the Web Speech API is the controller interface for the speech service; this can be used to retrieve information about the synthesis voices available on the device, start and pause speech, and other commands besides.") -- plus a number of closely-related interfaces for representing text to be synthesised (known as utterances), voices to be used for the utterance, etc. Again, most OSes have some kind of speech synthesis system, which will be used by the API for this task as available.

## Demo

To show simple usage of Web speech synthesis, we've provided a demo called [Speak easy synthesis](https://github.com/mdn/web-speech-api/tree/gh-pages/speak-easy-synthesis). This includes a set of form controls for entering text to be synthesised, and setting the pitch, rate, and voice to use when the text is uttered. After you have entered your text, you can press Enter/Return to hear it spoken.

![UI of an app called speak easy synthesis. It has an input field in which to input text to be synthesised, slider controls to change the rate and pitch of the speech, and a drop down menu to choose between different voices.](https://mdn.mozillademos.org/files/11977/speak-easy-synthesis.png)

To run the demo, you can clone (or [directly download](https://github.com/mdn/web-speech-api/archive/master.zip)) the Github repo it is part of, open the HTML index file in a supporting desktop browser, or navigate to the [live demo URL](http://mdn.github.io/web-speech-api/speak-easy-synthesis/) in a supporting mobile browser like Chrome, or Firefox OS.

### Browser support

Support for Web Speech API speech synthesis is still getting there across mainstream browsers, and is currently limited to the following:

- Firefox desktop and mobile support it in Gecko 42+ (Windows)/44+, without prefixes, and it can be turned on by flipping the `media.webspeech.synth.enabled` flag to `true` in `about:config`.

- Firefox OS 2.5+ supports it, by default, and without the need for any permissions.

- Chrome for Desktop and Android have supported it since around version 33, without prefixes.

### HTML and CSS

The HTML and CSS are again pretty trivial, simply containing a title, some instructions for use, and a form with some simple controls. The [`<select>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select "The HTML select (<select>) element represents a control that presents a menu of options. The options within the menu are represented by <option> elements, which can be grouped by <optgroup> elements. Options can be pre-selected for the user.") element is initially empty, but is populated with [`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option "In a Web form, the HTML <option> element is used to create a control representing an item within a <select>, an <optgroup> or a <datalist> HTML5 element.")s via JavaScript (see later on.)

```html
<h1>Speech synthesiser</h1>

<p>Enter some text in the input below and press return to hear it. change voices using the dropdown menu.</p>

<form>
 <input type="text" class="txt">
 <div>
 <label for="rate">Rate</label><input type="range" min="0.5" max="2" value="1" step="0.1" id="rate">
 <div class="rate-value">1</div>
 <div class="clearfix"></div>
 </div>
 <div>
 <label for="pitch">Pitch</label><input type="range" min="0" max="2" value="1" step="0.1" id="pitch">
 <div class="pitch-value">1</div>
 <div class="clearfix"></div>
 </div>
 <select>

 </select>
</form>
```

## JavaScript

Let's investigate the JavaScript that powers this app.

### Setting variables

First of all, we capture references to all the DOM elements involved in the UI, but more interestingly, we capture a reference to [`Window.speechSynthesis`](https://developer.mozilla.org/en-US/docs/Web/API/Window/speechSynthesis "The speechSynthesis read-only property of the Window object returns a SpeechSynthesis object, which is the entry point into using Web Speech API speech synthesis functionality."). This is API's entry point -- it returns an instance of [`SpeechSynthesis`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis "The SpeechSynthesis interface of the Web Speech API is the controller interface for the speech service; this can be used to retrieve information about the synthesis voices available on the device, start and pause speech, and other commands besides."), the controller interface for web speech synthesis.

```js
var synth = window.speechSynthesis;

var inputForm = document.querySelector('form');
var inputTxt = document.querySelector('.txt');
var voiceSelect = document.querySelector('select');

var pitch = document.querySelector('#pitch');
var pitchValue = document.querySelector('.pitch-value');
var rate = document.querySelector('#rate');
var rateValue = document.querySelector('.rate-value');

var voices = [];
```

#### Populating the select element

To populate the [`<select>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select "The HTML select (<select>) element represents a control that presents a menu of options. The options within the menu are represented by <option> elements, which can be grouped by <optgroup> elements. Options can be pre-selected for the user.") element with the different voice options the device has available, we've written a `populateVoiceList()` function. We first invoke [`SpeechSynthesis.getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices "The getVoices() method of the SpeechSynthesis interface returns a list of SpeechSynthesisVoice objects representing all the available voices on the current device."), which returns a list of all the available voices, represented by [`SpeechSynthesisVoice`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice "The SpeechSynthesisVoice interface of the Web Speech API represents a voice that the system supports. Every SpeechSynthesisVoice has its own relative speech service including information about language, name and URI.") objects. We then loop through this list -- for each voice we create an [`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option "In a Web form, the HTML <option> element is used to create a control representing an item within a <select>, an <optgroup> or a <datalist> HTML5 element.") element, set its text content to display the name of the voice (grabbed from [`SpeechSynthesisVoice.name`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/name "The name read-only property of the SpeechSynthesisVoice interface returns a human-readable name that represents the voice.")), the language of the voice (grabbed from [`SpeechSynthesisVoice.lang`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/lang "The lang read-only property of the SpeechSynthesisVoice interface returns a BCP 47 language tag indicating the language of the voice.")), and `-- DEFAULT` if the voice is the default voice for the synthesis engine (checked by seeing if [`SpeechSynthesisVoice.default`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice/default "The default read-only property of the SpeechSynthesisVoice interface returns a Boolean indicating whether the voice is the default voice for the current app (true), or not (false.)") returns `true`.)

We also create `data-` attributes for each option, containing the name and language of the associated voice, so we can grab them easily later on, and then append the options as children of the select.

```js
function populateVoiceList() {
 voices = synth.getVoices();

 for(i = 0; i < voices.length ; i++) {
 var option = document.createElement('option');
 option.textContent = voices[i].name + ' (' + voices[i].lang + ')';

 if(voices[i].default) {
 option.textContent += ' -- DEFAULT';
 }

 option.setAttribute('data-lang', voices[i].lang);
 option.setAttribute('data-name', voices[i].name);
 voiceSelect.appendChild(option);
 }
}
```

When we come to run the function, we do the following. This is because Firefox doesn't support [`SpeechSynthesis.onvoiceschanged`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/onvoiceschanged "The onvoiceschanged property of the SpeechSynthesis interface represents an event handler that will run when the list of SpeechSynthesisVoice objects that would be returned by the SpeechSynthesis.getVoices() method has changed (when the voiceschanged event fires.)"), and will just return a list of voices when [`SpeechSynthesis.getVoices()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/getVoices "The getVoices() method of the SpeechSynthesis interface returns a list of SpeechSynthesisVoice objects representing all the available voices on the current device.") is fired. With Chrome however, you have to wait for the event to fire before populaitng the list, hence the if statement seen below.

```js
populateVoiceList();
if (speechSynthesis.onvoiceschanged !== undefined) {
 speechSynthesis.onvoiceschanged = populateVoiceList;
}
```

### Speaking the entered text

Next, we create an event handler to start speaking the text entered into the text field. We are using an [onsubmit](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onsubmit) handler on the form so that the action happens when Enter/Return is pressed. We first create a new [`SpeechSynthesisUtterance()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance/SpeechSynthesisUtterance "The SpeechSynthesisUtterance() constructor of the SpeechSynthesisUtterance interface returns a new SpeechSynthesisUtterance object instance.") instance using its constructor -- this is passed the text input's value as a parameter.

Next, we need to figure out which voice to use. We use the [`HTMLSelectElement`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement "The HTMLSelectElement interface represents a <select> HTML Element. These elements also share all of the properties and methods of other HTML elements via the HTMLElement interface.") `selectedOptions` property to return the currently selected [`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option "In a Web form, the HTML <option> element is used to create a control representing an item within a <select>, an <optgroup> or a <datalist> HTML5 element.") element. We then use this element's `data-name` attribute, finding the [`SpeechSynthesisVoice`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisVoice "The SpeechSynthesisVoice interface of the Web Speech API represents a voice that the system supports. Every SpeechSynthesisVoice has its own relative speech service including information about language, name and URI.") object whose name matches this attribute's value. We set the matching voice object to be the value of the [`SpeechSynthesisUtterance.voice`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance/voice "The voice property of the SpeechSynthesisUtterance interface gets and sets the voice that will be used to speak the utterance.") property.

Finally, we set the [`SpeechSynthesisUtterance.pitch`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance/pitch "The pitch property of the SpeechSynthesisUtterance interface gets and sets the pitch at which the utterance will be spoken at.") and [`SpeechSynthesisUtterance.rate`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance/rate "The rate property of the SpeechSynthesisUtterance interface gets and sets the speed at which the utterance will be spoken at.") to the values of the relevant range form elements. Then, with all necessary preparations made, we start the utterance being spoken by invoking [`SpeechSynthesis.speak()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/speak "The speak() method of the SpeechSynthesis interface adds an utterance to the utterance queue; it will be spoken when any other utterances queued before it have been spoken."), passing it the [`SpeechSynthesisUtterance`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance "The SpeechSynthesisUtterance interface of the Web Speech API represents a speech request. It contains the content the speech service should read and information about how to read it (e.g. language, pitch and volume.)") instance as a parameter.

```js
inputForm.onsubmit = function(event) {
 event.preventDefault();

 var utterThis = new SpeechSynthesisUtterance(inputTxt.value);
 var selectedOption = voiceSelect.selectedOptions[0].getAttribute('data-name');
 for(i = 0; i < voices.length ; i++) {
 if(voices[i].name === selectedOption) {
 utterThis.voice = voices[i];
 }
 }
 utterThis.pitch = pitch.value;
 utterThis.rate = rate.value;
 synth.speak(utterThis);
```

In the final part of the handler, we include an [`SpeechSynthesisUtterance.onpause`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance/onpause "The onpause property of the SpeechSynthesisUtterance interface represents an event handler that will run when the utterance is paused part way through (when the pause event fires.)") handler to demonstrate how [`SpeechSynthesisEvent`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisEvent "The SpeechSynthesisEvent interface of the Web Speech API contains information about the current state of SpeechSynthesisUtterance objects that have been processed in the speech service.") can be put to good use. When [`SpeechSynthesis.pause()`](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis/pause "The pause() method of the SpeechSynthesis interface puts the SpeechSynthesis object into a paused state.") is invoked, this returns a message reporting the character number and name that the speech was paused at.

```js
 utterThis.onpause = function(event) {
 var char = event.utterance.text.charAt(event.charIndex);
 console.log('Speech paused at character ' + event.charIndex + ' of "' +
 event.utterance.text + '", which is "' + char + '".');
 }
```

Finally, we call [blur()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/blur) on the text input. This is mainly to hide the keyboard on Firefox OS.

```js
 inputTxt.blur();
}
```

### Updating the displayed pitch and rate values

The last part of the code simply updates the `pitch`/`rate` values displayed in the UI, each time the slider positions are moved.

```js
pitch.onchange = function() {
 pitchValue.textContent = pitch.value;
}

rate.onchange = function() {
 rateValue.textContent = rate.value;
}
```
