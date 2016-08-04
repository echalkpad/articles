# Slack + SuperScript -- Rise of the Bots

[Original URL](https://medium.com/@rob_ellis/slack-superscript-rise-of-the-bots-bba8506a043c#.5zax6gvkk)

> This article has been updated to work with SuperScript 0.6.8\. If you are interested into Slack+Bots. The SuperScript Editor comes with built-in Slack Support.Two days after I announced...

```
This article has been updated to work with SuperScript 0.6.8\. If you are interested into Slack+Bots. The SuperScript Editor comes with built-in Slack Support.
```

Two days after I announced [SuperScript](https://medium.com/@rob_ellis/superscript-ce40e9720bef) -- A bot engine for creating human like chat bots. [Slack announces their new RealTime API](http://slackhq.com/post/104688116560/rtm-api) to enable Bot Developers.

This Posts will walk though creating a bot using these two pieces of software and jumps into some of the more interesting corners in SuperScript.

Canadians are oddly fascinated by the weather, lets create a bot that tells us the weather when someone asks.

To start, we will need to install superscript from NPM and init a new bot, this has been updated to be even easer.

```
$ npm install superscript -g
$ bot-init slackbot --client slack
$ cd slackbot 
$ npm install
```

First we install superscript globally from NPM, this allows us to create bots at will using the init command. We then init a new bot called `slackbot` and tell it we want to use the slack client, rather than the telnet client. Finally we install the bot dependancies.

Next lets head over to Slack and get a token for our bot to use. Keep in mind that Bots will take up one integration slot and you may need to upgrade your plan if you are at your limit.

You need to be signed in to view the page, visit <https://slack.com/> and than navigate to integrations, DIY Integrations & Customizations, Bots.

Finally you will want to give your bot a name, and generate a auth token. Keep this token save, we will need it shortly.

Back in your terminal, lets open the bot folder with your favourite text editor.

We will need to paste in the token to allow our new bot to connect to our slack team and hang out. By default, it will be in the general channel, but you can invite him to any other channel. Open server.js and paste in your token where it says:

```
var token = "...";
```

The provided boilerplate code has a little etiquette built in, our bot will reply when being spoken to directly or in a DM. We shouldn't need to touch anything.

So at this point we really have a working slack bot, we just need to provide it some clever things to say, and you could jump into the [scripting guide](http://superscriptjs.com/documentation/scripting) and do that. But lets forge on and learn about some other neat things.

Our bot will always start off in the _random_ topic. Lets create a general greeting.

Open the main.ss in your topic folder and lets add a trigger for weather.

```
?:WH articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii weather *
- I'm not near a window.
```

Okay, now when ever our bot sees "weather" in a question sentence, it will reply with "I'm not near a window."

We can tests this by compiling the topics and running the bot.

```
$ parse 
$ node server.js
```

Parse will parse all the conversation in the topic folder down to a data.json file ready for the slack server to read.

Okay, lets call out to [OpenWeather API](http://openweathermap.org/api) to fetch some real weather data, to do this we need to move our reply to a plugin.

First we will change our gambit (trigger and reply) to this:

```
?:WH articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii weather *~2 in *
- ^weather()
```

Next we need to create a plugin called getWeather.

Create a new file called weather.js and save it in your plugin folder. We will use the following contents.

This Gist will export a weather function with a callback that will inject the text back into superscripts reply. So if there is an error in the request or we don't have a name in the message object we just reply with "I'm not near a window."

Before this will work, we need to add the request module to our package.json

```
$ npm install request —save
```

Okay, fire up the bot, and lets see what happens when we say:

"What is the weather like in Vancouver?"

The Open Weather API also exposes temperature data, with the exact same call -- try to create a temperature function that will return the temperature if asked. If you bot is American, you might want to change the units from metric to imperial.

So from here, the next step is to create a triple store of all the cities we know about and use that data to drive the API call for the location. We can be much more clever if asked "What is the weather like in the kitchen? I will save that for the next tutorial.

I hope you enjoyed this walk though. Please leave comments on [twitter](http://twitter.com/superscriptjs), or create an issue in [Github](https://github.com/silentrob/superscript).
