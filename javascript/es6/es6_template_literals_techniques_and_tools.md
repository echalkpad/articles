
[Original URL](https://www.sitepoint.com/es6-template-literals-techniques-and-tools/)

> This article is part of a web development series from Microsoft. Thank you for supporting the partners who make SitePoint possible. JavaScript template literals, new to ES6, provide a powerful new...

_This article is part of a web development series from Microsoft. Thank you for supporting the partners who make SitePoint possible._

[JavaScript template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), new to ES6, provide a powerful new tool for doing everything from extending strings across lines to building powerful HTML and XML generators. This article will cover the big changes that template literals bring to JavaScript, including the backtick character, expressions in templates, template functions, and building more complex XML/HTML templates.

## The Backtick Character

You've no doubt run into this situation: you have a line of text, maybe HTML, that extends over several lines. How to put those multiple lines in HTML? It is possible to use the + operator to join strings across multiples lines (provided you enclose them in quotes) or even just append the line with the backslash character (`"\"`). Those work. They are also awkward, error prone, and ugly.

ES6 introduces another way to extend strings across a single line, using the back-tick character (`` ` `` ). At its simplest, this solves two issues with one throw – the aforementioned one of letting you have one string with embedded carriage returns, and the not to be sneezed at ability to incorporate both single and double quotes in a string without having to escape them.

Consider this code and observe what it generates below:

```
`"This isn't just another quote," Ada said. "It's a magical quote.";

"I'm not quite sure what you mean." 

Clive settled onto the couch, one foot over the armchair rest, eyeing her dubiously.

"Consider this - if I have to write a quote in code normally, say something like 'This is a weird quote.' \
then I have to spend a lot of time using escape characters like \\" and \\', and they can make for real \
legibility problems. With a magic quote, though, they just go away!"

"Really?"

"You do have to double escape your escape characters, however. \\n will still get interpreted as an hard \
carriage return, but \\\\n will give you the slash followed by the letter 'n'."
`
console.log(quote)
```

This generates the output:

_"This isn't just another quote," Ada said. "It's a magical quote."<br>
"I'm not quite sure what you mean."<br>
Clive settled onto the couch, one foot over the armchair rest, eyeing her dubiously.<br>
"Consider this – if I have to write a quote in code normally, say something like 'This is a weird quote.' then I have to spend a lot of time using escape characters like `\"` and `\'`, and they can make for real legibility problems. With a magic quote, though, they just go away!"<br>
"Really?"_

"You do have to double escape your escape characters, however. `\n` will still get interpreted as a hard carriage return, but `\\n` will give you the slash followed by the letter `'n'`."

The effect is instantaneous here – in effect, the backtick (or more accurately, the _template literal_ operator) performs a conversion of carriage returns, tabs and quotes into their respective normal string representations. This not only handles the pain of dealing with apostrophes in conversational text (as demonstrated above), but it also makes the corresponding code considerably easier to read.

Note as well that the terminating backspace character is still recognized within templates, which can be very useful when you have text that runs longer than your available screen-width. The difference between a regular string and a template string is that in the former you need to escape every line, while in the latter you need only escape the overly long strings.

If your string contains a number of escaped characters (such as the newline or tab character) and you don't want to get caught up in counting slashes, you can also use the [String.raw` literal form](http://exploringjs.com/es6/ch_template-literals.html).

```
String.raw`The sequence \t puts a tab into a template literal`
```

"The sequence \t puts a tab into a template literal."

## Evaluating Expressions in Template Strings

Template literals also solve another problem: they can evaluate variables within the string. The `${expr}` construct, when used within a template literal, will evaluate the expression and incorporate this into a string itself.

```
var name = "Ada";
var vocation = "steampunk programmer";
varpcAssert = `${name} is a ${vocation}.`;
console.log(pcAssert)
// "Ada is a steampunk programmer."
```

## Using a template literal as a...template

In the example below, the variable name and vocation are both predefined, and the `${}` expressions then replace the variables with their associated values. In a similar vein, you can both evaluate content and use object references within these expressions:

![Example code - 1](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467638376ES6-template-literals-techniques-and-tools01-example1-code.png)

## Template Functions

At this stage, template literals are beginning to look interesting. You can create a complex template literal that can reduce the amount of time embedded such content into a string. However, template literals begin to come into their own when combined with functions which produce the same output.

![Example code - 2](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467638384ES6-template-literals-techniques-and-tools02-example2-code.png)

Note that this can also be simplified somewhat by using [ES6 arrow notation](http://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/):

```
var pounds = (amount) => (
    amount.toLocaleString("en-UK", {
        style:'currency',
        currency:'GBP'
    })
);

varpronounUCase = (gender) => (gender == 'female') ? 'She' : 'He';

var describe = (obj) => (
    `${obj.name} is a ${obj.vocation}.\
    ${pronounUCase(obj.gender)} has ${pounds(obj.wealth)} \ in the bank.`
);

var person = {
    name: "Ada",
    vocation: "steampunk programmer",
    gender: "female",
    wealth: 24025
};

console.log(describe(person))

// "Ada is a steampunk programmer. She has £24,025.00 in the bank."
```

If the goal was to make a method on `person`, you'd be better off going with the more traditional functional notation, as the _this_ keyword is not defined within an arrow function:

```
var person = {
    name: "Ada",
    vocation: "steampunk programmer",
    gender: "female",
    wealth: 24025,
    pounds: (amount) => (amount.toLocaleString("en-UK", {
        style: 'currency',
        currency:'GBP'
        })
    ),
    pronoun: (gender) => (gender === "female") ? "She" : "He",
    toString: function(){
        varself = this;
        return (`${self.name} is a ${self.vocation}.\
            ${self.pronoun(self.gender)} has ${self.pounds(self.wealth)} \
            in the bank.`
        )
    }
};

console.log(""+ person)

// "Ada is a steampunk programmer.She has £24,025.00 in the bank."
```

## Creating a toString() Method with a Template

The use of the [toString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString) method should be noted, as `toString()` is automatically called whenever an object is converted into a string without having to be explicitly invoked. This means that while `person` itself will always give the object, `"" + person` will return the templated string as output, making `toString()` useful for defining on classes and prototyped objects.

## Building HTML and XML Templates

Such template functions have obvious implications for building HTML and XML structures. For instance, you can construct a list of links in HTML by passing an array into a template function to generate an HTML string, then inserting this into another element:

```
var li = (obj) => `
```

`; varul = (arr) =>

; vararr = [ {url: "[http://www.twitter.com"](http://www.twitter.com”), label: "Twitter"}, {url: "[http://www.linkedin.com"](http://www.linkedin.com”), label: "Linked In"}, {url: "[http://www.facebook.com"](http://www.facebook.com”), label: "Facebook"} ] document.getElementById('displayList').innerHTML = ul(arr);

### Creating lists of links using templates.

This will generate the following output:

```
"<ul>
<li><a href="http://www.twitter.com" target="_new">Twitter</a></li>
<li><a href="http://www.linkedin.com" target="_new">LinkedLinked Linked In</a></li>
<li><a href="http://www.facebook.com" target="_new">Facebook</a></li>
</ul>"
```

which will then be rendered as an unordered list of links.

![List of links](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467638390ES6-template-literals-techniques-and-tools03-list-of-links.png)

Tables can be constructed in a similar fashion:

![Example code - 3](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/07/1467638396ES6-template-literals-techniques-and-tools04-example3-code.png)

This will generate a table like this:

Aircraft Name | Aircraft Type  | Aircraft Count
------------- | -------------- | --------------
F–35          | Attack Bomber  | 125
F–18          | Attack Fighter | 42
F–15          | Fighter        | 432

More complex structures are possible, of course, but the idea to be illustrated here is that template literals can go a long way towards building complex (not to mention potentially dynamic) scripts and components.

Template literals are a powerful addition to the JavaScript toolset, and indeed, bring to the language a capability that is perhaps closest to [XSLT](https://en.wikipedia.org/wiki/XSLT) in the XML world – a means to build sets of templates that can, in concert, build all kinds of rich output structures via transformation.

This article is part of the web development series from Microsoft tech evangelists and [DevelopIntelligence](http://www.developintelligence.com/) on practical JavaScript learning, open source projects, and interoperability best practices including [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?wt.mc_id=DX_838520) browser and the new [EdgeHTML rendering engine](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?wt.mc_id=DX_838520). DevelopIntelligence offers [JavaScript Training](http://appendto.com/courses/javascript/) and [React Training](http://appendto.com/courses/react-training/) Courses through [appendTo](http://appendto.com/), their front-end focused blog and course site.

We encourage you to test across browsers and devices including Microsoft Edge – the default browser for Windows 10 – with free tools on [dev.microsoftedge.com](https://dev.windows.com/en-us/?wt.mc_id=DX_838520), including the [EdgeHTML issue tracker](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/?wt.mc_id=DX_838520), where you can report or search EdgeHTML issues, such as problems with site rendering or standards compliance.Also, [visit the Edge blog](https://blogs.windows.com/msedgedev/?wt.mc_id=DX_838520) to stay updated and informed from Microsoft developers and experts.
