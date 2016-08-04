# Optimize Python with Closures

[Original URL](http://tech.magnetic.com/2015/05/optimize-python-with-closures.html)

> This article is cross-posted on my personal blog, late.am. Magnetic's real-time bidding system, written in pure Python, needs to keep up with a tremendous volume of incoming requests. On an...

This article is [cross-posted](http://late.am/post/2015/05/07/optimize-python-with-closures.html) on my personal blog, [late.am](http://late.am/).

Magnetic's real-time bidding system, written in pure Python, needs to keep up with a tremendous volume of incoming requests. On an ordinary weekday, our application handles about 300,000 requests per second at peak volumes, and responds in under 10 milliseconds. It should be obvious that at this scale optimizing the performance of the hottest sections of our code is of utmost importance. This is the story of the evolution of one such hot section over several performance-improving revisions.

## Real Time Bidding

Real Time Bidding, or 

<span class="caps">RTB</span>

, is a technique by which many internet ads delivered. When you visit a website using 

<span class="caps">RTB</span>

, a request is sent to dozens or hundreds of "bidders" which have to quickly decide whether they want to show you an ad, and if so, how much they would like to pay. 

<span class="caps">RTB</span>

 bidders have up to 100 milliseconds to make this decision -- any slower and you won't win the auction no matter how much you bid -- so performance is key.

At Magnetic, we do much of our targeting in real time during the bid request. We use a combination of filters to qualify ad campaigns for the particulars of the bid request. Here we'll consider just one of the several types of filters we use, one which checks that the content category of the page where an ad will be shown against a set of categories that we want to target.

Of course, different campaigns have different targeting criteria, and thus different filter configurations. Additionally, each campaign may use a different subset of filters. On average, we end up calling about 150 filters per bid request, or 4.5 million filters per second total at peak times, so ensuring maximal performance is essential.

## First Approach: Classes

The obvious way to implement such a filter is to use a class to store the configuration, thus allowing multiple instances of the filter for different campaigns. At bidding time, we look up the set of instances which represent the filters for each of the campaigns the user was targeted for, and call each filter to determine if the user is eligible for the campaign. Such a class might look like:

```
class PageCategoryFilter(object):
 def __init__(self, config):
 self.mode = config["mode"]
 self.categories = config["categories"]

 def filter(self, bid_request):
 if self.mode == "whitelist":
 return bool(
 bid_request["categories"] & self.categories
 )
 else:
 return bool(
 self.categories and not
 bid_request["categories"] & self.categories
 )
```

This code would pass most code reviews, and is a fairly straightforward implementation of the idea we've thus far described only in prose. (We pass in a dictionary of configuration, rather than direct arguments, since we have different types of filters and want all of them to expose the same interface to the code that sets them up and calls them.)

Unfortunately, though perhaps unexpectedly given the topic of this post, there is a performance problem with this approach, especially for a block of code that will be called millions of times per second.

## The Bound Method Problem

Superficially, accessing the `categories` attribute and `filter` method appear to be doing about the same amount of work -- both access the attribute of an instance. Unfortunately, looks can be deceiving:

```
>>> a_filter = PageCategoryFilter(["foo", "bar", "baz"])
>>> a_filter.categories
frozenset(['baz', 'foo', 'bar'])
>>> a_filter.filter
<bound method PageCategoryFilter.filter of <PageCategoryFilter object at 0x107f13910>>
```

As expected, `categories` returns the attribute's value, but accessing `filter` returns a bound method object. What is a bound method? It's the magic that allows Python to insert the `self` argument when you call the method.

Specifically, an instance's methods are access via a descriptor, a Python feature that allows some code to be executed to satisfy the results of an attribute access expression. When Python executes the definition of a class, it wraps each function in a descriptor whose job is to supply the `self` argument. Later, when you access the attribute for the method, Python calls `__get__` on the descriptor, and supplies the instance as an argument. This allows the method descriptor to rewrite the call to the underlying function to include the `self` argument. For more, see [Christ Beaumont](http://chrisbeaumont.org/)'s excellent [Python Descriptors Demystified](http://nbviewer.ipython.org/urls/gist.github.com/ChrisBeaumont/5758381/raw/descriptor_writeup.ipynb), or watch a short [video version](https://www.youtube.com/watch?v=9A1ycvcgc_U) of it.

From a performance perspective, the key difference between ordinary attribute access and method calls is that each time you call a method, or rather each time you access the attribute which refers to a method, the Python 

<span class="caps">VM</span>

 must execute some additional code to create the bound method. It must do more work in order to provide the `self` argument. None of this extra work is ordinarily visible -- it doesn't show up in stack traces, for instance -- but it does take some small amount time to execute, and if you call methods often enough, that can add up.

## Second Approach: Functions

If bound methods are a problem, perhaps we can arrange for all the pertinent arguments just to be passed in to an ordinary function. This should avoid the overhead of supplying the `self` attribute:

```
def page_category_filter(bid_request, config):
 if config["mode"] == "whitelist":
 return bool(
 bid_request["categories"] & config["categories"]
 )
 else:
 return bool(
 config["categories"] and not
 bid_request["categories"] & config["categories"]
 )
```

As we'll see in benchmarks, this code slightly outperforms the class-based implementation by avoiding the need for bound method calls. But is this as fast as we can make the filter go?

## The Dictionary Access Problem

In either branch of this function, we do three dictionary accesses: one for "mode", one for the "categories" item in the bid request dictionary; and one for the "categories" item in the configuration dictionary. Since none of these accesses is repeated in any individual call of this function, it makes no sense to assign the result of the dictionary accesses to a local variable and thus "cache" the result.

<span class="dquo">“</span>

But wait," you say, "aren't dictionaries fast?"

Ordinarily, and algorithmically, yes, dictionaries are quite fast. However, hidden behind those square brackets is quite a bit of work: Python must hash the key, apply a bit-mask to the hash, and look for the item in an array that represents the storage for the dictionary. Edge cases can create collisions which take even longer to resolve. For a more thorough exploration of how dictionaries are implemented in Python, see [Brandon Rhodes](http://rhodesmill.org/brandon/)' talk [The Mighty Dictionary](http://rhodesmill.org/brandon/talks/#mighty-dictionary).

In practice, for frequently-called code the cost of dictionary item access, however small, is amplified and adds up.

## Final Approach: Closures

So if even dictionaries are too slow, how can we resolve this performance crisis? We can turn the "variables" stored in the dictionary into local variables (sort of), which are the fastest to access since they are stored in an array and referenced by pre-computed offset. Accessing a local variable in Python is nearly instantaneous, or as close to it as we can get writing Python code.

A closure is a function which uses variables defined in its enclosing scope -- a nested function. Python recognizes that the (inner) function uses variables that aren't in its argument list, and makes those variables available to the inner function. Such an inner function is said to "close over" the variables it uses from the outer scope, hence "closure".

Because the number and names of closures is known at compile time, the 

<span class="caps">VM</span>

 can use an array to track the closed-over variables, in the same way as it uses an array to track local variables and constants. For more on how Python builds and executes a function, see [Exploring Python Code Objects](http://late.am/post/2012/03/26/exploring-python-code-objects.html).

We translate our page category filter into a closure by creating a factory function which returns another function, the closure itself. From the perspective of the calling code, this looks and feels a lot like creating an instance of a class.

```
def make_page_category_filter(config):
 categories = config["categories"]
 mode = config["mode"]
 def page_category_filter(bid_request):
 if mode == "whitelist":
 return bool(bid_request["categories"] & categories)
 else:
 return bool(
 categories and not
 bid_request["categories"] & categories
 )
 return page_category_filter
```

As we'll see, this implementation outperforms either of the others, because we avoid two of the three dictionary lookups, and replaces them with fast access to the closed-over variables `categories` and `mode`. Additionally, callers don't suffer from the bound method problem, since `page_category_filter` is just a regular function with no magically inserted arguments.

## The Proof is in the Timing

Each of the three methods shown here is "fast" in the sense that filtering any single bid request takes very little actual time -- less than a microsecond on my machine in all cases. However, since this code (and other code like it) is called inside of what amounts to very tight loops, even tiny differences in the run time of a single invocation amount to a lot overall.

To fully exercise the code paths, we'll create four filter configurations: a whitelist with some allowed categories, a blacklist with some forbidden categories, an empty whitelist, and an empty blacklist. We'll call each of them an equal number of times in a straightforward timing loop. Here's an example using the first implementation:

```
filters = [
 PageCategoryFilter(dict(mode="whitelist", categories=["foo", "bar", "baz"])),
 PageCategoryFilter(dict(mode="blacklist", categories=["foo", "bar", "baz"])),
 PageCategoryFilter(dict(mode="whitelist", categories=[])),
 PageCategoryFilter(dict(mode="blacklist", categories=[])),
]

bid_request = {"categories": set("bat")}
start = time.time()
for _ in xrange(N):
 for a_filter in filters:
 a_filter.filter(bid_request)
end = time.time()
```

Averaged over 15 million calls to each filter in CPython 2.7.9 on my 2013 MacBook Pro, 2.6GHz Core i7, I get:

Approach | Per-Call Time | Speedup
-------- | ------------- | -------
Class    | 0.4082 us     | --
Function | 0.3744 us     | 8.2%
Closure  | 0.3213 us     | 21.3%

Feel free to check out [the benchmark script](https://gist.github.com/dcrosta/3f60796682aa6a72b003).

## What about PyPy?

At Magnetic, we've switched from running all of our performance-sensitive Python code from CPython to PyPy, so let's consider whether these code optimizations are still appropriate. I used the same benchmark setup as above, but with PyPy 2.5.1 and a warmup run for each implementation to allow PyPy time to 

<span class="caps">JIT</span>

:

Approach | Per-Call Time | Speedup
-------- | ------------- | -------
Class    | 0.0432 us     | --
Function | 0.0554 us     | -28.3%
Closure  | 0.0431 us     | 0.1%

PyPy is incredibly fast, around an order of magnitude faster than CPython in this benchmark. Because PyPy performs more advanced optimizations than CPython, including many optimizations for classes and methods, the timings for the class vs. closure implementations are a statistical tie.

Curiously, the function implementation is actually slower than the class approach in PyPy. PyPy is able to optimize code using classes more than code using dictionaries, because it is able to specialize code for each class in your program. Dictionaries, on the other hand, are a generic mapping data structure, and PyPy is not able to create specialized machine code for one particular "shape" of dictionary or another. [Alex Gaynor](https://alexgaynor.net/) covers this distinction in greater depth in his talk [Fast Python, Slow Python](http://pyvideo.org/video/2627/fast-python-slow-python) from PyCon 2014.

## So what have we learned?

The [Zen of Python](http://legacy.python.org/dev/peps/pep-0020/) implores us to implement our code in the "one -- and preferably only one -- obvious way", which is likely one of the first two approaches presented here (depending on whether you favor functions or classes). Indeed, this is still good advice, as code is read far more often than it is written. However, there are cases where less obvious ways have distinct benefits. Or maybe I'm just Dutch.

We also must always remember our [Knuth](http://en.wikipedia.org/wiki/Donald_Knuth): "[premature optimization is the root of all evil](http://c2.com/cgi/wiki?PrematureOptimization)." At Magnetic, we pursued this optimization only because it made sense for our application, where the filters are called very frequently, and only after measurement showed that the real-time filters accounted for a large amount of our per-request processing time.

_Many thanks to [A. Jesse Jiryu Davis](http://emptysqua.re/) for reviewing a draft of this article._

Tags: [python](http://tech.magnetic.com/tag/python.html), [performance](http://tech.magnetic.com/tag/performance.html)
