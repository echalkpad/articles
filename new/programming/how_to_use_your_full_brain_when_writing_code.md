# How to use your full brain when writing code

[Original URL](http://chrismm.com/blog/how-to-use-your-full-brain-when-writing-code/)

> On "Your Brain At Work", Dr. David Rock gives a quick introduction to the current state of neuroscience and goes on to give a great deal of advice on how to handle the perils of...

On "[Your Brain At Work](http://amzn.to/1UjAAf0)", Dr. David Rock gives a quick introduction to the current state of neuroscience and goes on to give a great deal of advice on how to handle the perils of office life, which most of us developers can probably relate to. In this post, will attempt to take the main ideas from this book and apply them to common software development situations.

## Your CPU at work

It turns out that the brain does work somewhat like a CPU. Think of it as a processor that can only handle a limited number of concurrent tasks, but has a really large amount of storage. Let's see how this analogy can help us develop software better.

## Multitasking

As a general rule, you should work on no more than one or two short tasks at once. The brain gets overloaded more easily than you might think.

**Your mental CPU has a limited stack, with enough space for 3 or 4 items at the most**. Just like an OS switching between programs, when you work on more than one thing, your brain has to repeatedly unload relevant information for one task and load things for another one. We can't do this as fast as a CPU, and certainly nowhere near as often, so take it easy and focus on one thing.

![](http://i1.wp.com/chrismm.com/wp-content/uploads/2016/03/diagram.png?w=700%20700w,%20http://i1.wp.com/chrismm.com/wp-content/uploads/2016/03/diagram.png?resize=300%2C121%20300w) When you switch tasks, your mind has to figure out what you were thinking about previously

## Muscle memory

Repetitive training in a task will eventually make your brain run it on its "default network". **This is like the brain's muscle memory or a CPU's internal cache. Once you repeat a task enough times, it will become almost effortless.**

It's important to note that even when you're mentally exhausted, you can still do tasks you've got a lot of practice with.

You can take advantage of this process by reviewing things you do once in a while, and training yourself on that a bit every day. There are code challenge websites which help with this, but from what I've seen, they are usually focused on rather useless algorithms, like sorting, which you can find in a library most of the time, and memorizing them will offer little benefit.

A more useful training task might be writing an SQL query, or the code for a website's controller, or string handling, or practicing regular expressions, or even command line tools such as grep or awk.

I suggest that, **when starting a new project which will be using a new framework, you take some time every day to go through each one of the tutorials**. Eventually, you will be able to concern yourself with just your project's code. The rest, the glue, you will be able to do blind.

It can be tempting to use code generation tools in this case, such as Yeoman. They can be useful, but if you use them while learning, you just won't know the code nearly as well as if you had built everything yourself.

## Vocabulary

I have found that having a strong vocabulary on a particular subject can greatly increase the speed at which you process ideas on it. It makes sense since language is our main tool when interpreting thoughts and events.

As an example, it is indeed far easier to think "referential transparency" or "pure function" than to think "those functions that always return the same value and don't have side-effects".

For building a strong vocabulary, there is the obvious habit of reading a lot, which I think at this point most developers already know that they have to. Tools like [Pocket](http://getpocket.com) and [Feedly](http://feedly.com) come in handy.

I like to complement this with memorization of the most difficult concepts, especially things I don't run into very often while reading. You can use [Anki](https://ankiweb.net) for this (or any other spaced-repetition software). Load it up with lots of interesting definitions. Be creative: anything from databases, Big O formulas, C library functions, anything that might turn out to be useful.

![Some definitions I've been studying recently](http://i0.wp.com/chrismm.com/wp-content/uploads/2016/03/anki.png?w=454%20454w,%20http://i0.wp.com/chrismm.com/wp-content/uploads/2016/03/anki.png?resize=300%2C201%20300w) Some definitions I've been studying recently

The key takeaway from this is that **you don't know that you need something until you know that it exists** so, for instance, the benefit of memorizing the C standard library is not just to remember parameter orders, but also to have more tools at your disposal by knowing about more functions when you run into new problems.

## Wrapping it up

1. Streamline your work by splitting it intro small tasks
2. Avoid multitasking
3. Practice coding problems every day, beyond just your project's tasks
4. Memorize

## Additional reading

If you're interested in this, I strongly recommend that you give [Dr. Rock's book](http://amzn.to/1UjAAf0) a shot. It goes into a lot more detail and is certainly more authoritative and eloquent than me. Don't miss it!
