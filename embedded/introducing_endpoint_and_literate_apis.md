# Introducing Endpoint and Literate APIs

[Original URL](http://blog.tonicdev.com/2016/01/26/endpoint.html)

> We're excited to announce our latest feature: Tonic Endpoint. One thing we've always wanted is a great way to use Tonic to experiment with HTTP servers. Node is for servers, and now with Endpoint,...

[]() We're excited to announce our latest feature: [Tonic Endpoint](https://tonicdev.com/docs/endpoint). One thing we've always wanted is a great way to use Tonic to experiment with HTTP servers. Node is for servers, and now with Endpoint, Tonic is too.

Endpoint turns any Tonic Notebook into a server by exporting a single function, `tonicEndpoint`. This function takes a request and response, just like a typical _http.listen_ handler. That's it: no deploying, no ports to worry about. Your function will be called anytime someone visits the corresponding url on the **tonicdev.io** domain. Here's an example:

exports.tonicEndpoint = function(request, response) { response.end("Hello world!"); }

The code snippet above is actually a **live API**, unique to you. Just click on the URL to open it in a browser, or try sending requests with a tool like _cURL_. Any change you make will _immediately_ update the API.

When you combine this with built in access to every package on **npm**, it's easy to make quick "micro-apis" to test out a new idea, connect two services together, respond to a webhook, or anything else you can imagine.

## Literate APIs

Much like [literate programming](http://comjnl.oxfordjournals.org/content/27/2/97), the idea here is to make APIs that can be read to understand, and be easily shared. Take a look at this [oil paint filter](https://tonicdev.com/tolmasky/oil-paint-filter) example. It takes an image and makes it look like an oil painting:

[![](http://blog.tonicdev.com/assets/article_images/2016-01-26-endpoint/horse-api.png)](https://tonicdev.com/tolmasky/oil-paint-filter)

But what's really interesting is that, in addition to working as a Tonic notebook, it can also accept input as an HTTP request. Here's a live example that posts to the oil painting endpoint.

<span id="instructions">Enter the URL of an image above and Tonic will apply an oil painting filter. It works best with small images.</span>

Whenever you come across an Endpoint API, you can just change **tonicdev.io** to **tonicdev.com** and see how things were written. That means that if it doesn't do exactly what you want, you can easily fork it and modify it. Think of it like Github, but for living services.

## Using Endpoint

We've built some helpers that we think will make common tasks simpler: a simple [JSON API helper](https://tonicdev.com/tonic/json-endpoint), and a wrapper for using [Express](https://tonicdev.com/tonic/express-endpoint). You can read more about how to use Endpoint in the [full documentation](https://tonicdev.com/docs/endpoint).

Endpoint is an early experiment right now, and we still have much work to do, especially in the UI department. But we are really excited about the possibilities and wanted to start getting feedback. So please, [let us know](mailto:support@tonicdev.com) what you think!
