# Understanding Loose Typing in JavaScript

[Original URL](http://blog.jeremymartin.name/2008/03/understanding-loose-typing-in.html)

> For many front end developers, JavaScript was their first taste of a scripting and/or interpretive language. To these developers, the concept and implications of loosely typed variables may be second...

For many front end developers, JavaScript was their first taste of a scripting and/or interpretive language. To these developers, the concept and implications of loosely typed variables may be second nature. However, the explosive growth in the demand for Web 2.0-ish applications has resulted in a growing number of back end developers that have had to dip their feet into pool of client side technologies. Many of these developers are coming from a background in strongly typed languages, such as C# and Java, and are unfamiliar with both the freedom and the potential pitfalls involved in working with loosely typed variables.

Since the concept of loose typing is so fundamental to scripting in JavaScript, an understanding of it is essential. This article is a top level discussion of loose typing in JavaScript. Since there may be subtle differences in loose typing from language to language, let me constrain this discussion to the context of JavaScript. OK, let's dig in...

## What is Loose Typing?

Well, this seems like a good place to start. It is important to understand both what loose typing _is_, and what loose typing _is not_. Loose typing means that variables are declared without a type. This is in contrast to strongly typed languages that require typed declarations. Consider the following examples:

```
/* JavaScript Example (loose typing) */
var a = 13; // Number declaration
var b = "thirteen"; // String declaration

/* Java Example (strong typing) */
int a = 13; // int declaration
String b = "thirteen"; // String declaration
```

Notice that in the JavaScript example, both a and b are declared as type var. Please note, however, that this does _not_ mean that they do not have a type, or even that they are of type "var". Variables in JavaScript _are_ typed, but that type is determined internally. In the above example, var a will be type 

<span class="inline_code">Number</span>

 and var b will be type 

<span class="inline_code">String</span>

. These are two out of the three primitives in JavaScript, the third being 

<span class="inline_code">Boolean</span>

.

JavaScript also has other types beyond primitives. The type diagram for JavaScript is as follows (as per [Mozilla](http://developer.mozilla.org/en/docs/A_re-introduction_to_JavaScript)):

![](http://image.bayimg.com/oajpbaabc.jpg "JavaScript Type Diagram")

Ya really - Null and Undefined too.

Note, however, that this distinction between primitives and objects will be dismissed in JavaScript 2.0\. You can read more about that [here](http://www.mozilla.org/js/language/js20-2000-07/libraries/types.html).

## Type Coercion

Type coercion is a topic that is closely associated with loose typing. Since data types are managed internally, types are often converted internally as well. Understanding the rules of type coercion is extremely important. Consider the following expressions, and make sure you understand them:

```
7 + 7 + 7; // = 21
7 + 7 + "7"; // = 147
"7" + 7 + 7; // = 777
```

In the examples above, arithmetic is carried out as normal (left to right) until a String is encountered. From that point forward, all entities are converted to a String and then concatenated.

Type coercion also occurs when doing comparisons. You can, however, forbid type coercion by using the === operator. Consider these examples:

```
1 == true; // = true
1 === true; // = false

7 == "7"; // = true
7 === "7"; // = false;
```

There are methods to explicitly convert a variable's type as well, such as 

<span class="inline_code">parseInt</span>

 and 

<span class="inline_code">parseFloat</span>

 (both of which convert a String to a Number).

Double negation (!!) can also be used to cast a Number or String to a Boolean. Consider the following example:

```
true == !"0"; // = false
true == !!"0"; // = true
```

## Conclusion

This obviously is not a definitive reference to loose typing in JavaScript (or type coercion for that matter). I do hope, however, that this will be a useful resource to those who are not familiar with these topics, and a good refresher for those who already are. I have tried to insure that the above is accurate, but if you notice anything incorrect, please let me know! And as always, thanks for reading!
