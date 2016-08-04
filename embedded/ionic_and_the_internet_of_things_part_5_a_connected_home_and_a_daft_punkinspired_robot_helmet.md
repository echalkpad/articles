# Ionic and the Internet of Things, Part 5: A Connected Home and a Daft Punk-Inspired Robot Helmet

[Original URL](http://blog.ionic.io/ionic-and-the-internet-of-things-part-5-a-connected-home-and-a-daft-punk-inspired-robot-helmet/)

> By Katie on January 22, 2016 We're constantly excited by the creativity Ionic developers demonstrate when using Ionic for IoT projects. Ionic developer North McCormick, who started Ionic Uta

<span class="author-icon">
  <img src="http://0.gravatar.com/avatar/69a31e08b02e80b0b370f2068d0a3e97?s=32&amp;r=g" class="avatar avatar-32 wp-user-avatar wp-user-avatar-32 photo avatar-default" width="32">
</span>

 

<span class="author vcard">By Katie</span>

 on 

<span class="entry-date">January 22, 2016</span>

![ionic-iot-generic](http://blog.ionic.io/wp-content/uploads/2016/01/ionic-iot-generic.png)

We're constantly excited by the creativity Ionic developers demonstrate when using Ionic for [IoT projects](http://blog.ionic.io/ionic-and-the-internet-of-things/). Ionic developer [North McCormick](https://github.com/NorthMcCormick), who started [Ionic Utah](https://twitter.com/ionicutah), recently talked with us about two personal IoT projects he built last year, using Ionic.

"I was super excited that when the time came to work on my IoT projects that I had Ionic at my side and ready to go," says McCormick, who designed an automated sprinkler system for his parents and a Daft Punk-inspired robot helmet for a costume. "I've been using Ionic since the early betas, and it's grown so much!"

## A Connected Home Platform

McCormick's first IoT project was a connected home platform called Atom, which automated his parents' sprinkler system. Atom was built off two different back-end technologies: [Parse](https://parse.com/), which manages device lists and activations; and [Electric Imp](https://electricimp.com/), which supplies developers with WiFi-connected cards and development boards for projects that come in a few different form factors.

"I decided to use Electric Imp because they handle all the WiFi connectivity and API to work with the chips, which was something I really didn't want to deal with," explains McCormick. "Ionic helped me build out the user interface."

![connected-home](http://blog.ionic.io/wp-content/uploads/2016/01/connected-home.jpg)

McCormick got the first few revisions done in less than a day. "Everything, like login, activating devices, and managing them was so simple," he says. "I started out with playlists and a parent/child view template and went from there."

With the Electric Imp in place and everything wired up, McCormick used TestFlight to deploy the app to his parents' phones.

"They had just built a new house, and it saved them from spending a bunch of time running around doing sprinkler timing," says McCormick. "If I wasn't able to build the app so quickly, it never would have been a reality."

McCormick says his first experience in IoT opened his eyes to the value of rapid prototyping. "Being able to go from an idea on a piece of paper to an almost 100% working project was priceless," he says.

## Daft Punk-Inspired Robot Helmet

McCormick's second venture into the IoT was a connected, Daft Punk-inspired robot helmet. The controller is an Arduino with a BLE attachment.

![daft-helmet](http://blog.ionic.io/wp-content/uploads/2016/01/daft-helmet.jpg)

"Bluetooth was new to me, but a Cordova plugin took over a lot of what needed to be done to connect it," says McCormick. "The Ionic app was built in less than an hour, and it was a hit on the streets of San Francisco on Halloween."

McCormick's app toggles the faces in the helmet even scrolls text across the front. Eventually, McCormick wants to use the Ionic app to receive faces from online, send them to the app with a push notification, and display them, allowing the public to change it up.

"Again, another project that would just not have been possible without Ionic," says McCormick. "It's my go-to for the mobile platform. I think that Electric Imp is a game changer in small companies or businesses looking to deploy solutions that work almost over the counter. I'd like to build more home-platform devices and eventually tap into things like home-kit and such."
