# Make a Self-Training, NLP-Driven Slack Bot

[Original URL](http://blog.templeton.host/self-training-nlp-enabled-slack-bot-tutorial/)

> Slack has taken the world by storm, growing to be one of the hottest companies around seemingly overnight. Originally just a "messaging app for teams" (their words), Slack now touts a vibrant...

Slack has taken the world by storm, growing to be one of the hottest companies around seemingly overnight. Originally just a "messaging app for teams" (their words), Slack now touts a vibrant ecosystem of extensions and integrations to make teams work better.

Let's take a peek at what's going on with Slack integrations, and then I'll show you how to make a Slack Bot that understands flexible English with an approachable Natural Language Processing (NLP) system, all in node.js.

(For the impatient: [code](https://github.com/andrew-templeton/bottie))

## Incoming, Outgoing, RTM, Oh My

Vendors and individual teams write integrations on Slack because the integrations take only a short time to write, whether they be webhook-based or leverage the more advanced Real Time Messaging (RTM) API. These integrations center around notifications, collaboration and commands - for example, a team might want a Slack integration that sends a message to a particular chat room when a customer sends their sales email address a message.

More recently, Slack began touting the platform's support for bots, which use the RTM API to present themselves similar to normal, human users. Bots bring the power of software right into the chatrooms that business users are increasingly spending their time in. This readily-available presence within the chatrooms make bots a convenient way to automate many business functions, or simply aid teams to work more effectively.

## Howdy.ai and Botkit <3

If you want to make a Slack bot, you need three things:

1. Connectivity to Slack
2. Input interpretation of user commands
3. Integration logic to back bot actions

[Botkit](https://github.com/howdyai/botkit), by [Howdy.ai](https://howdy.ai), is an open-source toolkit which fully handles the Slack connectivity portion we need, and makes basic input interpretation a little easier. It provides an easy way to connect to Slack via the RTM API, supports multi-message conversations, storage adapters, and lets you define simple input interpretation handlers using regular expressions.

The problem with using simple regular expressions for your bot is that regular expressions are VERY particular, and your users on your Slack channel are humans. If the whole purpose of writing a bot is to make work easier, then we should not need to train our users to work with specific bot commands - then we have turned our Slack bot into a command line prompt.

We want to leverage the excellent connectivity tools Howdy.ai has given us with Botkit, with a more flexible system for input interpretation, which understands English.

## NLP + Botkit = Magic

Natural Language Processing (NLP) allows us to build this flexible English interpretation we want. Put (very) simply, NLP aims to help computers understand human language. While NLP is a deep, complex, and still-developing field, we can use a small piece of NLP to teach our bot to understand which commands we want to use even when we use flexible English.

We will use simple document classification, a portion of NLP, to make bots understand which command we are trying to invoke when we talk to them in plain English. A "document" here is a string of text, and "classification" means we will try to decide to what group said string of text belongs. When working with a bot, our bot will use NLP on our message documents, and classify which command we are trying to invoke.

All we need to do is set up a system which plugs some example sentences for the bot to learn, assign each sentence a command (its class), put these labeled sentences into a classifier, and connect the classifier to Botkit.

## Show Me The Code!

Let's get into it. We're going to make our bot trainable on-the-fly, so we can teach it new sentence types without writing any code. If we want the sentences to invoke actions on other systems, we will have to write that logic later, but this portion will let us author new interpretation logic directly in Slack.

This assumes you have node.js installed and have a Slack login for which you are an administrator, as you need to be able to authorize your bot to communicate with Slack.

### Get Your Slack Bot Token

You'll need to go to [Slack's Custom Integrations panel](https://my.slack.com/apps/build/custom-integration) and click on the Bots link:

![Slack Custom Integrations Panel](http://the.cdn.bytuple.com/blog-templeton-host/slack-custom-integrations-panel.png)

Then, name your bot:

![Name Your Bot](http://the.cdn.bytuple.com/blog-templeton-host/slack-bot-name.png)

You'll land on the new bot's integration page. You need to copy the **API Token** value here into your clipboard.

![Get Bot Token](http://the.cdn.bytuple.com/blog-templeton-host/slack-bot-token.png)

Save this value, as we need to give it to Bottie (or whatever you called yours) in a later step.

### Get the Bottie Base Code

I set up a [GitHub repo with the code for Bottie](https://github.com/andrew-templeton/bottie) so you can follow along. Let's clone it onto your machine:

```
$ git clone https://github.com/andrew-templeton/bottie;
```

After the repository finishes cloning, you'll need to run `npm install` to install dependencies: [`natural`](https://github.com/NaturalNode/natural) for our NLP classifiers and [`botkit`](https://github.com/howdyai/botkit) for the connectivity layer.

```
$ npm install;
```

Once the dependencies finish installing, we need to give the environment the proper Slack API Token that we copied earlier (<COPIED_TOKEN> here):

```
$ export SLACK_TOKEN='<COPIED_TOKEN>';
```

Now, we have Bottie's base downloaded and configured to connect to our Slack installation.

You can start Bottie up by running:

```
$ node index
```

That's it! Bottie should be up and running in Slack. You can message Bottie now by adding it to your Direct Messages, using `/invite @bottie` to invite Bottie to a channel, and sending messages with `@bottie` in them. If you used a different name for the bot when configuring it in the Slack Integrations panel, use that name when addressing the bot.

### Open Bottie's Brain

Bottie uses classifiers and example sentences to understand what we are saying, so we should take a look at what Bottie understands already. Open your the `bottie` directory you cloned down with your favorite text editor and navigate to `./builtins.js`.

![Bottie Built In Commands](http://the.cdn.bytuple.com/blog-templeton-host/bottie-builtins.png)

Right now, Bottie only understands when someone asks Bottie to tell a joke, and when people ask Bottie to echo back what is said to Bottie. We will teach Bottie how to understand other commands soon.

The way that these commands work is by adding the array of phrases on each key into a Logistic Regression Classifier (LRC) as a new document labeled by the key and then training the classifier when we have added all documents. The LRC is borrowed from the `natural` package I mentioned earlier and is an industry-standard document classifier we use in NLP. The math behind the LRC is fairly complex, but for our simple use cases, we can merrily use the LRC without worrying about the details too much.

I have named this piece of the code as "Brain", which you can find in [`./src/brain.js`](https://github.com/andrew-templeton/bottie/blob/44806d463206a1bbdf3e97b1e46f65e7501b710b/src/brain.js). Open it up in your text editor and you should see something like this:

![Bottie Brain](http://the.cdn.bytuple.com/blog-templeton-host/bottie-brain.png)

In the actual file, you will see there's a bit more code to handle the execution of the code Bottie will delegate to when it understands commands, but these 30 lines or so are the magic that makes the system work and understand English. You'll want to give Bottie as many examples as possible, because LRCs work much, much better the more sentence structures and synonyms you feed into them.

#### Bottie's Ears

The Brain file is interesting, but we should also take a look at Bottie's "Ears", which make it connect and listen (surprise) to a Slack installation, using `botkit`. Open [`./src/ears.js`](https://github.com/andrew-templeton/bottie/blob/44806d463206a1bbdf3e97b1e46f65e7501b710b/src/ears.js) in your text editor, or click the link, and you'll see something like:

![](http://the.cdn.bytuple.com/blog-templeton-host/bottie-ears.png)

This is a very simple wrapper around `botkit`'s already fairly clean API.

- The `BotKit.slackbot` function call does _not_ reference a token or a specific installation, so we could theoretically make Bottie listen to more than one slack installation, simply by making a second `new Ears(anotherToken).listen()` call.
- The `this.scopes` object tells Bottie if it should listen and try to interpret absolutely everything, or just listen and respond when actually addressed. I have configured Bottie to only respond when addressed here.
- The `.startRTM` call is the magic method that actually connects Bottie to a Slack installation, and the `Bot.hears` call is what we use to listen for different patterns written to Bottie.

#### Training Bottie

Now, not only can we say flexible sentences to Bottie, but we should also be able to train Bottie new language patterns without writing any code. This is enabled via a special command, `TRAINING TIME!!!`. When we want to teach Bottie a new skill in Slack, this is what we should do:

![Training Bottie](http://the.cdn.bytuple.com/blog-templeton-host/bottie-training.png)

When I'm finished training Bottie, I should put **done** on a line by itself, wait for Bottie to tell me it's done thinking, then try it out:

![Finished Training Bottie](http://the.cdn.bytuple.com/blog-templeton-host/bottie-training-done.png)

This is great! It means that non-technical users can suggest features and test out bot skill invocations without needing to know NLP or how to code.

#### How Bottie Trains Itself

What's really going on here is that Bottie is using `Ears`/`botkit` to start a conversation and ask for phrase name plus a set of phrase examples, then using `Brain`/`natural` to train the Brain LRC on-the-fly. Bottie also serializes the new phrase examples and empty skill file onto disk so it remains on next load.

The method for accomplishing this is fairly simple - we tell `Ears` to listen specifically for the magic phrase `TRAINING TIME!!!` [on these lines in `index.js`](https://github.com/andrew-templeton/bottie/blob/44806d463206a1bbdf3e97b1e46f65e7501b710b/index.js#L37-L40), and [invoke a `Training` module in `./src/training.js`](https://github.com/andrew-templeton/bottie/blob/44806d463206a1bbdf3e97b1e46f65e7501b710b/src/train.js). The `Training` module then uses `speech.startConversation` provided by `botkit` to begin a multi-step conversation. We listen for `done` by itself in Slack to signal the user is done sending sample inputs, then add all the phrases to the `Brain`, then tell `Brain` to `think` / train the internal LRC driving Bottie. There is some additional logic to persist the information to disk, which is standard JSON file manipulation.

#### Putting It All Together

Now that we have reviewed `Brain`, `Ears`, and `Train`, the final step of integrating these pieces is incredibly simple. Navigate to [`./index.js`](https://github.com/andrew-templeton/bottie/blob/44806d463206a1bbdf3e97b1e46f65e7501b710b/index.js#L29-L52) lines 29 through 52 in your text editor or by clicking the link, and you should see the main logic body:

![Bottie Main Logic](http://the.cdn.bytuple.com/blog-templeton-host/bottie-main.png)

On lines 31 and 32, we are simply running a `Teach` function, which is a wrapper around the `natural` library's `LogisticRegressionClassifier#addDocument` method. Once we have added all of our phrase sets, we tell the `Brain` to think, which is a wrapper on `LogisticRegressionClassifier#train`.

We then make the `Ears` use `botkit` to `listen` on our Slack installation and `hear` two patterns. While `botkit` normally lets you listen to patterns, we only want to listen to one specific pattern, the special `TRAINING TIME!!!` command, then pass all other phrases to the second handler, in which we use the Bottie `Brain` to `interpret` messages and perhaps `invoke` skills associated with phrases. `interpret` runs both `LogisticRegressionClassifier#getClassifications` to pull raw phrase grouping probabilities, and `LogisticRegressionClassifier#classify` to tell us which is the most likely phrase group overall.

## Wrapping Up

We now know how to provision a Slack Bot and get a token for integration, connect to Slack via the RTM API using [Howdy.ai](https://howdy.ai)'s handy `botkit` library, and use basic NLP techniques in JavaScript with the open source `natural` toolkit. We also saw how to make a bot train new phrasing on the fly, as well as make our bot write new code stubs for future development.

Interested in Slack bot development, AI, Machine Learning, or NLP? Have feedback for me on my code, writing, or topics? Tweet me [@ayetempleton](https://twitter.com/ayetempleton) - I'd love to hear from you!
