# getiblog

[Original URL](http://blog.getify.com/concurrently-javascript-1/)

> What is concurrency? How is it different from parallelism? JavaScript is single-threaded on the event loop, so how does its asynchrony fit into the mix? These are great questions, but sadly, I think...

What is concurrency? How is it different from parallelism? JavaScript is single-threaded on the event loop, so how does its asynchrony fit into the mix?

These are great questions, but sadly, I think most JS developers are not really asking them enough. The concepts behind writing concurrent code are incredibly important, but we tend to only focus on the API provided to us by some library or framework. These APIs have a tremendous capability to shape our thoughts about the underlying functionalities, and often not in accurate or productive ways.

I want us to talk first about the concepts around concurrency before we focus on what we can do with some API or pattern. **Don't worry:** following parts of this blog post series will talk more about APIs, if that's what really gets you excited. But don't skip over these concepts.

## Parallelism

Parallelism in computing is when two or more _things_ are happening at exactly the same instant. Practically speaking, this happens only when there's two separate cores in the system's CPU, so at any given moment, different electrical impulses are firing.

BTW, the _things_ we're talking about happening in parallel are not _big_ tasks like network requests and responses. Instead, we're talking about very low-level actions, like adding two numbers together or performing a bitwise operation on a single number. Let's call these **operations** rather than **tasks**.

It may be hard to picture it, but the high-level things we do in programming like sorting numbers, making database queries, or styling the text with a custom font – they really do break down to (lots of) these very low-level operations.

### Concurrency

Concurrency means two or more _things_ are happening in the same time period. But here, the _things_ are tasks, not operations. So, compare concurrency to parallelism.

See how they're similar but also distinct?

Concurrency speaks to the idea of having two high-level tasks – like an Ajax request/response cycle and a repainting of the page after a scroll event – happening "at the same time".

Each task's low-level operations might or might not be happening in parallel. Concurrency doesn't necessarily imply parallelism, but includes parallelism conceptually.

### Threads

Parallelism is usually modeled in programming through a construct called **threads**.

In a really rough sense, we can think of a thread as an ordered list of operations to be performed by the system. A system level thread would be the set of tasks that a CPU core is going to perform. So if a computer has 4 cores, there would be at most 4 system threads that could be processing 4 separate lists of operations.

That entire set of operations could be conceptually composed together to look like one or more high level tasks we might be working with in our programs.

Of course, if your computer only has 4 cores, and thus only 4 possible system level threads at any given moment, that's going to be rather limiting and difficult for the dozen or more programs you have open right now to trade off on what they need to get done.

So your operating system will provide OS level (virtual) threads on demand to programs that ask for them. At any given moment, there could be dozens, hundreds, even thousands of these spinning. It's up to the OS to _schedule_ these virtual threads across the actual system threads (CPU cores).

The OS is pretty good at this scheduling, so even if there are many more virtual threads than system threads, the processing of all those operations will still procede as if they're all happening truly in parallel.

## Async

Now, what about asynchrony? JavaScript programmers will likely be familiar with the notion that JS is asynchronous, and some will have even heard that JavaScript is "single threaded".

That's true. From the language perspective, the JS code makes the assumption that no two lines in the same program instance will ever be executing at exactly the same time.

In fact, normal JS functions have "run to completion" semantics, meaning that in fact no function that's currently running can be preemtively interrupted by another function. Of course, function **A** can _call_ function **B**. But **B** can never be scheduled to start running in parallel with **A**.

That means **A** never needs to worry about any side effects that **B** may have on the program state, except from the moment **A** invokes **B** to when **B** fully completes.

This semantic is significantly simplifying in terms of ease of writing correct, bug-free code.

JavaScript trades out the power of multi-threading parallelism for much simpler language mechanics. Because the event-loop is so efficient, JS programs can achieve impressive amounts of performance even though they're only running on a single thread.

Of course, JS programmers have some role to play there. The better we are at breaking up tasks into operations, the more effective the JS engine can be at scheduling these operations on the single thread event loop.

## Coordination

When you have two separate lists of operations to perform that are entirely _independent_ – they don't depend on each other in ordering, and they don't use any _shared resources_ – modern parallel threaded programming is mostly a no brainer.

But much more commonly, you need to coordinate some _dependency_ between the threads. Coordination is the hard part.

### Parallel

Imagine you have two different tasks to perform with several operations in each task. Let's visualize that situation with two lists:

Task A:

- operation 1a
- operation 2a
- operation 3a

Task B:

- operation 1b
- operation 2b
- operation 3b

If **Task A** is a file reading operation and **Task B** is a file writing operation, and they happen to be against the same file, you can probably imagine how the order of these operations is pretty important. If you can't know, and indeed control predictably, the order of operations, your program will have a different outcome each time it runs.

That sort of situation is not _always_ an error, but it's often seen as one, and given the label: "race condition".

To avoid race conditions, your program needs to decide what type of coordination needs to occur. For example, you may say **A** needs to wait for all of **B** to finish before it starts. That's the easiest of the coordinations. But it's also the least interesting to explore conceptually.

What if **1a** is "open a file handle" and **1b** is "open a file handle"? It would seem like those two things could happen at the same time, right? But if **2a** is "read the contents of the file" and **2b** is "write some more text to the file", the order of those two really matters. Finally, **3a** and **3b** are both "close the file handle", and again, these operations are OK to happen in parallel.

Our desired result may be that we want the new contents being written to the file to also be read back out. Or, we may want the opposite: reading before writing.

The desired coordination could be visualized as such (the two columns are two separate threads where the operations happen in parallel):

There's several components of coordination to consider here. First, **1a** and **1b** need to be instructed to occur in parallel.

Next, **2b** needs to be instructed to wait for both **1a** and **1b** to finish, regardless of which order that might occur in. Consequently, **2a** needs to wait for **2b** to finish before it runs. Finally, **3a** and **3b** need to wait for **2a** to finish, and then need to fire in parallel.

### Async / Parallel

Whether we're talking about truly parallel programming, or async (single-threaded) programming like in JS, coordination of concurrency is absolutely foundational for effective programming.

The coordination we're talking about here should be fairly straightforward to conceptualize. But modern concurrency is fantastically more complicated. We need to figure out how to build up higher patterns from lower level ideas.

The coordination steps we just talked about can be thought of in two categories:

- things happening in parallel
- things happening in series

For "things happening in parallel", our only concern is that we be able to wait for all the _things_ in a set to complete before moving on. For "things happening in series", we need to be able to wait for a _thing_ to complete before moving on to the next _thing_.

Hopefully you can see how we can build all other higher order notions of concurrent programming by mixing and building on top of these two building blocks.

## JavaScript Concurrency

The coordination concepts we've layed out in this post are expressable in some form or another in JS. But why would we choose one form over another? Because the understandability of our code is at least as important as the functionality of our code.

Starting with the humble **callback**: to express a series, you call one callback at the end of the previous one. to express a parallel group, you call multiple callbacks in a row, and you use some shared (global or closured) varaiable(s) to maintain and coordinate the state.

**Thunks** are a pattern for organizing closure in callbacks to eliminate ordering (time) complexity. So it's much easier to express both series and parallel with them. IOW, we have the same capability – concurrent coordination – but our chosen pattern improves the code readability.

**Promises** are like thunks but with more trustability and a nicer API. So, concurrency is even better with promises than with thunks. A series is a promise chain, and a parallel group is achieved with `Promise.all(..)`.

**Generators** (+ Promises) achieve almost perfect sync-looking async code, which makes expressing our coordinated concurrency look even closer to how our brains work.

But there's more higher order patterns for even more sophisticated concurrency. In subsequent parts of this blog post series, we're going to look at **reactive programming** (event-concurrency) and **CSP** (channel-concurrency).

Our goal is to get better about using the conceptuals we've covered here to express concurrency in our programs in more _reason_able ways.

[Tweet](http://twitter.com/share?_=1701050996&original_referer=http%3A%2F%2Fblog.getify.com%2Fconcurrently-javascript-1%2F&related=AustinJS%3AAustin.JavaScript%20monthly%20meetup%20group&text=Concurrently+JavaScript+%28part+1%29&url=http%3A%2F%2Fblog.getify.com%2Fconcurrently-javascript-1%2F)
