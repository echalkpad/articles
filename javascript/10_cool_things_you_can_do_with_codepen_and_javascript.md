
[Original URL](https://www.sitepoint.com/cool-things-codepen-javascript/)

> Hey web people! I work on CodePen a playground for front end web design and development. One of the things you can do on CodePen is create Pens, which are HTML, CSS, and JavaScript you create and see...

Hey web people! I work on [CodePen](http://codepen.io/) a playground for front end web design and development. One of the things you can do on CodePen is create Pens, which are HTML, CSS, and JavaScript you create and see the result of immediately. Ty North has already told you about some [reasons to use CodePen](https://www.sitepoint.com/9-reasons-should-using-codepen/), so this time, I thought I'd focus on some of the things you can do specifically with JavaScript on CodePen.

## 1\. Add Any Library You Want, Quickly

Every Pen has its own Settings. This is where you can set things like what _External JavaScript_ you'd like to use.

![Adding an external library](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/146494561810-things-add-lib.gif)

It's as simple as selecting a library from the _Quick Add_ dropdown, or even better, just start typing the library and we'll offer matching choices. There are thousands of CDN-hosted libraries we offer through typeahead.

## 2\. Write in ES2015

If you're like me and baby-stepping into [new ES2015 features](https://css-tricks.com/lets-learn-es2015/), you can practice on CodePen by enabling the Babel JavaScript preprocessor.

![Selecting a JavaScript preprocessor](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945632babel.gif)

Now you can use ES2015 features and Babel will process them down to older version of JavaScript so what you write works everywhere.

![View compiled JavaScript](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945650es2015.gif)

Shown here is the `let` keyword, an arrow function, and template literals.

You can also write ES2015 stuff _without_ using Babel as well, but you may run into browser support issues like you would any other cutting edge feature.

## 3\. Use a Real Console for Output and Debugging

You can use your browser's DevTools console on CodePen, of course. But you need to make sure you set the context of it to the demo `<iframe>`, or else it won't work.

Or, you can pop open CodePen's built-in console, which is always in the correct context.

![CodePen's JavaScript Console](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945638codepen-javascript-console.gif)

Mini tip! You might notice the URL change when you open/close differnet code panels. There are four numbers, and stand for HTML, CSS, JS, and Console (in that order). So the URL parameter:

```
?editors=0011
```

Stands for HTML closed, CSS closed, JavaScript open, Console open. Passing a `2` as the last number **maximizes** the console. In this way you could share a Pen where the output is intentionally only for the console.

## 4\. Use React and JSX

Babel, awesomely, also supports the JSX syntax. By adding React and ReactDOM, you have all the ingredients to build in React!

![Hello, World! in React](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945664hello-world.gif)

Try [opening this template](http://codepen.io/pen?template=yOmpBV) to give it a try.

Besides Babel, CodePen offers CoffeeScript, TypeScript, and LiveScript. TypeScript will process the JSX as well!

## 5\. Include Other Pens as Resources

Let's say you wanted to use the JavaScript from _another Pen_. You can do that! Just drop the URL of that other Pen in _External JavaScript_, and CodePen will know what you mean.

![Include another Pen as a resource](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945695pen-as-resource1.png)

This way you can create multiple Pens that all use the same JavaScript, so it may be easier to update. This works for CSS in the same way. For HTML, you include the Pen URL in triple brackets within the HTML itself, like:

![Example of using triple square brackets around a Pen URL to include that HTML](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945735triple-brackets.png)

We also have more robust [Asset Hosting](https://blog.codepen.io/documentation/pro-features/asset-hosting/) as a PRO feature.

## 6\. Ajax Stuff from Other Pens

Speaking of using other Pens as resources, you can access the raw code from Pens at special URLs. For example, if you want to access just the JavaScript from another Pen, add `.js` to the end of the URL, like:

<http://codepen.io/SamyBencherif/pen/ONyQOQ.js>

You could Ajax for that and use it elsewhere. Or cURL for it, or whatever else. This can be useful for storing data in another Pen as to not muddy up the JavaScript in the Pen you are working on.

You can access the raw code of any Pen with these URL extensions:

**If a Pen is...** **Raw Code** **Preprocessed<br>
Code** **HTML** Raw HTML .html N/A Markdown .markdown .html Slim .slim .html HAML .haml .html **CSS** Raw CSS .css N/A Sass .sass .css SCSS .scss .css LESS .less .css **JS** Raw JS .js N/A CoffeeScript .coffeescript .js LiveScript .livescript .js TypeScript .typescript .js Babel .babel .js

## 7\. Teach People Things

In some sense, people can learn from any Pen on CodePen. It's real code you can not only look at but see the results of. Some people even make Pens that are [specifically about teaching](http://codepen.io/netsi1964/pen/QbLLGW) something. Our [blogging feature](http://codepen.io/blogging) is used almost entirely for technical writing in the spirit of teaching.

But CodePen has other, more direct ways of teaching. For example, [Professor Mode](https://blog.codepen.io/documentation/pro-features/professor-mode/), which allows other people to watch you code in real time, as well as chat with you and each other.

![Professor Mode, which allows other people to watch you code in real time](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945716prof-mode.gif)

[Collab Mode](https://blog.codepen.io/documentation/pro-features/collab-mode/) is useful for teaching too: it allows multiple people to work on a Pen _at the same time_, meaning the teaching can happen hands-on.

## 8\. See Major Errors in Your Code as-You-Type

An (in my opinion!) underrated feature of CodePen is that it warns of you of errors in your code right within the editor. JavaScript is no exception here. When CodePen detects an error, it will highlight the line and show an icon. Click the icon to reveal the error message, which may be helpful in understanding the problem.

![JSHint running as you type](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945679inline-errors.gif)

CodePen will even try and prevent you from executing infinite loops (which are bad because they lock up the browser and may prevent you from saving your work).

## 9\. Lint Your JavaScript

You can have CodePen check your JavaScript with the popular [JS Hint](http://jshint.com/).

A tool that helps to detect errors and potential problems in your JavaScript code.

This might uncover problems that aren't execution-stopping errors that we automatically check for.

![JSHint results](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945685jshint.png)

Just in case the error message isn't clear enough, there are handy-dandy _Google it_ links for finding more information.

CodePen lints your _processed_ JavaScript. So if you're using a preprocessor, it will lint the processed code for linting warnings. If JS Hint finds something to warn you about, it will flip over to the compiled code to show you there.

If JS Hint finds nothing, it'll tell you!

![JS Hint found no errors! You're good.](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945690no-errors.jpg)

## 10\. Tidy up Your JavaScript

One click will clean up your code! It's even friendly with JSX.

![CodePen's Tidy feature in action](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1464945729tidy.gif)

If you have any favorite JavaScript features or tricks on CodePen, I'd love to hear 'em!
