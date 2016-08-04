# "Real" Mixins with JavaScript Classes

[Original URL](http://justinfagnani.com/2015/12/21/real-mixins-with-javascript-classes/)

> Mixins and Javascript: The Good, the Bad, and the Ugly. Mixins and JavaScript are a like the classic Clint Eastwood movie. The good is that composing objects out of small pieces of implementation is...

## Mixins and Javascript: The Good, the Bad, and the Ugly.

Mixins and JavaScript are a like the classic Clint Eastwood movie.

The good is that composing objects out of small pieces of implementation is even possible due to JavaScript's flexible nature, and that mixins are fairly popular in certain circles.

The bad is a long list: there's no common idea of what the concept of a mixin even is in JavaScript; there's no common pattern for them; they require helper libraries to use; more advanced composition (like coordination between mixins and prototypes) is complicated and does not fall out naturally from the patterns; they're difficult to statically analyze or introspect; finally, most mixin libraries mutate objects or their prototypes, causing problems for VMs and some programmers to avoid them.

The ugly is that the result of all this is a balkanized ecosystem of mixin libraries and mixins, with often incompatible construction and semantics across libraries. As far as I can tell, no particular library is popular enough to even be called common. You're more likely to see a project implement their own mixin function than see a mixin library used.

JavaScript mixins so far have simply fallen well short of their potential, and mixins as described in academic literature and even implemented in a few good languages.

For someone who loves mixins, and thinks they should be used as much as possible, this is terrible. Mixins solve a number of issues that single-inheritance languages have, and to me, most of the complaints that the prototypal crowd has against classes in JavaScript. I would argue all inheritance should be mixin inheritance - subclassing is just a degenerate form of mixin application.

Luckily, there's light at the end of the tunnel with JavaScript classes. Their arrival finally gives JavaScript very easy to use syntax for inheritance, and JavaScript classes are more powerful than most people realize, and are a great foundation for building _real_ mixins.

In this post I'll explore what mixins should do, what's wrong with current JavaScript mixins, and how simple it is to build a very capable mixin system in JavaScript that plays extremely well with classes.

## What, Exactly, are Mixins?

To understand what a mixin implementation should do, let's first look at what mixins are:

> A mixin is an abstract subclass; i.e. a subclass definition that may be applied to different superclasses to create a related family of modified classes.

> Gilad Bracha and William Cook, [Mixin-based Inheritance](http://www.bracha.org/oopsla90.pdf)

This is the best definition of mixins I've been able to find. It clearly shows the difference between a mixin and a normal class, and strongly hints at how mixins can be implemented in JavaScript.

To dig deeper into the implications of this definition, let's add two terms to our mixin lexicon:

- _mixin definition_: The definition of a class that may be applied to different superclasses.
- _mixin application_: The application of a mixin definition to a specific superclass, producing a new subclass.

The mixin definition is really a _subclass factory_, parameterized by the superclass, which produces mixin applications. A mixin application sits in the inheritance hierarchy between the subclass and superclass.

The real, and only, difference between a mixin and normal subclass is that a normal subclass has a fixed superclass, while a mixin definition doesn't yet have a superclass. Only the _mixin applications_ have their own superclasses. You can even look at normal subclass inheritance as a degenerate form of mixin inheritance where the superclass is known at class definition time, and there's only one application of it.

### Examples

Here's an example of [mixins in Dart](https://www.dartlang.org/articles/mixins/), which has a nice syntax for mixins while being similar to JavaScript:

```
class B extends A with M {} 
```

Here _A_ is a base class, _B_ the subclass, and _M_ a mixin. The mixin application is the specific combination of _M_ mixed into _A_, often called _A-with-M_. The superclass of _A-with-M_ is _A_, and the actual superclass of _B_ is not _A_, as you might expect, but _A-with-M_.

Some class declarations and diagrams might be helpful to show what's going on.

Let's start with a simple class hierarchy, with class _B_ inheriting from class _A_:

```
class B extends A {} 
```

![](http://justinfagnani.com/content/images/2015/06/class-hierarchy-1-1.svg)

Now let's add the mixin:

```
class B extends A with M {} 
```

![](http://justinfagnani.com/content/images/2015/06/class-hierarchy-2.svg)

As you can see, the mixin application _A-with-M_ is inserted into the hierarchy between the subclass and superclass.

Note: I'm using long-dashed line to represent the mixin declartion (_B_ includes _M_), and the short-dashed line to represent the mixin application's definition.

#### Multiple Mixins

In Dart, multiple mixins are applied in left-to-right order, resulting in multiple mixin applications being added to the inheritance hierarchy:

```
class B extends A with M1, M2 {}

class C extends A with M1, M2 {} 
```

![](http://justinfagnani.com/content/images/2015/06/class-hierarchy-3.svg)

## Traditional JavaScript Mixins

The ability to freely modify objects in JavaScript means that it's very easy to copy functions around to achieve code reuse without relying on inheritance.

Mixin libraries like [Cocktail](https://github.com/onsi/cocktail), [traits.js](http://soft.vub.ac.be/~tvcutsem/traitsjs/), and patterns described in many blog posts (like one of the latest to hit Hacker News: [Using ES7 Decorators as Mixins](http://raganwald.com/2015/06/26/decorators-in-es7.html)), generally work by modifying objects in place, copying in properties from mixin objects and overwriting existing properties.

This is often implemented via a function similar to this:

```
function mixin(source, target) { 
 for (var prop in source) {
 if (source.hasOwnProperty(prop)) {
 target[prop] = source[prop];
 }
 }
}
```

A version of this has even made it into JavaScript as [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign).

`mixin()` is usually then called on a prototype:

```
mixin(MyMixin, MyClass.prototype); 
```

and now `MyClass` has all the properties defined in `MyMixin`.

### What's So Bad About That?

Simply copying properties into a target object has a few issues. Some of this can be worked around with smart enough mixin functions, but the common examples usually have these problems:

#### Prototypes are modified in place.

When using mixin libraries against prototype objects, the prototypes are directly mutated. This is a problem if the prototype is used anywhere else that the mixed-in properties are not wanted. Mixins that add state can create slower objects in VMs that try to understand the shape of objects at allocation time. It also goes against the idea that a mixin application should create a new class by composing existing ones.

#### `super` doesn't work.

With JavaScript finally supporting `super`, so should mixins: A mixin's methods should be able to delegate to an overridden method up the prototype chain. Since `super` is essentially lexically bound, this won't work with copying functions.

#### Incorrect precedence.

This isn't necessarily always the case, but as often shown in examples, by overwriting properties, mixin methods take precedence over those in the subclass. They should only take precedence over methods in the superclass, allowing the subclass to override methods on the mixin.

#### Composition is compromised

Mixins often need to delegate to other mixins or objects on the prototype chain, but there's no natural way to do this with traditional mixins. Since functions are copied onto objects, naive implementations overwrite existing methods. More sophisticated libraries will remember the existing methods, and call multiple methods of the same name, but the library has to invent its own composition rules: What order the methods are called, what arguments are passed, etc.

References to functions are duplicated across all applications of a mixin, where in many cases they could be bundled in a shared prototype. By overwriting properties, the structure of protytpes and some of the dynamic nature of JavaScript is reduced: you can't easily introspect the mixins or remove or re-order mixins, because the mixin has been expanded directly into the target object.

If we actually use the prototype chain, all of this goes away with very little work.

## Better Mixins Through Class Expressions

Now let's get to the good stuff: _Awesome Mixins™ 2015 Edition_.

Let's quickly list the features we'd like to enable, so we can judge our implementation against them:

1. Mixins are added to the prototype chain.
2. Mixins are applied without modifying existing objects.
3. Mixins do no magic, and don't define new semantics ontop of the core language.
4. `super.foo` property access works within mixins and subclasses.
5. `super()` calls work in constructors.
6. Mixins are able to extend other mixins.
7. `instanceof` works.
8. Mixin definitions do not require library support - they can be written in a universal style.

Above I referred to mixins as "subclass factories, parameterized by the superclass", and in this formulation they are literally just that.

We rely on two features of JavaScript classes:

1. `class` can be used as an expression as well as a statement. As an expression it returns a new class each time it's evaluated. (sort of like a factory!)
2. The `extends` clause accepts arbitrary expressions that return classes or constructors.

The key is that classes in JavaScript are first-class: they are values that can be passed to and returned from functions.

All we need to dfine a mixin is a function that accepts a superclass and creates a new subclass from it, like this:

```
let MyMixin = (superclass) => class extends superclass { 
 foo() {
 console.log('foo from MyMixin');
 }
};
```

Then we can use it in an `extends` clause like this:

```
class MyClass extends MyMixin(MyBaseClass) { 
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ... */
}
```

And `MyClass` now has a `foo` method via mixin inheritance:

```
let c = new MyClass(); 
c.foo(); // prints "foo from MyMixin" 
```

Incredibly simple, and incredibly powerful! By just combining function application and class expressions we get a complete mixin solution, that generalizes well too.

Applying multiple mixins works as expected:

```
class MyClass extends Mixin1(Mixin2(MyBaseClass)) { 
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ... */
}
```

Mixins can easily inherit from other mixins by passing the superclass along:

```
let Mixin2 = (superclass) => class extends Mixin1(superclass) { 
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Add or override methods here */
}
```

And you can use normal function composition to compose mixins:

```
let CompoundMixin = (superclass) => Mixin2(Mixin3(superclass)); 
```

This approach gives us a very good implementation of mixins

As I mentioned before, many examples of JavaScript mixins get this wrong, and mixins override the subclass.

### `super` works.

One of the biggest benefits is that `super` works inside methods of the subclass and the mixins. Since we don't ever overwrite methods on classes or mixins, they're available for `super` to address.

`super` calls can be a little unintuitive for those new to mixins because the superclass isn't known at mixin definition, and sometimes developers expect `super` to point to the declared superclass (the parameter to the mixin), not the mixin application. Thinking about the final prototype chain help here.

### Composition is preserved.

This is really just a consequence of the other benefits, but two mixins can define the same method, and as long as they call `super`, both of them will be invoked then applied.

Sometimes a mixin will not know if a superclass even has a particular property or method, so it's best to guard the super call:

```
let Mixin1 = (superclass) => class extends superclass { 
 foo() {
 console.log('foo from Mixin1');
 if (super.foo) super.foo();
 }
};

let Mixin2 = (superclass) => class extends superclass { 
 foo() {
 console.log('foo from Mixin2');
 if (super.foo) super.foo();
 }
};

class S { 
 foo() {
 console.log('foo from S');
 }
}

class C extends Mixin1(Mixin2(S)) { 
 foo() {
 console.log('foo from C');
 super.foo();
 }
}

new C().foo(); 
```

prints:

```
foo from C 
foo from Mixin1 
foo from Mixin2 
foo from S 
```

### Improving the Syntax

I find applying mixins as functions to be both elegantly simple - describing exactly what's going on, but a little bit ugly at the same time. My biggest concern is that this construction isn't optimized for readers unfamiliar with this pattern.

I'd like a syntax that was easier on the eyes and at least that gave new readers something to search for to explain what's going on, like the Dart syntax. I'd also like to add some more features like memoizing the mixin applications and automatically implementing `instanceof` support.

For that we can write a simple helper which applies a list of mixins to a superclass, in a fluent-like API:

```
class MyClass extends mix(MyBaseClass).with(Mixin1, Mixin2) { 
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ... */
}
```

Here's the code:

```
let mix = (superclass) => new MixinBuilder(superclass);

class MixinBuilder { 
 constructor(superclass) {
 this.superclass = superclass;
 }

 with(...mixins) { 
 return mixins.reduce((c, mixin) => mixin(c), this.superclass);
 }
}
```

That's it! It's still extremely simple for the features and nice syntax it enables.

### Constructors and Initialization

Constructors are a potential source of confusion with mixins. They essentially behave like methods, except that overriden methods tend to have the same signature, while constructors in a inheritance hierarchy often have different signatures.

Since a mixin likely does not know what superclass it's being applied to, and therefore its super-constructor signature, calling `super()` can be tricky. The best way to deal with this is to always pass along all constructor arguments to `super()`, either by not defining a constructor at all, or by using the spread operator: `super(...arguments)`.

This means that passing arguments specifically to a mixin's constructor is difficult. One easy workaround is to just have an explicit initialization method on the mixin if it requires arguments.

## Further Exploration

This is just the beginning of many topics related to mixins in JavaScript. I'll post more about things like:

- How mixin inheritance can address the fear that ES6 classes and classical inheritance are bad for JavaScript.
- Caching mixin applications so that the same mixin applied to the same superclass reuses a prototype.
- Getting `instanceof` to work.
- Using function-style mixins in ES5.
- De-duplicating mixins so mixin composition is more usable.

## The mixwith.js project

I started a project on GitHub to build a library around this pattern: <https://github.com/justinfagnani/mixwith.js><br>
It can be found on npm: <https://www.npmjs.com/package/mixwith>
