# Getting Started with Slack Bots

[Original URL](http://www.sitepoint.com/getting-started-slack-bots/)

> Slack is a popular tool for encouraging better communication among team members. It comes with a slew of helpful features and services including third-party integrations and webhook support. Slack...

[Slack](https://slack.com/) is a popular tool for encouraging better communication among team members. It comes with a slew of helpful features and services including third-party integrations and webhook support. Slack webhooks can be fun and useful, and they're easy to set up. In this tutorial we'll set up a simple server using [Node.js](http://nodejs.org/) and [Heroku](https://www.heroku.com/) that will respond to your team's messages from Slack as a "bot." Familiarity with JavaScript and Git will be helpful. If you haven't used Git before, take a look at [Git for Beginners](http://www.sitepoint.com/git-for-beginners/) to get started. Access to a Slack account is also assumed, but don't worry – they're free!

## Creating a New Slack Integration

From the Slack web interface, open the menu in the top left corner next to your team's name and select [Configure Integrations](http://slack.com/services/new). Here, you can see a dashboard with all the integrations available to you. Scroll to the very bottom under **DIY Integrations & Customizations**, and add an **Outgoing WebHook**. Read the short description and then click **Add Outgoing WebHooks Integration**.

![DIY Integrations & Customizations](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/12/1418788421slack_intro-diy_integrations.png)

You will see the Outgoing WebHook configuration and an example of what the request and response payloads should look like. Our first bot will simply greet the user when called. To do this, we will allow the hook to listen for the Trigger Word "hello" on any channel. We don't know the URL it will be posting to yet, so we will come back to this page later.

## Creating a Web Server

We'll be using [Express 4](http://expressjs.com/) on Node.js to build a simple web server.

Create a new directory for your app, and run `npm init` to generate your `package.json` file. In your `package.json` dependencies, include `"express": "^4.x.x"` and `"body-parser": "^1.x.x"`. The `body-parser` package will be used to parse the payload sent from Slack. Create the server file named `app.js`. Here, we will `require` the necessary modules, add the body parser middleware, error handler, and a test route. Then, we tell the server to start listening.

```
var express = require('express');
var bodyParser = require('body-parser');

var app = express();
var port = process.env.PORT || 3000;

// body parser middleware
app.use(bodyParser.urlencoded({ extended: true }));

// test route
app.get('/', function (req, res) { res.status(200).send('Hello world!') });

// error handler
app.use(function (err, req, res, next) {
 console.error(err.stack);
 res.status(400).send(err.message);
});

app.listen(port, function () {
 console.log('Slack bot listening on port ' + port);
});
```

Run the server with `node app` and verify that visiting `http://localhost:3000` responds with "Hello world!".

## Writing the Bot Logic

Create a file named `hellobot.js`

This module will be where our bot logic lives. It will include one exported function that returns a message to Slack with status code `200`. We'll start by parsing the data that Slack sends to our server, and then we'll respond with a properly formatted payload of our own. To do this, let's revisit our Outgoing WebHook under [Configured Integrations](https://slack.com/services). The **Outgoing Data** example shows us all the info Slack sends when an Outoing WebHook is triggered.

![Outgoing WebHook Data Example](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/12/1418788418slack_intro-outgoing_data.png)

You can see that some of the data can be useful for authentication or routing, but for now we're just going to focus on the `user_name` property. Using this value, we can send a personalized greeting back to the user.

```
module.exports = function (req, res, next) {
 var userName = req.body.user_name;
 var botPayload = {
 text : 'Hello, ' + userName + '!'
 };

 // avoid infinite loop
 if (userName !== 'slackbot') {
 return res.status(200).json(botPayload);
 } else {
 return res.status(200).end();
 }
}
```

Here we create our `botPayload` object. The only necessary property is `botPayload.text`, which defines what our bot will display when we respond. Add the `text` property with a greeting to the user, and respond with JSON and a status code of `200`. Notice that our bot's response is our Outgoing WebHook's trigger word! To avoid an infinite loop, we can check the `user_name` of the body. All hooks post as "slackbot", even if the name appears differently in chat. We can use this fact to keep our hook from responding to itself.

Back in `app.js`, require the bot module at the top.

```
var hellobot = require('./hellobot');
```

Then, add a route that listens for a `POST` to `/hello`.

```
app.post('/hello', hellobot);
```

Let's do a quick test. Run the app again using `node app`, and in another terminal window use `curl` to make sure the app responds as we expect it to. (Note: since our bot only looks for one value here, there's no need to include the entire Slack payload in our test).

```
curl -X POST --data "user_name=foobar" http://localhost:3000/hello
```

If our app works, we should see a response that looks like this:

```
{"text":"Hello, foobar!"}
```

Now, it's time to put our bot online.

## Deploying the App on Heroku

[Heroku](https://www.heroku.com/) is a convenient service that helps you get a web server online for free in very little time. Deployments are made by pushing your code to their git server where it is automatically deployed and run. Create an account on Heroku or log in if you already have an account.

Once you've logged in, you'll see your Heroku dashboard. Click the `+` in the top right to create a new app. Follow the instructions provided to log in to Heroku on your computer. Deployment is easy, but there is a lot to know the first time you deploy an app. Make sure you follow [this guide](https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction) when you're getting your app set up. We'll do some double checks along the way, but there's a lot of good info in there that's outside the scope of this article.

Before we can deploy our bot, we'll need to tell Heroku how to run the app. This is done by creating a [Procfile](https://devcenter.heroku.com/articles/getting-started-with-nodejs#define-a-procfile). Your Procfile only needs a single line:

```
web: node app
```

Once you you've done this, you can commit your changes and push to `heroku`. Heroku automatically installs the node modules, and you'll see the results of your deployment. If everything went smoothly, you'll see a link to your app at the bottom. Copy that URL, and then visit your Outgoing WebHook's configuration page once again. Add the URL you copied to the **URL(s)** input field with the `/hello` route appended and modify the name and icon of the hook if you'd like.

![Outgoing WebHook URL(s)](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/12/1418788416slack_intro-outgoing_urls.png)

There's one more step to get the bot online: we need to give it a [Dyno](https://devcenter.heroku.com/articles/getting-started-with-nodejs#scale-the-app). In the terminal, enter:

```
heroku ps:scale web=1
```

This tells Heroku to give your app one web worker. Careful; your app only gets one free dyno per month!

Your bot should be online now, so go back into Slack and test it out!

![Hellobot Response](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/12/1418788420slack_intro-hellobot.png)

If you didn't get a response, go back and check your code. You can use the terminal command `heroku logs` to see details about the requests as well as any `console.log()` statements you've added.

Not too difficult, was it? We successfully set up a webhook in Slack, deployed our app to Heroku, and got the two to talk to each other! A bit more work than a simple "hello world" program, but much more rewarding. Now that we have our server running, we can easily add more hooks and responses. Hellobot is pretty friendly, but it's not that useful. In the next section we'll use Slack's other webhook types to create a dice roll chat command.

## DiceBot – Using Slack Commands and Incoming WebHooks

In the last section, we used an Outgoing WebHook that listened to chat for a trigger word, sent a message to our server, and displayed the response. In this section, we'll use two other tools for creating custom integrations: **Slash Commands** and **Incoming WebHooks**. Visit the [Configure Integrations](http://slack.com/services/new) page and take a look at the other options under **DIY Integrations & Customizations**. Let's start by adding the **Slash Commands** integration. Read the short description, set the command to "/roll", and add the integration. The next page shows the configuration. You can see that the Outgoing Data looks similar to the Outgoing WebHook we configured earlier. We'll use the app we created in the last section for this section, so we can use the same URL, but this time we'll `POST` to the route `/roll`.

Notice that the Slash Command integration can respond to the user, but only in private. What use is a dice roll if only you can see the dice? Slack suggests using an Incoming WebHook in tandem with Slash Commands to send messages to chat. Go back to the [Configure Integrations](http://slack.com/services/new) page and add an **Incoming WebHook**. Read the description, select a channel, and add the hook. Don't worry about what channel you picked; we can specify the channel in our bot's response. Because we can choose the channel in our app, it means that we can also use this integration for any other bots we create. The Incoming WebHook doesn't have much in the way of configuration, but it does have a lot of important information for constructing our bot's response payloads. The most important part is the **Webhook URL**. We'll be `POST`ing to this from our app in order to send messages to Slack.

Since we've pointed our Slash Command at the route `/roll`, we can add that route to our server alongside of hellobot. We'll be using a secret URL for our incoming hook, so it's a good idea to keep it hidden in case you want to make your code public. A good way to do this is to use Heroku's [config vars](https://devcenter.heroku.com/articles/getting-started-with-nodejs#define-config-vars).

Let's also add `request` to our package to use with the incoming hook:

```
npm install --save request
```

Now we'll add `dicebot.js`. Dicebot will export one function, but we'll need two additional functions: one to `POST` to the Incoming WebHook, and one to roll the dice.

```
var request = require('request');

module.exports = function (req, res, next) {
};

function roll () {
}

function send () {
}
```

As many tabletop gamers know, there are many more types of dice than just six-sided dice. Other dice have four sides, eight sides, ten sides, twenty sides, and more! A special notation is used when rolling these dice: `<number of dice>d<sides of dice>`. So, rolling two six-sided dice is notated as "2d6″. We can let users roll multiple dice using this notation, but we'll have to check for correct input from the user.

Let's start by initializing some variables and parsing the input. We'll set some default values in case the user wants to omit input.

```
module.exports = function (req, res, next) {
 // default roll is 2d6
 var matches;
 var times = 2;
 var die = 6;
 var rolls = [];
 var total = 0;
 var botPayload = {};

 if (req.body.text) {
 // parse roll type if specified
 matches = req.body.text.match(/^(\d{1,2})d(\d{1,2})$/);

 if (matches && matches[1] && matches[2]) {
 times = matches[1];
 die = matches[2];
 } else {
 // send error message back to user if input is bad
 return res.status(200).send('<number>d<sides>');
 }
 } 
}
```

The regular expression above is pretty reliable for parsing dice rolls and limits the values to two-digit numbers only so the user can't go crazy. If the user messed up their syntax, we send back a hint.

Rolling the dice with the user input is easy enough. See [this thread](http://stackoverflow.com/a/1527820) for an explanation of how a random roll works. For truly random rolls you could contact the [random.org API](https://api.random.org/json-rpc/1/). Let's write the roll function.

```
function roll (min, max) {
 return Math.floor(Math.random() articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii (max - min + 1) + min);
}
```

Then, in our main function we'll make some rolls and build a message to respond with. Let's include the name of the roller so other users can see who used the `/roll` command.

```
// roll dice and sum
for (var i = 0; i < times; i++) {
 var currentRoll = roll(1, die);
 rolls.push(currentRoll);
 total += currentRoll;
}

// write response message and add to payload
botPayload.text = req.body.user_name + ' rolled ' + times + 'd' + die + ':\n' +
 rolls.join(' + ') + ' = *' + total + '*';
```

We added the message to the `botPayload.text` property with some Slack formatting around `total` to make it bold. We can also configure the name, icon, and channel using the response payload. We'll set the channel based on the channel the user issues the roll command from. Slack suggests using the name of the channel in the payload, but that means we'd have to prepend it with "#". Instead, we can use the channel ID, which will allow us to post in public channels as well as private groups. For the icon, a convenient die emoji exists, though you could provide a better image using `icon_url`.

```
botPayload.username = 'dicebot';
botPayload.channel = req.body.channel_id;
botPayload.icon_emoji = ':game_die:';
```

The payload looks good. Let's write the `send` function. I've chosen to store only the path tokens in Heroku's config vars. Make sure to send the body as a string of JSON.

```
function send (payload, callback) {
 var path = process.env.INCOMING_WEBHOOK_PATH;
 var uri = 'https://hooks.slack.com/services' + path;

 request({
 uri: uri,
 method: 'POST',
 body: JSON.stringify(payload)
 }, function (error, response, body) {
 if (error) {
 return callback(error);
 }

 callback(null, response.statusCode, body);
 });
}
```

In our main function, we'll initiate the send request. If it works, we can respond to the Slash Command with an empty `200`. If not, we can use the values from our `send()` callback to build an error. This will trigger the error handler in `app.js` and send back `400`, which will notify the user of the error.

```
// send dice roll
send(botPayload, function (error, status, body) {
 if (error) {
 return next(error);
 } else if (status !== 200) {
 // inform user that our Incoming WebHook failed
 return next(new Error('Incoming WebHook: ' + status + ' ' + body));
 } else {
 return res.status(200).end();
 }
});
```

Let's run our server and `curl` it again. This time, we'll need to include the `channel_id`. You can use the [Slack API tester](https://api.slack.com/methods/channels.list/test) to find the correct `channel_id`. Since the Incoming WebHook has already been set up, it should hear our server call it. We'll also need to include the Incoming WebHook path in our environment since the app isn't on Heroku yet.

```
INCOMING_WEBHOOK_PATH=/your/path/tokens node app

curl -X POST --data "user_name=foobar&channel_id=C0123ABCD&text=4d6" http://localhost:3000/roll`
```

![Dicebot Response](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/12/1418788423slack_intro-dicebot.png)

Looks good! Let's deploy it.

### Redeploying to Heroku

Since we used Heroku's config vars to store our webhook's token, don't forget to set it in the app's **Settings** menu.

![Heroku Config Vars](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/12/1418788414slack_intro-heroku_configvars.png)

Then use git to add the new files, commit your changes, and push to your `heroku` remote. Heroku will install node modules and start the server. Before we test it, let's start watching the logs in real time:<br>
`heroku logs -t`

With our hooks all set up, our roll command should be ready to go. Try out a few rolls! If something didn't work, you can use the Heroku logs to debug. To see the completed app in its entirety, check out this [GitHub repo](https://github.com/jsprodotcom/getting-started-with-slack-bots).

### Continuing Development

These two bots demonstrate all the tools you need to start writing your own chat bots and integrations. You can integrate many services into bots, or you can try to make a convincing conversation partner. APIs and node modules exist for you to achieve both of these goals.

Some other ideas for bots:

- a bot that uses the GitHub API to show you open pull requests
- a bot that uses the Yelp API to help your team decide on lunch
- a bot that does Google searches for you
- a bot that talks to you only with buzzwords and jargon

If you come up with good ideas, share your code! Bots are fun to make, and they've never been easier to create.
