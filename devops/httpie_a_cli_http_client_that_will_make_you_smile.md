# httpie: A CLI http client that will make you smile

[Original URL](http://radek.io/2015/10/20/httpie/)

> Good tools make great craftsmen. Just as a sculptor owns a range of different chisels and hammers to remove the precise amount of marble at a time, we programmers need a repertoire of little helpers...

Good tools make great craftsmen. Just as a sculptor owns a range of different chisels and hammers to remove the precise amount of marble at a time, we programmers need a repertoire of little helpers that will take the mundane tasks out of the way so we can focus on our art.

What makes a good programming tool? I say that it needs to be focused on doing whatever it was designed to do _exceptionally well_. Just exactly what you need, without compromises. There's a whole lot of other things different people might expect from their tools and that's why there are so many.

Surely, the sculptor would be able to get the statue done with just a one-size-fits-all chisel and hammer. It might be a bit rough, but still a beautiful piece. But it would probably take ages to complete, with periods of horrible frustration when carving the eyelids with a clearly inadequate tool.

If you're doing back-end web development, [httpie](http://httpie.org/) might be one of the tools that are just right for you.

## What is it?

**httpie** is a command-line http client, much like cURL or wget. Written entirely in Python (using [requests](http://docs.python-requests.org/en/latest/) for the backend and [pygments](http://pygments.org/) for syntax highlighting), it's well portable to many platforms, including the big three: Windows, Linux and OSX.

The first version was published by [Jakub Roztocil](https://twitter.com/jkbrzt) in 2012\. And what really sets it apart is its user interface. It extends the basic functions of curl or wget with things like pretty-printing and syntax-highlighting that make all the difference. It recognises a few most-commonly used formats, such as JSON and HTML and processes the output accordingly. All that from the comfort of your terminal.

This is how it looks:

[![httpie vs. curl](http://radek.io/assets/images/posts/httpie/upstream-example.png)](http://radek.io/assets/images/posts/httpie/upstream-example.png) **httpie vs. curl**: A comparison. ([Source](https://github.com/jkbrzt/httpie)) **httpie** is open-source, distributed under the terms of the BSD licence. Check out Jakub's [repository on Github](https://github.com/jkbrzt/httpie) to see the code and contribute!

## Where do I get it?

The easiest way to get **httpie** is from [PyPI](https://pypi.python.org/pypi) using the following cross-platform command:

```
pip install httpie
```

If you prefer using OS-level packaging instead, **httpie** is widely available as a package in many Linux distros and [Homebrew](http://brew.sh/) for OSX too.

Check out all your options in the [project's README](https://github.com/jkbrzt/httpie#installation).

## How it works?

The interface isn't the simplest ---- the http protocol has many things that need to be covered, after all. But the design is intuitive and it _makes sense_ in a way that you'll remember it the first time you look at it. See the synopsis of the `http` command below:

```
http OPTS] METHOD] URL REQUEST_ITEMS...]
```

The only mandatory argument is the **URL**. Optionally, you can choose the method and provide several _request items_ ---- additional parameters for your requests. These might include extra headers, POST data, URL parameters, form fields and more. The `http` command also understands a variety of `--options` that affect both how your request will and the response will be handled.

The most basic usage therefore looks like this (mind you, there's no _ie_ at the end of the command):

```
http http://radek.io/
```

The method falls back to **GET** by default, as you would probably expect, and GitHub's servers will be serving you the content of my website shortly after. The headers are printed out along with the response; pretty simple.

[![httpie in the terminal](http://radek.io/assets/images/posts/httpie/basic.png)](http://radek.io/assets/images/posts/httpie/basic.png) **httpie**: Downloading my homepage.

## More examples

This is great, but **httpie** can do a whole lot more. You'll appreciate the convenience, particularly when sending requests slightly more elaborate than the one above. Check out the following examples.

### URL parameters

One of the things you can add to the request with _request items_ are GET parameters. Use two _equals_ signs between the name of the parameter and its value and httpie will take care of the rest.

```
http https://httpbin.org/get foobar wickedwitch
```

The above command will produce something like this:

[![httpie GET example](http://radek.io/assets/images/posts/httpie/get.png)](http://radek.io/assets/images/posts/httpie/get.png) **Example 1**: Adding GET parameters.

### POST

When sending JSON data with a POST request, you can specify key-value parts of the payload using a similar convention, but delimited with a single _equals_ sign. When preceded by a colon, the value is interpreted as a non-string, allowing you to pass over numbers, arrays and even whole objects.

```
http POST https://httpbin.org/post wickedwitch casts:'["spells", "curses"]' --json
```

Here's the output:

[![httpie POST example](http://radek.io/assets/images/posts/httpie/post.png)](http://radek.io/assets/images/posts/httpie/post.png) **Example 2**: Sending a JSON request using POST. A colon on its own makes **httpie** interpret the particular _request item_ as an additional header, attaching it to the request.

```
http GET https://httpbin.org/headers User-Agent:'Wicked Witch 1.0'
```

This is what I got in my terminal:

[![httpie custom headers example](http://radek.io/assets/images/posts/httpie/headers.png)](http://radek.io/assets/images/posts/httpie/headers.png) **Example 3**: Adding custom headers.

### Authentication

One of the many options allows you to get past basic auth as follows:

```
http GET https://httpbin.org/basic-auth/wicked/witch -a wicked:witch
```

And here's the output:

[![httpie basic auth example](http://radek.io/assets/images/posts/httpie/basic-auth.png)](http://radek.io/assets/images/posts/httpie/basic-auth.png) **Example 4**: Basic auth with httpie. This was of course just the tip of the iceberg. Head over to the [project's README](https://github.com/jkbrzt/httpie/blob/master/README.rst) or the command's `--help` for more examples and detailed explanations.

Whether you need to automate parts of your workflow with a quick bash script or test something while developing an API, **httpie** is a great addition to your virtual toolbox. It can't really do much more than `curl` or `wget`, but it's a whole lot nicer. Don't use a large hammer for the eyelids, give [httpie](https://github.com/jkbrzt/httpie) a go!

Looking for more awesome tools? Check out my previous posts about [ack](http://radek.io/2014/08/25/ack/) and [tco](http://radek.io/2014/03/30/tco/).
