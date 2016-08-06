# Building a Slack Clone in Meteor.js: Getting Started

[Original URL](https://scotch.io/tutorials/building-a-slack-clone-in-meteor-js-getting-started)

> This is the first of a five-part series on building a Slack clone using Meteor. The aim of these tutorials are not just for you to blindly follow instructions, but it's our hope that...

> This is the first of a five-part series on building a Slack clone using Meteor. The aim of these tutorials are not just for you to blindly follow instructions, but it's our hope that you'll understand the thought process and reasoning behind the architecture.

## Installation

Installation is simple, just run the shell script Meteor prepared for us!

```
$ curl https://install.meteor.com/ | sh
```

> If you're using Windows, download the [official Meteor installer for Windows](https://install.meteor.com/windows).

The installation will provide us with a command-line tool called the [Meteor Tool](https://www.meteor.com/tool), which gives us the `meteor` commands.

To see what features the Meteor Tool provides, simply run

```
$ meteor --help
```

## A Meteor App

We will use the `meteor create` command to create a dummy application.

```
$ meteor create slack
slack: created. 
```

Simple! And now to run your application...

```
cd slack 
meteor
```

You can now go to `localhost:3000` and see this dummy application:

[![dummy-meteor](https://cask.scotch.io/2015/05/dummy-meteor-1026x500.png)](https://cask.scotch.io/2015/05/dummy-meteor.png)

> To stop the application, press Ctrl + C

Great! We have a counter! But this is not what we want! So leave the tab opened and keep an eye on it, then delete everything apart from the `.meteor` directory.

Open up a new terminal and run

```
rm slack.*
```

### Hot Code Push

You'll see the screen automatically refresh into a blank screen; you may also see in the terminal, where we ran the `meteor` command, display the following lines.

```
=> Modified -- restarting. 
=> Meteor server restarted
```

It shows that, while running in development mode, Meteor will detect changes you have made and automatically re-build the application. Once built, it will use a feature of Meteor termed _Hot Code Push_, and push those changes to all the clients that are connected to it, without them having to refresh.

This is because the client and the server connects through what is essentially [WebSockets](https://developer.mozilla.org/en/docs/WebSockets) connections, so the server can send messages to the client at any time.

This makes development a joy because you don't have to manually refresh the page each time you make a tiny change to the CSS – your changes are live.

This hot code push feature is also intelligent, if you make changes to the server code which does not affect the client, those changes are not pushed.

### `.meteor`

So what is this Meteor magic? It's no magic at all, it's all the work of the Meteor's core packages.

If you look inside the `.meteor` directory, you'll see these files.

```
$ ll
total 40
drwxr-xr-x 3 daniel daniel 4096 Apr 25 19:19 ./
drwxr-xr-x 8 daniel daniel 4096 Apr 25 19:22 ../
-rw-rw-r-- 1 daniel daniel 229 Apr 25 18:55 .finished-upgraders
-rw-rw-r-- 1 daniel daniel 6 Apr 25 18:55 .gitignore
-rw-rw-r-- 1 daniel daniel 323 Apr 25 18:55 .id
drwxrwxr-x 5 daniel daniel 4096 Apr 25 18:59 local/
-rw-rw-r-- 1 daniel daniel 274 Apr 25 18:55 packages
-rw-rw-r-- 1 daniel daniel 15 Apr 25 18:55 platforms
-rw-rw-r-- 1 daniel daniel 15 Apr 25 18:55 release
-rw-rw-r-- 1 daniel daniel 825 Apr 25 18:55 versions
```

The `.meteor/packages` file contains all the packages our application currently depends on. Currently there are three – `meteor-platform`, `autopublish` and `insecure`. The `meteor-platform` package is the meat of Meteor, but it's nothing more than a collection of other packages.

In fact, if you look inside the `.meteor/versions` file, you'll see the exact version of every package installed. All the entries apart from `autopublish` and `insecure` are dependecies specified from the `meteor-platform` package.

### Updating Meteor

Information about your current Meteor release is stored in `.meteor/release`. You can run the command `meteor update` to update Meteor, and that tells Meteor tool to check inside this file and see if it is already the latest version, and if not, updates itself.

It's good practice to always be using the latest version of Meteor, especially when it comes to security fixes.

## File Structure

Remember that Meteor is a full-stack framework, which means it includes both client- and server-side code. Meteor has a few special directories that helps us separate them.

- `client/` – _Any_ files inside _any_ directories named `client` are purely client-side, and will not be loaded by the server. We will add code for event handlers here.

- `server/` – Conversely, any files inside any directories named `server` are purely server-side, and will not be sent to the client. We will add code we don't want the users to see here, such as code for password authentication.

- `public/` – We will place assets that needs to be served as-is to the client in this directory. You can reference them as if they are in the root directory. So `public/img/kittens.png` can be accessed in your HTML files as `img/kittens.png`. We will put images, `robot.txt`, `favicon.ico` files here.

- `private/` – Like `public/`, `private/` houses assets which should only be accessed by the server via the [Assets API](http://docs.meteor.com/#/full/assets).

Go ahead and create these directories now.

```
$ mkdir client server public private
```

## Creating the Layout

[![slack](https://cask.scotch.io/2015/05/slack1-928x500.png)](https://cask.scotch.io/2015/05/slack1.png)

If you look at HTML of the Slack app, you'll find it divided into a header, a body and a footer, with each section further divided into left and right sections.

So let's create a HTML layout that mimics this; we'll call it `room.html`. Since this is HTML, which gets loaded only on the client, it makes sense to put it in the `client/` directory.

```
$ vi client/room.html
```

Below is the HTML for a simplified version of the Slack chatroom, which you can find at `client/room.html` in the source code. To make it look like Slack, we have also included some CSS – `client/global.css`. You can find all the source code for this post on GitHub.

```
<head>
 <meta charset="utf-8">
 <meta http-equiv="X-UA-Compatible" content="IE=edge">
 <title></title>
 <link href='http://fonts.googleapis.com/css?family=Lato:400,700,900' rel='stylesheet' type='text/css'>
</head>
<body>
 <div class="header">
 <div class="team-menu">scotch</div>
 <div class="channel-menu">
 <span class="channel-menu_name">
 <span class="channel-menu_prefix">#</span>
 general
 </span>
 </div>
 </div>
 <div class="main">
 <div class="listings">
 <div class="listings_channels">
 <h2 class="listings_header">Channels</h2>
 <ul class="channel_list">
 <li class="channel active ">
 <a class="channel_name">
 <span class="unread">0</span>
 <span>
 <span class="prefix">#</span>
 hk
 </span>
 </a>
 </li>
 </ul>

 </div>
 <div class="listings_direct-messages"></div>
 </div>
 <div class="message-history">
 <div class="message">
 <a href="" class="message_profile-pic"></a>
 <a href="" class="message_username">scotch</a>
 <span class="message_timestamp">1:31 AM</span>
 <span class="message_star"></span>
 <span class="message_content">Lorem ipsum dolor sit amet, consectetur adipisicing elit. Rem molestias incidunt officia animi ducimus nostrum a cum, sunt, minima fugit repellendus assumenda expedita. Eaque autem vero delectus, optio, consequuntur suscipit.</span>
 </div>
 </div>
 </div>
 <div class="footer">
 <div class="user-menu">
 <span class="user-menu_profile-pic"></span>
 <span class="user-menu_username">danyll</span>
 <img class="connection_icon" src="data:image/png;base64,iVBORw0KGgoAAAAN...">
 <span class="connection_status">online</span>
 </div>
 <div class="input-box">
 <input type="text" class="input-box_text">
 </div>
 </div>
</body>
```

The end result looks like this:

[![slack-mockup-1](https://cask.scotch.io/2015/05/slack-mockup-11-889x500.png)](https://cask.scotch.io/2015/05/slack-mockup-11.png)

Not an exact match, but it's close enough!

## Templating

But that was a _big_ file, and it can become a problem when we have multiple people working on it at the same time – the front-end developer wants to add a class while the backend developer wants to change a variable name? Even with Git, it can be messy.

HTML5 introduced a new [`<template>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) element, which Meteor takes advantage of to allow us to separate our markup into more managable chunks.

You simply take out some markup and wrap it inside a `<template>` element. You can then insert or remove that template dynamically. The `<template>` element should have an attribute `name`, like `header`, which is what you'll refer to when you use the template.

Meteor uses its own version of the [Handlebars](http://handlebarsjs.com/) templating engine called Spacebar, and that's what we'll use to insert our templates.

### Spacebars

There are four major types of template tags provided by Spacebars:

1. `{{ variable }}` – Everything inside the double-braces are evaluated and printed as plain text
2. `{{> message }}` – inserts a template by name. So to insert the `header` template, I'd write `{{> header}}`.
3. `{{#each}}` – these _block tags_ define logic you'd be familiar with, like `#if` or `#each`
4. `{{{}}}` – Everything inside the triple-braces are inserted as raw HTML

Spacebars leaves you with a much neater, manageable markup.

#### room.html

```
<head>
 <meta charset="utf-8">
 <meta http-equiv="X-UA-Compatible" content="IE=edge">
 <title></title>
 <link href='http://fonts.googleapis.com/css?family=Lato:400,700,900' rel='stylesheet' type='text/css'>
</head>
<body>
 {{> header}}
 <div class="main">
 {{> listings}}
 {{> messages}}
 </div>
 {{> footer}}
</body>
```

#### messages.html

```
<template name="messages">
 <div class="message-history">
 {{> message text="All these messages"}}
 {{> message text="Uses the same template"}}
 {{> message text="But have a different data context"}}
 {{> message text="It's why these messages are all different!"}}
 </div>
</template>
```

#### message.html

```
<template name="message">
 <div class="message">
 <a href="" class="message_profile-pic"></a>
 <a href="" class="message_username">scotch</a>
 <span class="message_timestamp">1:31 AM</span>
 <span class="message_star"></span>
 <span class="message_content">Some sample text</span>
 </div>
</template>
```

### Meteor's Magic

You might have noticed that we named our HTML file `room.html`, and not the usual `index.html`; but when we navigated to `localhost:3000`, we were still able to see the page. Without routing, how does Meteor know which files to serve?

When you build your application, Meteor goes through all the `.html` files that are not inside the `server/`, `public/` and `private/` directories, and search for `<head>`, `<body>` and `<template>` elements. The contents of the these elements are aggregated together and translated into JavaScript, which gets sent to the client. The client will then re-build the page using these scripts.

Looking at the HTML file that does get sent over, you can see that it's just a bare-bone skeleton – everything is rendered via information contained in the JavaScript files. This is why the name of the HTML file doesn't matter.

```
<!DOCTYPE html>
<html>
<head>
 <link rel="stylesheet" type="text/css" class="__meteor-css__" href="/bf13b96942f2893b46b5edb25734c3eb9c47279b.css?meteor_css_resource=true">
<script type="text/javascript">__meteor_runtime_config__ = JSON.parse(decodeURIComponent("%7B%22meteorRelease%22%3A%22METEOR%401.1.0.2%22%2C%22ROOT_URL%22%3A%22http%3A%2F%2Flocalhost%3A3000%2F%22%2C%22ROOT_URL_PATH_PREFIX%22%3A%22%22%2C%22appId%22%3A%221glu7f5jidcmv1vfqmqa%22%2C%22autoupdateVersion%22%3A%22350c1c5d88ed8487ef7af2db8f2cb02c36651cd5%22%2C%22autoupdateVersionRefreshable%22%3A%22df31ac58c2d23c893d30002007959e1c98c88700%22%2C%22autoupdateVersionCordova%22%3A%22none%22%7D"));</script>
 <script type="text/javascript" src="/56c27ba4c0d6bab40c8e71c90d30aec9014a83be.js"></script>
<meta charset="utf-8">
 <meta http-equiv="X-UA-Compatible" content="IE=edge">
 <title></title>
 <link href='http://fonts.googleapis.com/css?family=Lato:400,700,900' rel='stylesheet' type='text/css'>
</head>
<body>
</body>
</html>
```

## Data Context

Right now all our messages are identical, that wouldn't make a good chat app. Instead, we need to pass in parameters into the templates which we can then use as variables.

So instead of `{{> message}}`, we write `{{> message text='Message text'}}`, and in our `message` template

```
<template name="message">
 <div class="message">
 <a href="" class="message_profile-pic"></a>
 <a href="" class="message_username">scotch</a>
 <span class="message_timestamp">1:31 AM</span>
 <span class="message_star"></span>
 <span class="message_content">{{text}}</span>
 </div>
</template>
```

This will insert the `text` variable into the template.

```
<div class="message-history">
{{> message text="All these messages"}}
{{> message text="Uses the same template"}}
{{> message text="But have a different data context"}}
{{> message text="It's why these messages are all different!"}}
</div>
```

[![data-context](https://cask.scotch.io/2015/05/data-context.png)](https://cask.scotch.io/2015/05/data-context.png)

## Template Helpers

I hope you realized that listing out each message individually will not work in the long run. Instead, let's create an array holding these messages, and then use the `{{#each}}` block tag of Spacebars to list them out.

To do this, we will use Template Helpers; you can think of them as variables you can use inside your template.

To define a template helper, use the syntax `Template.templateName.helpers`. Since our messages are found inside the `messages` template, we will write:

```
Template.messages.helpers({
 messages: [
 { text: "All these messages" },
 { text: "Uses the same template" },
 { text: "But have a different data context" },
 { text: "It's why these messages are all different!" },
 { text: "Hey!" },
 { text: "What's up man!" },
 { text: "Hello" }
 ]
});
```

And now we can use the `{{#each}}` block tag in our `messages` template

```
<template name="messages">
 <div class="message-history">
 {{#each messages}}
 {{> message text=text}}
 {{/each}}
 </div>
</template>
```

As expected, we get the same results, only now it's much tidier!

[![using-helpers](https://cask.scotch.io/2015/05/using-helpers.png)](https://cask.scotch.io/2015/05/using-helpers.png)

## Template Event Map

The template event maps specifies all the event handlers of the template. In our case, we need to listen in the `footer` template for when the user presses ↵ (Enter / Return), afterwhich we should take what's in the input text box, and add it to our helper array.

Similar to template helpers, you specify the event map using the syntax `Template.name.events`. For the keys in the event map, the first word specifies which events to bind, and the

```
Template.footer.events({
 'keypress input': function(event) {
 if (event.charCode == 13) {
 event.stopPropagation();
 $('.message-history').append('<div class="message"><a href="" class="message_profile-pic"></a><a href="" class="message_username">scotch</a><span class="message_timestamp">1:31 AM</span><span class="message_star"></span><span class="message_content">' + $('.input-box_text').val() + '</span></div>');
 $('.input-box_text').val("");
 return false;
 }
 }
});
```

For now, we will just append some HTML at the end of `<div class="message-history">`, this is in no way ideal! In the next article, we will see how to use collections to make this a lot easier.

## Excited?

This lesson is really just to get you familiar with Meteor and understanding the core architecture of the framework. In the upcoming articles, we will see how we can store our messages inside a database, and how Blaze will live-update our user interface with new messages as they are being added. We will also create different rooms, and allow users to join them using subscriptions and publications! Stay tuned!
