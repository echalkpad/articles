# Bookmarklet Scripts

[Original URL](http://botleg.com/stories/bookmarklet-scripts/)

> Bookmarklet allows you to run some script by clicking them after adding those to your browser as bookmarks. When your users click the bookmark, the script runs on the current page that the user is on...

Bookmarklet allows you to run some script by clicking them after adding those to your browser as bookmarks. When your users click the bookmark, the script runs on the current page that the user is on and that information can be used. In this article, we will see how to create a bookmarklet script to send your current page with some tags via `AJAX` requests. We will also look into how to identify and authenticate users who are clicking this bookmarklet. You can see the code for the script [here](https://gist.github.com/hanzeljesheen/b056fe4319955ce14b53).

## The Basic Idea

The bookmarklet script is a piece of javascript code that you can save as the bookmark. When the bookmarklet is clicked, the javascript code will be executed. The javascript code will be enclosed in an anonymous function which is called as soon as it's defined. We enclose the function with parenthesis and prepend it with `javascript:` to tell the browser that the code is infact javascript.

```
javascript: (function() {
 // your script here.
}());
```

This is the basic structure of a bookmarklet script and we can minify this to a single line.

## AJAX Request

One of the basic thing that we need with a bookmarklet script is to send something to a server. This can be done in javascript with AJAX requests. You can read more about AJAX requests [here](http://www.w3schools.com/ajax/ajax_xmlhttprequest_send.asp).

```
javascript: (function() {
 xhr = new XMLHttpRequest();

 xhr.open("POST", encodeURI("http://localhost:8080"));

 xhr.send(JSON.stringify({
    url: document.location.href
 }));
}());
```

Here, we are sending a `POST` request to `http://localhost:8080`. We can get the current page address with `document.location.href` and we are sending this as the body of the POST request.

One thing to note here is `CORS`. The origin of the AJAX request will be from the user's current page URL, which won't have the same domain as the the destination of the request. This make the request `cross-domain`. For example, if you are clicking the bookmarklet when you are in `google.com`, your origin is `google.com` and destination is `localhost`. To allow such `cross-domain` request, the server must set the following headers.

```
Access-Control-Allow-Origin: '*'
Access-Control-Request-Method: '*'
Access-Control-Allow-Methods: 'POST'
Access-Control-Allow-Headers: 'Content-Type'
```

## UI Elements

Now that we have sent location of the page, let's also send some tags that the user can input. For this we need a form with text field and a button, the UI elements. To achieve this with out script, we save the required HTML as a string and add that to out page.

```
javascript: (function() {
 var el = '
 <div id="bookmarklet" style="
 position:fixed;
 background: #FFF;
 border: 1px #000 solid;
 right:50px;
 top:50px;
 z-index:1000;
 width:250px;
 ">
 Tag: <input type="text" id="tag-input">
 <button id="add-tag">Submit</button></div>';
 document.body.innerHTML += el;
}());
```

Here, we have a `div` with a textfield and a button saved as string in `el` variable. This variable is then added to our page and this gives us the HTML elements in the current page. We use the `position:fixed` and `z-index:1000` property on the enclosing `div` to make the box floating on top of the current page.

Make sure that you style all the elements in your UI with custom CSS. Otherwise, these elements will inherit the current page's CSS properties. That could make your UI look different when opened in different pages.

Once new elements are added to the current page, we can make the AJAX request with the `click` event on the button. To make this happen, add the following to the end of your script, before the anonymous function ends.

```
document.getElementById('add-tag').addEventListener("click", function () {
 xhr = new XMLHttpRequest();

 xhr.open("POST", encodeURI("http://localhost:8080"));

 xhr.send(JSON.stringify({
 url: document.location.href,
 tags: document.getElementById('tag-input').value
 }));
 document.getElementById('bookmarklet').remove();
});
```

## Authentication

If you have a cookie based authenication system and the user has logged in to your site, every request to your site will have the cookie details in its header. We can use that for authentication. There is however a caveat, the browsers don't send the cookie details for cross-domain AJAX requests by default. So, we need to add the following line before send the `POST` request.

```
xhr.withCredentials = true;
```

So the entire code for the bookmarklet script will look like this.

```
javascript: (function() {
 var el = '
 <div id="bookmarklet" style="
 position:fixed;
 background: #FFF;
 border: 1px #000 solid;
 right:50px;
 top:50px;
 z-index:1000;
 width:250px;
 ">
 Tag: <input type="text" id="tag-input">
 <button id="add-tag">Submit</button></div>';
 document.body.innerHTML += el;
 document.getElementById('add-tag').addEventListener("click", function () {
 xhr = new XMLHttpRequest();

 xhr.open("POST", encodeURI("http://localhost:8080"));
 xhr.withCredentials = true;

 xhr.send(JSON.stringify({
 url: document.location.href,
 tags: document.getElementById('tag-input').value
 }));
 document.getElementById('bookmarklet').remove();
 });
}());
```

## Conclusion

In this article, we have seen how to create bookmarklet scripts that can send AJAX requests, create UI elements and even do authentication.
