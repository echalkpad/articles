# Understanding JavaScript OOP - Quils in Space

[Original URL](http://robotlolita.me/2011/10/09/understanding-javascript-oop.html)

> Now that we're able to inherit properties from other objects and extend the specialised objects to define their own behaviours, we have a little problem: what if we actually wanted to access the...

Now that we're able to inherit properties from other objects and extend the specialised objects to define their own behaviours, we have a little problem: what if we actually wanted to access the parent behaviours that we just overwrote?

JavaScript provides the `Object.getPrototypeOf` function, that returns the `[⁣[Prototype]⁣]` of an object. This way, we have access to all properties defined within the prototype chain of an object. So, accessing a property in the parent of an object is quite simple:

 | <div class="highlight">
  <pre>

  <code>Object.getPrototypeOf(mikhail).name // same as <code>person.name&amp;#39;
// =&amp;gt; &amp;#39;undefined undefined&amp;#39;

// We can assert it&amp;#39;s really being called on</code>person' by
// giving <code>person&amp;#39; a</code>first_name' and `last_name'
person.first_name = 'Random'
person.last_name = 'Person'
Object.getPrototypeOf(mikhail).name
// =&gt; 'Random Person'</code>
</pre>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

So, a naïve solution for applying a method stored in the `[⁣[Prototype]⁣]` of an object to the current one, would then follow, quite naturally, by looking the property on the `[⁣[Prototype]⁣]` of `this`:

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15</code>
</pre>
</div> | <div class="highlight">
  <pre>

  <code>var proto = Object.getPrototypeOf

// (name:String) → String
// Greets someone intimately if we know them, otherwise use
// the generic greeting
mikhail.greet = function(name) {
 return name == 'Kristin Weiß'? this.name + ': Heya, Kristty'
 : /<em> we dunno this guy </em>/ proto(this).greet.call(this, name)
}

mikhail.greet(kristin.name)
// =&gt; 'Michael White: Heya, Kristty'

mikhail.greet('Margareth')
// =&gt; 'Michael White: Hello, Margareth'</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

This looks all good and well, but there's a little catch: it will enter in endless recursion if you try to apply this approach to more than one parent. This happens because the methods are always applied in the context of the message's first target, making the `[⁣[Prototype]⁣]` lookup resolve always to the same object:

![](http://dl.dropbox.com/u/4429200/blog/oop-super.png)

The simple solution to this, then, is to make all parent look-ups static, by passing the object where the current function is stored, rather than the object that the function was applied to.

So, the last example becomes:

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19</code>
</pre>
</div> | <div class="highlight">
  <pre>

  <code>var proto = Object.getPrototypeOf

// (name:String) → String
// Greets someone intimately if we know them, otherwise use
// the generic greeting.
//
// Note that now we explicitly state that the lookup should take
// the parent of `mikhail', so we can be assured the cyclic parent
// resolution above won't happen.
mikhail.greet = function(name) {
 return name == 'Kristin Weiß'? this.name + ': Heya, Kristty'
 : /<em> we dunno this guy </em>/ proto(mikhail).greet.call(this, name)
}

mikhail.greet(kristin.name)
// =&gt; 'Michael White: Heya, Kristty'

mikhail.greet('Margareth')
// =&gt; 'Michael White: Hello, Margareth'</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Still, this has quite some short-commings. First, since the object is hard-coded in the function, we can't just assign the function to any object and have it just work, as we did up 'till now. The function would always resolve to the parent of `mikhail`, not of the object where it's stored.

Likewise, we can't just apply the function to any object. The function is not generic anymore. Unfortunately, though, making the parent resolution dynamic would require us to pass an additional parameter to every function call, which is something that can't be achieved short of ugly hacks.

The approach proposed for the next version of JavaScript only solves the first problem, which is the easiest. Here, we'll do the same, by introducing a new way of defining methods. Yes, methods, not generic functions.

Functions that need to access the properties in the `[⁣[Prototype]⁣]` will require an additional information: the object where they are stored. This makes the lookup static, but solves our cyclic lookup problem.

We do this by introducing a new function -- `make_method` -- which creates a function that passes this information to the target function.

<div class="linenodiv">
  <pre>

  <code> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57</code>
</pre>
</div> | <div class="highlight">
  <pre>

  <code>// (object:Object, fun:Function) → Function
// Creates a method
function make_method(object, fun) {
 return function() { var args
 args = slice.call(arguments)
 args.unshift(object) // insert `object' as first parameter
 fn.apply(this, args) }
}


// Now, all functions that are expected to be used as a method
// should remember to reserve the first parameter to the object
// where they're stored.
//
// Note that, however, this is a magical parameter introduced
// by the method function, so any function calling the method
// should pass only the usual arguments.
function message(self, message) { var parent
 parent = Object.getPrototypeOf(self)
 if (parent &amp;&amp; parent.log)
 parent.log.call(this, message)

 console.log('-- At ' + self.name)
 console.log(this.name + ': ' + message)
}

// Here we define a prototype chain C -&gt; B -&gt; A
var A = Object.create(null)
A.name = 'A'
A.log = make_method(A, message)

var B = Object.create(A)
B.name = 'B'
B.log = make_method(B, message)

var C = Object.create(B)
C.name = 'C'
C.log = make_method(C, message)

// And we can test if it works by calling the methods:
A.log('foo')
// =&gt; '-- At A'
// =&gt; 'A: foo'

B.log('foo')
// =&gt; '-- At A'
// =&gt; 'B: foo'
// =&gt; '-- At B'
// =&gt; 'B: foo'

C.log('foo')
// =&gt; '-- At A'
// =&gt; 'C: foo'
// =&gt; '-- At B'
// =&gt; 'C: foo'
// =&gt; '-- At C'
// =&gt; 'C: foo'</code>
</pre>
</div>
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
