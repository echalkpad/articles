# The Ridiculously Easy Way to Animate Your Applications With VueJS

[Original URL](http://taha-sh.com/blog/the-ridiculously-easy-way-to-animate-your-applications-with-vuejs)

> Vue makes it incredibly easy to add transitions to your application. The transitions I'm talking about here are those that run when elements are inserted into or removed from the DOM. An example of...

Vue makes it incredibly easy to add transitions to your application. The transitions I'm talking about here are those that run when elements are inserted into or removed from the DOM.

An example of this would be showing/hiding a modal box that contains something to display to the user. We've seen a whole example on how to build one of those in [the previous tutorial](http://taha-sh.com/blog/how-to-create-a-reusable-modal-box-in-laravel-vuejs).

In this tutorial, I'm going to show you how you can use transitions in your application in a few seconds!

## What options do we have?

Vue gives us two options to add transitions to elements. One with CSS and the other with JavaScript.

Both are fine. But since the CSS approach is the most used one, and because with it, we can use some external animation libraries (such as [Animate.css](https://daneden.github.io/animate.css/)), it's going to be our choice in this tutorial.

## Which elements are going to animate?

The way you tell an element to animate is by using the attribute _"transition"_ on it. But in order for that to work, that element must have one of the following attributes: _v-if_, _v-show_, or _v-for_.

It should be obvious why we need them. Those are the basic ways to show and hide elements, duh!

So an example of such an element would be:

```
<div v-if="ok" transition="foo">Hello, World!</div>
```

_"foo"_ is the name of the transition you want to run (you name it as you want).

## The manual way

After you specify the name of the transition (in this case _foo_), you'll get three CSS rules to define.

- **.foo-transition**: This is the normal state for the element.
- **.foo-enter**: This is the starting state (before it's shown).
- **.foo-leave**: This is ending state (when the element is leaving).

Here's an example (from the [official documentation of VueJS](http://vuejs.org/guide/transitions.html#Example)) of how you'd define them.

```
.foo-transition {
 transition: all .3s ease;
 height: 30px;
 padding: 10px;
 background-color: #eee;
 overflow: hidden;
}

.foo-enter, .foo-leave {
 height: 0;
 padding: 0 10px;
 opacity: 0;
}
```

So here's what happens. When the element is about to be shown, the _foo-enter_ class will be applied on the element. After that, when it's fully shown, the class will change to _foo-transition_. Finally, when the element is leaving, the class will be _foo-leaving_.

Now that's great! But this is the manual way of doing it. What instead you can do, is use already defined classes from some CSS animation library (like [Animate.css](https://daneden.github.io/animate.css/)).

## Animating with animate.css

[Animate.css](https://daneden.github.io/animate.css/) provides a huge list of nice CSS transitioning effects that you can choose from. But the problem, though, their classes' names don't follow the convention of the Vue transition system (_.foo-transition_, _.foo-enter_, _.foo-leave_).

However, in version _1.0.14_, Vue gives us a way to change those class names to anything we want.

Here's how we can do it.

```
Vue.transition('foo', {
 enterClass: 'bounceInLeft',
 leaveClass: 'bounceOutRight'
})
```

So we provide it with the class names that we want to use for both states (entering & leaving). These class names can be anything we want, but in most cases, we'll get them from some CSS animation library, like Animate.css in this case.

All of this means, _bounceInLeft_ & _bounceOutRight_ will be our new transition classes instead of _foo-enter_ & _foo-leave_, respectively.

## Demo

To finish up this tutorial, I'd like to leave you with a quick demo that uses what we've learned in this tutorial.
