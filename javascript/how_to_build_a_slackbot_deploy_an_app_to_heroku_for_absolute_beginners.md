# How to Build A Slackbot + Deploy an App to Heroku for Absolute Beginners

[Original URL](http://blog.npmjs.org/post/128237577345/how-to-build-a-slackbot-deploy-an-app-to-heroku)

> Back in ye olde days of the internet, there was IRC (Internet Relay Chat), a chat network with channels and servers for every microcosm in the world. Folks would make these little bots or...

Back in ye olde days of the internet, there was IRC (Internet Relay Chat), a chat network with channels and servers for every microcosm in the world. Folks would make these little bots or services -- they'd greet you when you joined a channel, played trivia, Uno and other chat-based games with you. Unfortunately, if you wanted to make one yourself, you likely needed some knowledge of Perl, client-server architecture and a handful of Unix commands.

Today, most will opt for a more aesthetic alternative to IRC. Namely, Slack -- a chat-based team communication tool. The best part? You can create a Slackbot with substantially fewer roadblocks.

I'll try not to make assumptions on the technologies you may or may not have on your machine here. So let's go ahead and start from scratch. If you already have any or all of following downloaded, you're ahead of the game!

## **Setup**

1. Install Node.js and npm

> **Mac**

> Go to nodejs.org, Click 'install', and run through the install process.

> **Ubuntu**

> You should be able to use the following:

> ```
> curl -sL https://deb.nodesource.com/setup_0.10 | sudo -E bash -
> sudo apt-get install -y nodejscurl -sL https://deb.nodesource.com/setup_0.10 | sudo -E bash — sudo apt-get install -y nodejs
> ```

> More installation help at [https://github.com/nodesource/distributions#deb](https://medium.com/p/7347f99673ed/edit)

> **Windows**

> Go ahead the download [the Windows binary](http://nodejs.org/download/)

1. Create a Github account (<https://github.com/>) and download git (<http://git-scm.com/download>)

2. Clone this repository <https://github.com/ceejbot/LOUDBOT-SLACK> by using the following command in your terminal:

  git clone git@github.com:ceejbot/LOUDBOT-SLACK.git

3. Create an account and download Heroku on your machine<https://toolbelt.heroku.com/>

Pheew. Now that we have all that installed, on with the tutorial!

## **Add Slack Tokens to Our App**

1. Go into your Slack group, click on the caret and then "Configure Integrations" in the dropdown

![image](http://40.media.tumblr.com/4da4b3054d0cd91b7bce41649b9964df/tumblr_inline_nu31m5ndgi1tbywuh_500.png)

1. Under 'All Integrations' → 'DIY Integrations & Customizations', click on 'Outgoing WebHooks'

![image](http://41.media.tumblr.com/994db7b445cf3500d01148340662631a/tumblr_inline_nu31n35dqx1tbywuh_500.png)

1. The next screen should look like the following. Go ahead and click on 'Add Outgoing WebHooks Integration'

![image](http://36.media.tumblr.com/3f37d035bcfcaa49eda867cc9a62ec24/tumblr_inline_nu31nw80JB1tbywuh_500.png)

1. Set the channel to the specific channel you would like your slackbot to be active on, and copy the corresponding token (Rest assured, we'll be able to add our slackbot on multiple channels. We'll get to that in a bit)

![image](http://40.media.tumblr.com/f578fa4c5d540daff390a9310b77fe4b/tumblr_inline_nu31oy3Spt1tbywuh_500.png)

1. Open a terminal and navigate to the directory you cloned and use the following command to create your own file to store environment variables. You should note that your .gitignore includes this .env file.

  cat .env.example > .env

2. Next, using a text editor of your choice, open the loudbot directory

3. Open .env and replace the contents of TOKENS with the token you copied from slack. Now I'd promised you could add loudbot to multiple channels. To do this, repeat the above steps to add outgoing webhook integrations for as many channels are you would like. Just remember to copy the corresponding tokens into the TOKENS list in .env.

4. To complete the SLACK_TOKEN field, we'll have to revisit the Slack 'Configure Integrations' page. Under 'DIY Integrations & Customizations', there should be a service called 'Bots'

![image](http://41.media.tumblr.com/cde204d9b08fb54d80ee9d67902e9a91/tumblr_inline_nu31q16oR51tbywuh_500.png)

1. Create a new Bot, name it 'LOUDBOT' and copy the 'API TOKEN' under Integration Settings into the SLACK_TOKEN field in your .env file.

## **Deploy Our Slackbot to Heroku**

1.Next we need put loudbot on a server so it can constantly be listening and responding to our Slack messages.

1. Create a file entitled 'Procfile' without a file extension. This is the file that tells Heroku what commands to use to run your application. The contents of the file are a single line that should be the following:

![image](http://40.media.tumblr.com/690db322baf2c1857b4d481a234d255c/tumblr_inline_nu31rsdoVJ1tbywuh_500.png)

1. Finally, in your terminal, within your loudbot repository, run the following Heroku commands.

  heroku create

This will create a new heroku app (note you can only have a max of 5 heroku apps on the free plan). Git remotes are references to remote repositories and this command will also create a git remote you can reference as 'heroku' on the command line.

```
git add

git commit -m "my commit message"
```

These are git commands for that'll help track your changes to the app. They will commit your changes to your local directory, in preparation for deploying your app to Heroku.

```
git push heroku master
```

This will actually push your app to Heroku.

And there you have it -- your app is live. LOUDBOT learns from your shouting and you can talk with LOUDBOT in any channel it is active in using all-caps. Enjoy!

![image](http://36.media.tumblr.com/d35400ddda99a5d331801984af9cf132/tumblr_inline_nu31t0A1by1tbywuh_500.png)
