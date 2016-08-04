# Understanding how to use the Browser Developer Tools | Codementor

[Original URL](https://www.codementor.io/development/tutorial/javascript-css-html-tutorial-front-end-development-tools)

> This article will teach beginners to front-end web development all they need to know about how to use browser developer tool while experienced developers may also learn some new tips & tricks...

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/dev_tools.png)This article will teach beginners to front-end web development all they need to know about how to use browser developer tool while experienced developers may also learn some new tips & tricks through this post.

But surprisingly, they are not used to their full potential. A lot of developers are still unaware of the full power of the dev tools. So here, I would like to demonstrate some things to boost your workflow, and introduce you to some things which you might be unaware of. This is not just for beginners or even intermediates; if you are an expert, you might still find something new for yourself! ![:)](http://cmsadmin.codementor.io/wp-includes/images/smilies/simple-smile.png)

If you are completely new to dev tools, you should familiarize yourself with them and play around a bit. Pressing F12 would launch the developer tools on most new browsers on a desktop. On chrome, they look something like this:

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool1.png)

## The Elements tab

The Elements panel (which is shown above, open by default), shows an HTML tree, listing all DOM elements. On the right, the sidebar shows properties related to the presently selected element. By default, the CSS style rules are listed. You can also see the box-model for the element selected, event listeners attached to it, etc.

## Network Tab

This tab monitors _all_ incoming and outgoing HTTP requests from the web-page. If your webpage uses AJAX, you can track the AJAX requests as well from here.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool2.png)

Clicking on any request shows further details about it.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool3.png)

## Sources Tab

This is probably the most powerful place in the developer tools. Explaining each and every function of this tab is not possible in one article, but here is an overview.

The sidebar shows the directory listing of all the static resources loaded for the webpage presently open. Clicking on any one will open that resource in the File Area.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool4.png)

The sidebar on the right is for JavaScript inspection. By clicking on a line number, you can add a breakpoint for that line of the script open in the file area. When that line is about to be executed, script execution will be paused.

For ease of use, you can hit the Esc key to toggle the split console drawer, which allows you to see the console (and some other tabs) together with any of the main tabs.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool5.png)

So, say you want to inspect a variable defined in the function. You can't do that directly, since it is not available in the global scope, but you can do this by adding a break-point at the function declaration. When the function is about to be executed, the debugger will be invoked, and you step through the lines one by one, by clicking the "Step over next function call" button.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool6.png)

During this, all the variables accessible on that line would be accessible from the console, and would also be listed in the Scope Variables section on the right sidebar.

## Resources

This tab will list the different resources a webpage saves on the computer. Like `localStorage`, `sessionStorage`, cookies, etc.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool7.png)

You can even edit the resources right from the dev tools, in real-time. This is useful for debugging Single Page Applications that save a lot of data like config etc. in `localStorage`, or a cookie heavy site.

## The console

The console serves the purpose of a log file, as well as an open playground.<br>
You can type any command in the console and then hit enter. Whatever the return value of the command is will be printed in the console.

![](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool8.png)

You can also print to the console from within your script files via the `console.log` function.

```
var myVar = 'some data';
console.log(myVar); 
```

For DOM elements and very large objects, using `console.dir` instead of `console.log` can be beneficial, since it shows a directory listing kind of structure, which is expandable and collapsible. More console functions are listed at [MDN Console Web API article](https://developer.mozilla.org/en-US/docs/Web/API/console).

### Workflow

These are some little hidden gems in the developer tools, which are otherwise overlooked.

#### Find in styles

While inspecting the CSS style rules for an element on a page with a lot of styles, you might get frustrated. However, you can filter the style rules easily!

![enter image description here](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool9.png)

This is pretty useful when you are using a CSS framework like bootstrap.

#### DOM Breakpoints

If you are doing a lot of DOM manipulation, and something is not working as expected, you can add breakpoints directly to the DOM elements! Just right click on the element in the Elements panel, select Break on, and check the options at which you want the script to break.

![enter image description here](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool10.png)

Now whenever changes are made to that DOM element, the script execution will be paused, and the debugger will be invoked, allowing you to inspect the state of the application, and identify the problem(s).

#### Pause script execution

Hitting F8 while dev tools are open would immediately pause any scripts running. This is helpful when you have async code running, or JavaScript animations running.

![enter image description here](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool11.png)

Other than this, breakpoints let you inspect all variables at the current execution frame from the console, which allows you to access variables and functions which are otherwise hidden from the global scope.

#### Snippets

Snippets are the advanced version of the console. Snippets allow you to write multi-line JavaScript code, save them in the dev tools memory (they are preserved forever, until you delete them), have a git style version history (!), have smart code auto-completion, syntax highlighting, and what not! They utilize the same power of break-points too!

To use snippets in chrome developer tools, open the sources tab. On the sidebar on the left, click on the Snippets tab, and right click and create a new snippet. Give it a new name, if you like. And start writing your code!

![enter image description here](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool12.png)

Snippets are really great for prototyping. A similar thing called ["Scratchpad"](https://developer.mozilla.org/en-US/docs/Tools/Scratchpad) exists for Firefox, giving the same functionality.

#### Workspaces

Workspaces in chrome dev tools allow you to work on the code on your file system directly from the dev tools, and make persistent file changes, while utilizing the full power of break-points, call stack etc.

To use workspaces, simply right click on the sidebar at the left of the Sources tab, and select "Add Folder to Workspace", and follow through the guide.

![Image from developer.chrome.com licensed under the cc-sa3.0 license](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool13.png)

This is pretty useful when you are testing something out which has a lot to do with the front-end, and you want live interaction with the browser, as your code changes.

Beginners to JavaScript are [advised to focus on core ECMAScript only](https://stackedit.io/viewer#%21provider=gist&gistId=ca3770290d34b05b68d8&filename=HowToLearnJavaScript.md), and learn APIs like the DOM API only after that. For this, workplaces can be of great help, since you can write and execute full sized multi-line scripts persistently within the same environment.

More details can be found at the [documentation on Chrome Developers Website](https://developer.chrome.com/devtools/docs/workspaces).

If you want to inspect some minified (compressed code, resulting in unreadable code for humans), you can pretty print the code from the dev tools!

![enter image description here](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool14.png)

It works for CSS, HTML, and JavaScript!

#### Inspecting `<iframes>` from the console

If one of your scripts are in an iframe, which you wish to inspect in the console, you might find that directly typing the variable names defined for the iframe won't work.

For instance, if you have an `iframe` in your page, and the `iframe` contains an element with the id `xyz`, then typing `document.getElementById('xyz')` in the console directly won't give the element, because the `document` would refer to the parent page.

You might have encountered this if you have used [JSFiddle](http://jsfiddle.net). But you can change the window frame you are inspecting from the console!

![enter image description here](https://s3.amazonaws.com/codementor_content/2015-Jan-Week2/devtool15.png)

#### The console API

The console has a very nice API, specially for webkit based browsers like Chrome. Here are 5 quick tips for the Chrome Web Console:

1. `$` is an alias to `document.querySelector`, and `$$` is an alias to `document.querySelectorAll`.
2. `$0, $1... $4` give reference to the last 4 DOM elements selected from the DOM inspector. (So when you have to select an element, right click, and inspect element, then type `$0` in the console!)
3. `$_` holds the value for the last expression evaluated in the console. Useful when you are running a lot of expressions simultaneously. (Chrome Only).
4. `debug(function)`, typing this in the console would watch for the function passed as the first parameter to be called. When it is called, the debugger will be invoked, and the script would be paused. (Chrome only).
5. `getEventListeners(domElement)` would list all the event listeners attached to the DOM element. (Chrome only).

You can read more about the entire console API, and the command line API at [the documentation on Chrome Developers site](https://developer.chrome.com/devtools/docs/console-api).

Also, the Mozilla Web team recently announced the first browser made for developers – [Mozilla Firefox Developer Edition](https://www.mozilla.org/en-US/firefox/developer/), which includes special features to ease front-end development, and AJAX. It also comes with the Web IDE and App development resources.

### Conclusion

The dev tools are really very powerful and provide a great, open playground for beginners to learn, speculate, experiment, and think.

If you are a beginner, make yourself comfortable with the dev-tools from the start. It would help you in your due course of learning JavaScript, as well as HTML and CSS.

For intermediates and experts, they can prove to be of great help for prototyping small modules and stuff. More advanced features are being added to the dev-tools with time, like native Canvas debugging and inspection, additional console methods to ease data inspection, etc. Using them only does good.

[Codementor Awal Garg](https://www.codementor.io/awalgarg) is a web developer fluent in HTML/CSS and JavaScript for the frontend, and also uses JavaScript at the backend with node and express.

[![Awal Garg](https://www.gravatar.com/avatar/55df9170ae5f3f085f8ea17dbfde2b93?d=mm&s=200)](https://www.codementor.io/awalgarg)**Need Awal's help? Book a 1-on-1 session!**

[](https://www.codementor.io/awalgarg) or [join us as an expert mentor](http://www.codementor.io/mentor/apply)!
