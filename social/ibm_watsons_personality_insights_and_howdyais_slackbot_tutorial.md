# IBM Watson's Personality Insights and Howdy.AI's Slackbot tutorial

[Original URL](https://medium.com/@ash_hathaway/ibm-watson-s-personality-insights-and-howdy-ai-s-slackbot-tutorial-be68da6cfa10#.8wzglosgf)

> Chances are if you work in tech you know what it's like for your work to not be understood by the vast majority of people. Odd acronyms like API, OKR, or CRM, funny words like SCRUM and backlog, and...

Chances are if you work in tech you know what it's like for your work to not be understood by the vast majority of people. Odd acronyms like API, OKR, or CRM, funny words like SCRUM and backlog, and titles like dev evangelist usually take a bit of explaining. I'm pretty confident my entire family has never _actually_ understood what I do. _"You make the internet, sweetie. Good job."_ Thanks, Mom.

And then you know those times when you find yourself amongst friends and you can just let those acronyms fly without skipping a beat? And find humor in jokes about the CLI or bash scripts (I know, right!?). Well, late last October I found that in the first [ATX Bot Talk Meetup](https://medium.com/why-not/announcing-atx-bot-talk-meet-up-96e203b113a8#.edojo579q) hosted by [Howdy.AI](http://howdy.ai/).

It was a darn good meetup: A decent sized crowd, but not so many that social anxiety kicks in; friendly with a few familiar faces; good topics and speakers; reminiscing about good ole' IRC. Pizza. Beer. What more do you need?

[Jeff Kramer](https://twitter.com/jeffk) presented on how to make your own Slackbot using Python. I'm of course a fan of Slack and thought to myself, "Yeah! That looks fun!" And off I went.

Full disclosure--in another life I was a developer. I've always enjoyed it but it has been more than a couple years since I was coding day-to-day. And for me it's not quite like riding a bike. As one friend commented, it's more like riding a bike that has been sitting in the rain for a few years. Yes, that's more like it.

Still, the Slack API is stupid easy to use. IBM Watson's Personality Insights is easy to use, and with [Howdy.AI's botkit](http://howdy.ai/botkit) even old timers like me can get something going pretty quick.

For this walk-through let's find the personality of a channel using natural language processing.

First things first, let's get all of our credentials ready and that handy dandy Slackbot kit using the [docs](https://github.com/xoxco/botkit/blob/master/docs/index.md) here. Make sure you have [Node.js](https://nodejs.org/en/) and [NPM](https://docs.npmjs.com/getting-started/what-is-npm)

![](https://cdn-images-1.medium.com/max/800/1*AOpps1y3oRERBTQGYh2Lqw.png)

Then head over to Slack and get your bot integration going. For this example I'm going to use our IBMWatsonDevs Slack.

Go to Integrations:

![](https://cdn-images-1.medium.com/max/800/1*9vvam3w1Fe_p5fLEUYHsLg.png)

and add your bot to get credentials:

![](https://cdn-images-1.medium.com/max/800/1*E8FmNNt46yotviW0v8rx4w.png)

Grab your API Token. Give your bot a name & icon.

![](https://cdn-images-1.medium.com/max/800/1*Zjg2dmWX7tT8_opafzYDtg.png)

Now let's get Personality Insights setup. All of IBMWatson's APIs are hosted through IBM Bluemix, IBM's PaaS, so our next step is to head over to [Bluemix](https://console.ng.bluemix.net/).

Go ahead & signup:

![](https://cdn-images-1.medium.com/max/800/1*dpDGOQez97sVAOI4x0sHXQ.png)

And then login after you get your IBM ID:

![](https://cdn-images-1.medium.com/max/800/1*O-mP91Y_EcxRWuTHfghEpg.png)

Now we need a space to host our Personality Insights service so we can get our credentials. Let's make a space:

![](https://cdn-images-1.medium.com/max/800/1*dcyi9920TeTsAE9NIXJiQA.png)

And then add a service:

![](https://cdn-images-1.medium.com/max/800/1*Nc9sa9xay1xsAi4FmM-Wtw.png)

Now add Personality Insights:

![](https://cdn-images-1.medium.com/max/800/1*4U6y3gv-FQToMUbTf7MuZg.png)

Now create your space. Be sure to leave the app unbound. Note that Personality Insights has a [few tiers](http://www.ibm.com/smarterplanet/us/en/ibmwatson/developercloud/personality-insights.html) for pricing but is free for the first 100 API calls per month.

![](https://cdn-images-1.medium.com/max/800/1*W8rHDV6NGNoB9XwJzCpAyQ.png)

Now you're able to grab your service credentials. Keep these handy:

![](https://cdn-images-1.medium.com/max/800/1*MHZ5y9AL8EE8Kb_UxifxpA.png)

Alright, now we're almost ready to start cookin. Let's get the IBMWatson [Node.js SDK](https://www.npmjs.com/package/watson-developer-cloud#personality-insights)

![](https://cdn-images-1.medium.com/max/800/1*7lH6EMAzTZs43NBpPEwkzg.png)

So now we have our botkit and Watson ready to go. Let's get those credentials in there.

Find the file hello_world_rtm.js from your botkit. Let's add Personality Insights credentials:

![](https://cdn-images-1.medium.com/max/800/1*2uR1XdadyB1RcODBLN048Q.png)

This snippet is where your Slack token is going to get called:

![](https://cdn-images-1.medium.com/max/800/1*Ak2nQ-cJNbOGu2PCIj1jXA.png)

Now, each Slackbot has to be invited to a channel to use it. So I type in my bot's name and then invite the ibmwatson_bot:

![](https://cdn-images-1.medium.com/max/800/1*jWrZSFxYi1NRKKxdAbsAlg.png)

![](https://cdn-images-1.medium.com/max/800/1*ouQqLvuZfoFb0awZpqvYyg.png)

Remember that Slack token? Let's use it to get our bot online:

![](https://cdn-images-1.medium.com/max/800/1*PtPyFEbfTeTZNLFZdp0TCg.png)

You'll notice in Slack when a bot is online. It changes from this:

![](https://cdn-images-1.medium.com/max/800/1*yVCTujKpEPJg9hbB63b9Jg.png)

To this:

![](https://cdn-images-1.medium.com/max/800/1*9RNuGEJr8j-z-W1IIuJDoA.png)

It's alive! So you should have the RTM API going now:

Now let's actually make stuff happen. Check out this code that has the bot listen for "Hi Watson"

![](https://cdn-images-1.medium.com/max/800/1*FOD866UjGhhpRJqwtQM02A.png)

Let's try it out:

![](https://cdn-images-1.medium.com/max/800/1*2n8KBepFuo_rkgqLKi44zg.png)

Sweeeeet. You should also see some information in your terminal relating to the users, channels, and conversations. Each one gets a unique identifier which you can use to call or access if you want.

Now check out this code that relates to the Personality Insights call:

![](https://cdn-images-1.medium.com/max/800/1*gZjCPo9N45XDOW3A0jzyQA.png)

Now try it in the Slack channel:

![](https://cdn-images-1.medium.com/max/800/1*hBUhLhLNRhCQ-2Y6tl72BQ.png)

Dang! Our channel is not very agreeable, is it?! It's like we're a bunch of developers or something.

You'll see Personality Insights returns in your terminal, too:

![](https://cdn-images-1.medium.com/max/800/1*PPPqr0hWkFrMWMsk0jjXdw.png)

That's it! Now we know the personality of the channel.

If you don't want to keep passing in the token each time you can deploy this back to Bluemix or another PaaS.

The [Howdy.AI Botkit](http://howdy.ai/botkit) has many more features and IBM Watson has a bunch more natural-language processing tools if you're interested. You can also head over to my [Github](https://github.com/ashhath/WatsonPersonalityBot) to see the whole project.

![](https://cdn-images-1.medium.com/max/800/1*zxePVPh7c69NgBP4fO25Xw.png)

Overall great project to get my terminal window open again. It's interesting to only use the Slack interface and command line for a project, so I hope this tutorial was helpful. I hope to work on more starter projects and begin coding more. Thanks again to the help of [Ben](https://twitter.com/benbrown) at Howdy and [Jeff Stylos](https://github.com/jsstylos) at IBM.
