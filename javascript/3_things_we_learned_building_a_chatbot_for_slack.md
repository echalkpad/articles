# 3 Things We Learned Building a Chatbot for Slack

[Original URL](https://medium.com/@kipsearch/3-things-we-learned-building-a-chatbot-for-slack-2dc32321d77c#.v5mabd9ri)

> Kip is a chatbot that helps you save time by doing all the shopping for you and your team. Integrating with Slack was a logical choice, we're Slack users and wanted a simple, efficient way of team...

![](https://cdn-images-1.medium.com/max/800/1*0Z6s5C7jDFI7NxF83SAuqQ.png)

[Kip](http://kipthis.com) is a chatbot that helps you save time by doing all the shopping for you and your team. Integrating with Slack was a logical choice, we're Slack users and wanted a simple, efficient way of team shopping via chatbot.

Try Kip, click the button below:

![](https://cdn-images-1.medium.com/max/800/1*2y0xLJS6dCWYCf5YkJWBdQ@2x.png)

Click me We ran into some interesting issues when building Kip for Slack. We hope you find our solutions useful!

Sending emojis across devices can be messy. An emoji sent by you on iOS is displayed differently to someone receiving it on Android. Here are some examples of emoji variation across devices:

![](https://cdn-images-1.medium.com/max/800/1*2zQO_3OAGSu0UMaq2epm_g.png)

Emoji miscommunication, the new normal Chat platforms have their own way of displaying emojis. If you send the same emoji code to every platform, it will display differently to all users.

We added a translator into Kip bot so it can speak multiple emoji dialects. Kip sends different emoji codes based on which chat platform (Slack, Facebook, SMS Texting) a user messaged from:

![](https://cdn-images-1.medium.com/max/800/1*coKtLwFsdUaGF9XyBFU1kg.png)

Emoji translator Sample translation for numeric emoji (should actually use Redis or similar datastore, thanks [r3versed](https://medium.com/@r3versed)):

By doing so we hope to avoid miscommunication while still retaining the universal simplicity of an emoji 😊.

When we added team shopping to Slack, we ran into a problem where Kip would respond with search results for everything people typed into public channels like #general (quite annoying!).

We solved this by sorting incoming Slack messages into **direct messages:** one user talking to Kip and **public channels:** many users talking to Kip.

Slack sends new chat messages to Kip with an ID _(i.e. C5H6YHBN)_ that represents the channel the message came from. Chats that start with the letter **C** are from public channels, while **D** are direct messages.

If the message is from a public channel, we check the incoming message for the user ID of Kip bot on the team _(i.e. U7A8YHVN_) so we only respond when someone **@** mentions Kip.

Example of **@Kip** being mentioned in a public channel for a group purchase:

![](https://cdn-images-1.medium.com/max/800/1*oc80nkg2a6KLuXuPAEN1vA.png)

Example Node.js code we use for sorting messages:

![](https://cdn-images-1.medium.com/max/800/1*AMtuQ43ObVz1BZOrnmlXSg.gif)

With Google or Amazon, users now expect instant results. In searches with complicated modifications i.e "_similar to option 3 but cheaper and in blue_", Kip suffers a bit of lag. This makes our users impatient.

A simple design hack, we send a "typing event" to Slack so it looks like Kip is typing while our bot rushes to respond quickly. Our users say they don't mind the lag now since they know Kip understands them and is working on it. 😊

![](https://cdn-images-1.medium.com/max/800/1*CoU6iNAFf530PuDefG0XRw.png)

Sending "typing events" to Slack is not currently available in the Node.js package s[lackbots](https://www.npmjs.com/package/slackbots), so we extended it by adding this to the package index.js:

Using design solutions to solve complex programming issues saved us a lot of time and order of magnitude so that we could launch Kip earlier. In the next post, we'll be talking about our design process of coming up with AI shopping chatbot persona. Stay tuned!

Try Kip on Slack by tapping the button below or try the demo at [kipthis.com/chat](http://kipthis.com/chat)

[![](https://cdn-images-1.medium.com/max/800/1*zl4YeeE7wkyc-dt4apQwKQ.png)](https://slack.com/oauth/authorize?scope=bot&client_id=2804113073.14708197459)

Push this button If you enjoyed reading, please hit recommend. 💚
