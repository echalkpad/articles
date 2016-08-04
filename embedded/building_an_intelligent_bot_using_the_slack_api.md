# Building an Intelligent Bot Using the Slack API

[Original URL](http://nordicapis.com/building-an-intelligent-bot-using-the-slack-api/)

> Slack is a messaging app that has exploded in popularity among all sorts of teams. One of the things that makes Slack so compelling is that it integrates well with all kinds of other...

**![Building-an-Intelligent-Bot Using-the-Slack-API-nordic-apis](http://nordicapis.com/wp-content/uploads/Building-an-Intelligent-Bot-Using-the-Slack-API-nordic-apis.png)Slack** is a messaging app that has exploded in popularity among all sorts of teams. One of the things that makes Slack so compelling is that it integrates well with all kinds of other applications and services -- a perfect fit within API space.

Slack comes with a slew of standard integrations, but it's also really easy to build your own. Typical Slack integrations range from posting notifications to more sophisticated and interactive bots. In this article, we'll use the **Slack API Node.js library** to build our own bot, and explore **Wit.ai API** and other tools that use natural language processing to analyze and respond to human-inputted text. We'll also talk to some experts in the bot space for added insight. By the end, you'll have an introductory toolset for building your own useful team bot with a dash of personality.

## What is a Bot?

![software-bot-web-nordic-apis-slack-api](http://nordicapis.com/wp-content/uploads/software-bot-web-nordic-apis-slack-api.jpg)A bot is a "software robot." There are bots for all kinds of applications, some of which accept inputs in a conversational format to help automate a variety of tasks for users. Some of the most prominent examples of bots are Apple's [Siri](http://www.apple.com/au/ios/siri/) or Microsoft's [Cortana](http://windows.microsoft.com/en-us/windows-10/getstarted-what-is-cortana). Bots are not a new concept, but they're starting to become mainstream.

Many bots are now being developed for **Slack**. Examples include [Meekan](http://meekan.com/slack/) for scheduling meetings, and [Nestor](https://www.asknestor.me/) for calling you an Uber or finding you a restaurant. There are even bots for playing [poker](https://github.com/CharlieHess/slack-poker-bot), [Jeopardy!](https://github.com/gesteves/trebekbot), and [oldschool adventure games](https://twitter.com/stewart/status/624405238657617920). My experience has been from developing a bot for [getting graphic design tasks done](https://github.com/99designs/tasks-slack-bot).

> "Slack is an operating system for a whole new class of apps."

Bots can enable new ways of working and entirely new classes of applications. They can make your application more useful, more usable, and more natural. Some users even feel more comfortable using a conversational bot than other kinds of interfaces. To operate and integrate with various systems, bots naturally rely on many APIs:

> "Bots are creatures of almost pure API. It is the flesh and blood from which they are created. They speak and listen through messaging APIs, and carry out actions via other APIs."

> --[Ben Brown](https://medium.com/@benbrown/slack-is-the-operating-system-6bae1a6c0291) | [XOXCO](http://xoxco.com/) software design firm

## Getting Started

For this tutorial, we'll be using [Node.js](https://nodejs.org/) to build a bot that integrates with Slack. Make sure to [download and install Node.js](https://nodejs.org/download/) before continuing.

Slack has a variety of APIs that suit various purposes, but the one that is most useful for writing bots is the **Slack RTM** (Real Time Messaging) API.

To write our bot using the Slack RTM API, we'll be using the [official Slack client for Node.js](https://github.com/slackhq/node-slack-client), which can be installed using NPM. [Learn more about NPM](https://docs.npmjs.com/getting-started/what-is-npm).

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-v">install</span>
  <span class="crayon-o">–</span>
  <span class="crayon-e">save </span>
  <span class="crayon-v">slack</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">client</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Setting up a Bot User

Before diving into code, you'll need to create a [new bot user integration](https://my.slack.com/services/new/bot) in your Slack account. Give your bot a name and avatar -- this is a good opportunity to start thinking about the kind of personality you want your bot to have. More on that later!

Copy the generated **API token**, and now let's get started on our bot.

### Basic Structure

The basic structure of our bot will resemble the following. Make sure to paste in your Slack bot's API token.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">SlackClient</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">require</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘slack-client’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">SlackClient</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“PASTE YOUR BOT API TOKEN HERE”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">bot</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">// Track bot user .. for detecting messages by yourself</span>
</p>
  <p>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">on</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘loggedIn’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">user</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">team</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">bot</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">user</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Logged in as ”</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">user</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
</p>
  <p>
  <span class="crayon-o">+</span>
  <span class="crayon-s">" of "</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">team</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">name</span>
  <span class="crayon-o">+</span>
  <span class="crayon-s">“, but not yet connected”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">on</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘open’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘Connected’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">on</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘message’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">user</span>
  <span class="crayon-o">==</span>
  <span class="crayon-v">bot</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">id</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-st">return</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">// Ignore bot’s own messages</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">channel</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getChannelGroupOrDMByID</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘Hello world!’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">// More goes here later..</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">login</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

An important piece of housekeeping here is to detect the bot's own messages to ensure it doesn't reply to itself and get into a loop. This is a good start, but our bot isn't very useful yet. All it does is reply to any message it sees with 'Hello world!'.

### Handling Simple Commands

One of the simplest things a bot can do is understand a set of basic commands. The benefits are that they're simple to parse and will be familiar to users that have used a command line prompt.

This is pretty simple stuff, but there's a few extra details that are worth paying attention to. For demonstration purposes we're using the [99designs Tasks client for Node.js](https://github.com/99designs/tasks-api-node) in this example.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">text</span>
  <span class="crayon-o">==</span>
  <span class="crayon-s">‘create’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">_send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">type</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“typing”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">channel</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">channel</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">api</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">create</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">body</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘Please edit this image’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">urls</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">‘<a href="http://i.imgur.com/YryoTju.jpg’">http://i.imgur.com/YryoTju.jpg’</a></span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">task</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-st">return</span>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘Something went wrong’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Success! Created: ”</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">task</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">elseif</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">text</span>
  <span class="crayon-o">==</span>
  <span class="crayon-s">‘list’</span>
  <span class="crayon-o">||</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">text</span>
  <span class="crayon-o">==</span>
  <span class="crayon-s">‘status’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">slackClient</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">_send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">type</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“typing”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">channel</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">channel</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">api</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">myTasks</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">results</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-st">return</span>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘Something went wrong’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">list</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">results</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">items</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">join</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“\n”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Your Tasks: \n”</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">list</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">elseif</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">text</span>
  <span class="crayon-o">==</span>
  <span class="crayon-s">‘help’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Possible commands are: create, status, help, …”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">else</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Unknown command. Possible commands are: create, status, help, …”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

It's a good idea to be forgiving -- allow synonyms and aliases for commands. It's also helpful to provide useful help text and guidance when users make mistakes. You could take this further and consider detecting and correcting typing mistakes.

![typing-indicator](http://nordicapis.com/wp-content/uploads/typing-indicator.png)Something extra is using Slack's **typing indicator** for responses that take some time. The typing indicator is usually for when a human starts typing to let other human users know that they're responding.

A bot is a computer program and doesn't type messages like a human, so a typing indicator may seem pointless. However, giving users some instant feedback really helps reassure them when the bot is taking some time to respond, such as when making API calls or doing some processing that takes a few seconds. It makes the bot _feel_ human in a subtle way.

It's not currently officially implemented in the [Slack node.js library](https://github.com/slackhq/node-slack-client), but it can be worked around by calling the private `_send` method as seen in the example above. In addition, depending on the type of bot you're writing, it may also be useful to handle file uploads.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">upload</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">channel</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">send</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“This file was uploaded: ”</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">message</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">file</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">url</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Making Responses More Useful (and Better Looking!)

Plain text responses can go a long way, but there are many opportunities to respond in more informative and meaningful ways using [Slack's formatting options](https://slack.zendesk.com/hc/en-us/articles/202288908-Formatting-your-messages).

For example, [Datadog](https://www.datadoghq.com/), a cloud monitoring service, makes excellent use of chart images to show server monitoring statistics:

![](https://api.slack.com/img/api/attachment_example_datadog.png) The [poker](https://github.com/CharlieHess/slack-poker-bot) bot mentioned earlier is able to show poker hands using emoji and clever use of image attachments.

Take a look at the Slack [formatting](https://api.slack.com/docs/formatting) and [attachments](https://api.slack.com/docs/attachments) documentation for more details on what's possible.

Part of what makes bots really interesting is that you can give them some personality.<br>
Ideally, you want your bot to feel like a valuable part of your team and reflect your company's [culture](http://nordicapis.com/fostering-an-internal-culture-of-security/). Making your bot sound like a real person can be easy or difficult depending on how realistic you want it to be.

### Acting Natural

Natural language can open up many possibilities for new types of applications as well as enable users to combine data and services in interesting ways. One of the best examples is [Wolfram Alpha](http://www.wolframalpha.com/), which combines a computation engine and a massive database of knowledge about the world with a natural language user interface.

When it's done as well as this, it feels like magic.

It's also something that can introduce significant complexities and is an open area of research. Thankfully, there are services to help take care of all the hard work. One of my favorites is [Wit](https://wit.ai/).

Wit is a semantic analysis tool designed specifically for developing "bots" and "smart agents". You give Wit an example of a sentence you would like it to understand, and it has the capability to interpret meaning and intent, even if natural language is used.

### Using the Wit API

Install the Wit Javascript library

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">npm </span>
  <span class="crayon-v">install</span>
  <span class="crayon-o">–</span>
  <span class="crayon-e">save </span>
  <span class="crayon-v">node</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">wit</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 | <div class="crayon-pre">
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">wit</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">require</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘node-wit’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">ACCESS_TOKEN</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“YOUR WIT ACCESS TOKEN HERE”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">wit</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">captureTextIntent</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">ACCESS_TOKEN</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“What’s the weather in Melbourne?”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">res</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Response from Wit for text input: ”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Error: ”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">JSON</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">stringify</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">res</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">null</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">" "</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here's an example of what you get back from Wit:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“msg_id”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“17c81127-5c02-4646-9d9d-ba72bccd03b2”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“_text”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“What’s the weather in Melbourne?”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“outcomes”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
</p>
  <p>
  <span class="crayon-s">“_text”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“What’s the weather in Melbourne?”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“intent”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“weather”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“entities”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-s">“location”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">[</span>
</p>
  <p>
  <span class="crayon-s">“suggested”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-t">true</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“value”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Melbourne”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“type”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“value”</span>
</p>
  <p>
  <span class="crayon-s">“confidence”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">1</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The idea is to specify the kinds of "intents" you want your bot to understand. For example, a weather intent is able to understand a variety of natural language expressions related to asking what the weather is like. A home automation "intent" would be able to understand expressions about switching your lights on and off or controlling your air conditioner.

Wit takes care of parsing sentences to work out the intent as well as any entities that were referred to. There are a variety of community [contributed "intents"](https://wit.ai/blog/2015/02/20/new-intent-api) that are a great starting point and can be modified further to suit your needs.

Have a look at the [Wit documentation](https://wit.ai/docs) for more details.

### Avoiding the Uncanny Valley

![](http://nordicapis.com/wp-content/uploads/uncanny-valley-slack-api-intelligent-bot-nordic-apis.jpg) One of the potential downsides of using this kind of sophisticated natural language understanding is falling into the "**Uncanny Valley**". The uncanny valley is a term used to describe when an artificial resemblance -- be it CGI or Cleverbot -- looks, feels or moves almost -- but not exactly-- like a natural being. There's just something slightly off.

![Just not quite human... avoid creeping into the uncanny valley](http://nordicapis.com/wp-content/uploads/uncanny-valley-just-not-quite-human-nordic-apis-slack-api-bot-300x300.jpg) Just not quite human... avoid creeping into the Uncanny Valley

Ironically, a bot that is programmed to act like a cartoon character can feel more natural than something that is smarter and "almost human". There's a strange effect where making a bot too smart can also make it feel unnatural and creepy. This can also lead to frustration when a bot is unable to understand or behaves unpredictably and it's not clear to the user why.

A principle that many bot writers follow to avoid this is the idea of ["being as smart as a puppy"](http://berglondon.com/blog/2010/09/04/b-a-s-a-a-p/).

> "Making smart things that don't try to be too smart and fail, and indeed, by design, make endearing failures in their attempts to learn and improve. Like puppies."

So in some ways it can be better to keep your bot simple and to make it clear the bot isn't human. The goal here isn't to pass the [Turing test](https://en.wikipedia.org/wiki/Turing_test) and trick people into thinking your bot is human -- it's more important for your bot to be a **useful** and **reliable** tool for your team.

[Ben Brown](https://twitter.com/benbrown), of XOXCO, a firm that specializes in creating bots for Slack teams, agrees that making your bot less human can make it less creepy, more relatable, and even comical for your team:

> "Our approach is for our bots to avoid trying to be too human. They will never claim to be a real person, and when confronted with troubling input, they will basically do what R2D2 does when he runs into a staircase – beep, whir and say "I am just a robot, I can't do everything!" This is in large part based on Matt Jones' maxim, and also the Hitchhikers Guide to the Galaxy gag where the "Genuine People Personality" system built into the robots is a hilarious failure."

Even if your bot doesn't pretend to be human, you can still give it loads of personality. Much of the advice for making good conversation in social situations can also be applied to the design of your bot.

A good place to start is to follow [Grice's Maxims for conversation](http://www.sas.upenn.edu/~haroldfs/dravling/grice.html), which are what social scientists use to describe how real people typically behave in conversations.

#### The Jack Principles

[Jellyvision](http://www.jellyvision.com/) is a game development company that set out to make a quiz game with a computer-human interface that was realistic and fun. They developed a set of principles dubbed ["The Jack Principles"](http://demos.jellyvisionlab.com/downloads/The_Jack_Principles.pdf) [PDF], which are aimed to be a practical set of guidelines for designing human-computer interfaces.

The three main principles are:

1. **Maintain Pacing** -- Principles that help a designer maintain the pacing of an interactive conversation interface program.
2. **Create the Illusion of Awareness** -- Principles that help a designer create the illusion that the characters on the screen are actually aware of the person sitting in front of the screen.
3. **Maintain the Illusion of Awareness** -- Principles that a designer must follow to maintain the illusion that the characters are actually aware of the person sitting in front of the screen.

The key quality of this style of human-computer interface is described by Jellyvision as "cinematic and makes you feel like you're really talking to someone".

There's a bit of skill in designing a good bot -- it's very different to the kind of user interface most people are used to. Help by giving your bot some small touches such as a [sense of humour](http://www.nytimes.com/2015/08/13/fashion/siri-tell-me-a-joke-no-a-funny-one.html) and the ability to [take a compliment](http://i.imgur.com/sd3MtZy.png).

## Bots into the Future

As more and more teams embrace group messaging systems like Slack, developing natural language empowered software bots to automate tasks seems like an eventual next evolution, and perhaps a key productivity advantage. According to Brown, bots will only become more and more ubiquitous:

> "...bots will grow to take over a lot of features that apps and websites currently provide. Bots are a great way to delivery a wide variety of content and features on small screens. I think in the future, we'll all have personal bots as well as work bots that help us with our lives, automating common tasks and providing an interface to the exploding number of connected devices."

## Key Takeaways

1. Bots enable new kinds of applications
2. Don't overthink it. Keep it simple!
3. Natural language can make your bot more powerful--but use with care.

## Resources

A list of the helpful links mentioned throughout this article and more to help you get started:

_[Disclosure: Dennis Hotson is an engineer at 99designs, whose API is referenced in an above code sample]_
