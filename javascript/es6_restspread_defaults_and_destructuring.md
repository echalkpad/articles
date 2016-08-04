# ES6 Rest/Spread, Defaults and Destructuring

[Original URL](http://www.datchley.name/es6-rest-spread-defaults-and-destructuring/)

> As I've been playing around with React and more specifically Redux these last couple of weeks, the usefulness and joy of ES6's new rest/spread '...' operator and default function arguments has become...

As I've been playing around with React and more specifically [Redux](http://redux.js.org/) these last couple of weeks, the usefulness and joy of ES6's new rest/spread `'...'` operator and default function arguments has become apparent. So many of the common idioms we've used in ES5 and prior can be done now in a more elegant and explicit way. So much so, that I think they warrant a post all to themselves.

## Default Function Arguments

Functions in javascript are extremely versatile to begin with, as they are first-class objects in the language. But, unlike some other languages, having default values for function arguments in ES5 was a bit cumbersome.

Here's a simple logarithm function to compute the logarithm of a given number for a particular base. For example, the base 10 logarithm of 1000 is 3, ie., 1000 = 10 x 10 x 10 = 10

<sup>3</sup>

.

```
// get base logarithm of n 
function log(n, base) { 
 base = base || 10;
 return Math.log(n) / Math.log(base);
}
log(1000, 10); 
//=> 2.9999999999999996 
```

**Note**: this gets us nearly 3, as [IEEE 754](http://en.wikipedia.org/wiki/IEEE_754) floating point rounding used in Javascript only gets us close.

Here, we default the second argument, `base`, to 10 using Javascript's `||` logical OR operator, which basically says: evaluate to the first (_left hand_) expression if that expression is truthy, otherwise evaluate to the second (_right hand_) expression. So if our function is called with no explicit base, then the `base` argument is `undefined` which evaluates as falsy cause the number 10 to be assigned to `base`.

While this gives us the ability to set default values for function arguments, it isn't very explicit. If we're reading through someone's code, we need to be looking specifically for code that does this to see that the function allows fewer arguments than it has declared.

With ES6's default arguments, however, we can make this more explicit in the code and easier to implement. Here's our new `log()` function using default arguments and ES6 [arrow function expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) syntax.

```
const log = (n, base=10) => Math.log(n) / Math.log(base); 
console.log(log(1000,10)); 
//=> 2.9999999999999996 
```

We simply assign a default value to a given parameter right in the declaration. Javascript evaluates those default arguments at run time, so a new value is always produced. You can even use a function call as a way to get the default value.

For instance, here's a function that creates an image DOM Element, allowing you to pass it styles that should be applied but using some defaults defined by another function if none are passed.

```
// Returns a default style config object for images
const getDefaultImgStyle = () => { 
 return {
 width: '100px',
 height: '100px',
 border: '1px solid #ccc'
 };
}

const img = (src, styles=getDefaultImgStyle()) => { 
 let img = document.createElement('img');
 img.src = src;
 Object.keys(styles).forEach((prop) => img.style[prop]=styles[prop]);
 return img;
}

img('http://babeljs.io/images/logo.svg'); 
//=> <img> DOMElement with applied styles inline
```

Earlier parameter values are also available to later parameter default values as well.

```
const numbers = (a, b = a+1, c = b+1) => { 
 console.log(a,b,c);
}
numbers(3); 
//=> 3 4 5
```

These are just some of the highlights; and there's [much more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters) to argument defaults; but let's move on to look at the spread/rest operator next.

## Spread/Rest Operator `...`

This operator, `'...'`, has two uses, depending on the context. In the first case we'll cover the usage as a _spread_ operator.

A common idiom in Javascript is using `Function#apply` to pass an array of values as arguments to some other function. For instance, we might have a simple `max` function that can give us the highest number out of any of the numbers passed to it as arguments. (_this is how the `Math#max` method works to begin with, but bear with me_)

```
function max() { 
 // arguments contains all params, named + non-named
 // we use Function#apply to pass those as individual arguments
 return Math.max.apply(null, arguments);
}
max(5,8); //=> 8 
max(5,8,1,18); //=> 18 
```

Here, we use `Function#apply` to pass our function's arguments, available to us in the special `argument` variable, as individual arguments to the `Math#max` method.

In ES6, we can do it in a more explicit and useful way, without having to resort to using `Function#apply` and worry about the _context_ argument which we previously passed as `null`.

```
function max() { return Math.max(...arguments); } 
max(5,18,1,8); 
//=> 18
```

The spread operator takes each value of the array and passes them as individual parameters to the `Math#max` method.

But, we can use the operator again as the _rest_ operator to collect all the arguments passed to our function without having to use the special `arguments` variable at all.

```
const max = (...nums) => Math.max(...nums); 
max(5,18,1,8); 
//=> 18
```

Using the spread/rest operator in these situations makes the code much more declarative about what arguments the function expects and how it will use them.

It also means we don't have to use the special `arguments` variable, which isn't a real Array. Having to use `arguments` precludes us from using standard Array methods like `Array#slice`. With the rest/spread operator, we get a real Array, with all the usual methods at our disposal.

Another example:

```
const join = (sep, ...args) => args.join(sep); 
join(':', "one", "two", "three"); 
//=> one:two:three
```

Had we intended to write this in ES5, we would have had to do the following:

```
function join(sep /*, ...args*/) { 
 var args = [].slice.call(arguments);
 return args.join(sep);
}
```

Our ES6 function expression is much more explicit about its surface API now; as opposed to our ES5 code, in which we, as good developers, at least documented the use of further arguments with a comment in the parameter declaration.

One caveat with using the rest parameter in ES6 is that, just like the Highlander, there can be only one. And, it has to be the last parameter to the function.

The following will throw a syntax error or give an `Unexpected token` error if using a transpiler like Babel.

```
const fn = (one, ...more, last) => { /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ... _articles/ articles/ bin/ CAs/ devops/ Documents/ dotfiles/ gethtml/ go/ hnews/ jason/ js/ netdata/ thesrc/ ucii/ }; 
```

Finally, consider something we do constantly in Javascript: merging two or more objects. You've likely done this with the well known `extend` method in libraries like jQuery and Underscore/LoDash.

Here's a simple `extend` function in ES5.

```
function extend(target /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ,source ,... */) { 
 var args = [].slice.call(arguments, 1); 

 return args.reduce(function(merged, obj) {
 return Object.keys(obj).reduce(function(receiver, prop) {
 if (obj.hasOwnProperty(prop)) {
 receiver[prop] = obj[prop];
 }
 return receiver;
 }, merged);
 }, target);
}

var base = { a: 1, b: 2 }, 
 src1 = { b: 3 },
 src2 = { c: 4 },
 merged;

merged = extend(base, src1, src2); 
//=> { a: 1, b: 3, c: 4 }
```

We can simplify this using `Object#assign` directly.

```
let merged = Object.assign(base, src1, src2); 
//=> { a: 1, b: 3, c: 4 }
```

But we can also perform the same operation with the spread operator as well.

```
merged = { 
 ...base,
 ...src1,
 ...src2
 };
//=> { a: 1, b: 3, c: 4 }
```

In this case, the spread operator works to destructure each property on the object directly onto the object literal it is being applied on; and it does so in order (_left to right_) so that future destructured object property values will overwrite previously set values.

For a great write up on rest and spread in ES6, check out Nicolás Bevacqua's post on the subject, [ES6 Spread & Butter in Depth](https://ponyfoo.com/articles/es6-spread-and-butter-in-depth).

## Destructuring

Destructuring has been available in a number of languages and is a convenient way to pull values out of arrays or objects.

In the left-hand side of an assignment statement, destructuring lets us use the pattern of the array or object to pull out values.

For example:

```
const list = [1,2,3]; 
const obj = { one: 1, two: 2, three: 3 };

let [n] = list; 
//=> n=1

let [a,b,c] = list; 
//=> a=1,b=2,c=3

let { one:x, two:y, three:z } = obj; 
//=> x=1, y=2, z=3

// function arguments are assignment statements
const fn = ([x,y,z]) => x+y+z; 
fn(list); //=> 6 
```

We can even access only the parts of the array or object that we want and skip others. If the array/object being destructured doesn't match the pattern used on the left-hand side, you end up with `undefined`.

```
const list = [1,2,3]; 
const obj = { one: 1, two: 2, three: 3 };

let { one:x, ,three:z } = obj; 
//=> x=1, z=3

let [x,,z] = list; 
//=> x=1, z=3

let [a,b,c,d] = list; 
//=> a=1, b=2, c=3, d=undefined
```

We can even handle nested properties of arrays and objects as well. And, given that the destructuring pattern on the left-hand side coerces the values on the right-hand side to objects before extracting the value, we can access any available properties or methods on that object as well.

```
const list = [1, [2, 3], 4]; 
const obj = { 
 one: 1,
 two: 2,
 items: [3, 4]
};

let [a, [b, c], d] = list; 
//=> a=1, b=2, c=3, d=4

let { one:f, items:[g,h] } = obj; 
//=> f=1, g=3, h=4

// strings are iterable as Arrays of characters
let { length: l } = "abc"; 
// => l=3
```

We can even use the _rest_ operator in the left-hand destructuring pattern as well.

```
const numbers = [1,2,3,4,5,6,7];

let [first, ...rest] = numbers; 
//=> first=1, rest=[2,3,4,5,6,7]
```

And, we can use default values, much like our default argument values for functions, when destructuring as well.

```
const list = [1,2]; 
const obj = { one: 1, two: 2 };

let [x,y,z=3] = list; 
//=> x=1, y=2, z=3

let { one:a, two:b, three:c=3 } = obj; 
//=> a=1, b=2, c=3
```

Keep in mind that we can also use ES6's property value shorthand syntax when extracting during destructuring as well.

```
const obj = { x: 1, y: 2, z: 3 }; 
let { x, y, z } = obj; 
//=> x=1, y=2, z=3
```

The shorthand notation is the equivalent of the following:

```
const obj = { x: 1, y: 2, z: 3 }; 
let { x:x, y:y, z:z } = obj; 
```

There are so many more nuances to destructuring in ES6, such as being able to destructure and use spread to get the values of an infinite series, like a generator function; or the use of computed property values.

For a more in depth look at the topic, check out Axel Rauschmayer's [post](http://www.2ality.com/2015/01/es6-destructuring.html) on destructuring and his [follow up post](http://www.2ality.com/2015/03/destructuring-algorithm.html) on the algorithm ES6 uses to implement the feature.

Hopefully, you've seen how using destructuring and the rest/spread operator can help you simplify your code as well as be much more explicit about what that code is doing.
