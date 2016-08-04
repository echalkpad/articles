# Ionic and the Internet of Things

[Original URL](http://blog.ionic.io/ionic-and-the-internet-of-things/)

> The Internet of Things (IoT) is officially defined as a "global infrastructure for the information society" that connects the physical to the virtual, using information and...

![internet-of-things-ionicframework](http://blog.ionic.io/wp-content/uploads/2015/09/internet-of-things-ionicframework.jpg)

The Internet of Things (IoT) is [officially defined](http://www.itu.int/en/ITU-T/gsi/iot/Pages/default.aspx) as a "global infrastructure for the information society" that connects the physical to the virtual, using information and communication technology.

In other words, the IoT is a concept that essentially allows any device to connect with the Internet, and thus other devices (and people) around it.

As this concept evolves, we're going to need ways to control and interact with these connected devices -- things like toys, refrigerators, and even cars -- pretty much anything you can think of. This is where things get exciting!

**The potential is _enormous_**.

Developers can build apps -- many of them -- that can easily interact with these devices. As such, apps will need to be built speedily and inexpensively. Ionic is an incredibly efficient way to build these apps and offers the full native support you need to access all the different device features. We're super excited about Ionic's potential in this space and what it's already offering developers.

Three awesome companies are leveraging Ionic for the Internet of Things in some really interesting ways:

## Ionic interfacing with hardware over Bluetooth Smart

Alex Albino (CTO) and Conrad Wade (CEO) of [Femto.io](http://femto.io/) are using Ionic to interface with hardware over Bluetooth Smart.

![femtoduino](http://blog.ionic.io/wp-content/uploads/2015/09/femtoduino.jpg)

"We make a wireless sensor board named [IMUduino](http://femto.io/products/imuduino)," says Albino. "It transmits 3D position/orientation data, along with altitude and temperature, all using Bluetooth Smart (a.k.a Bluetooth Low Energy)."

The IMUduino board itself is programmed over USB using the Arduino IDE, but communication over Bluetooth Smart allows the Femto.io team to interface with any BT 4.0 capable smartphone or computer, including iPhone, Mac Book, and even GNU/Linux machines using compatible Bluetooth 4.0 dongles.

Check out their [Ionic code on GitHub](https://github.com/femtoduino/imuduino-btle), inside the Cordova folder.

## Ionic with iBeacons

[Asim Hussain](https://twitter.com/jawache), the founder of [Bubblegum](http://www.bubblegumhq.com/), who also teaches [video courses on Angular and Ionic](http://school.codecraftpro.com/courses/ionic-from-web-to-mobile?product_id=29521&coupon_code=IONIC200), is building an app for a Crossfit gym in London using Ionic and iBeacons, which are small devices that can be mounted on a wall and send a ping every second or so, using Bluetooth.

![ibeacon-ionic](http://blog.ionic.io/wp-content/uploads/2015/09/ibeacon-ionic.png)

"In Crossfit, all members on any given day do the same exercise, called a WOD," explains Hussain. "Some gyms prefer not to reveal the exercise before the day, to avoid people chickening out, but the existing apps that let you record your Crossfit workout kind of need the gym to release the WOD beforehand, so they can parse and display it in the app."

Hussain's client wanted to keep the WOD private until the assigned day, so Hussain is creating a bespoke Ionic app that uses iBeacons to hide the exercise people will be doing that day until they actually enter the gym, so they don't balk.

"iBeacons have a range of about 30-50 meters and will let you know when you are within about ten meters of the device," says Hussain. "We put one in the gym, then built an Ionic app for the members to install. We don't reveal the WOD on their app until they get within a few meters of the entrance. Then, if we detect that they leave the gym (get out of range of the iBeacon), we show a popup on their phone, asking them to enter their scores, if they forgot, or to share today's score on their social networks."

Hussain used a [Cordova plugin](https://github.com/petermetz/cordova-plugin-ibeacon) to create the interface.

## Ionic with Wearables

[Jewelbots](http://jewelbots.com/), technology-enabled friendship bracelets, enable friends to communicate by lighting up when a friend is nearby or buzzing to send messages.

![jewelbot-ionic](http://blog.ionic.io/wp-content/uploads/2015/09/jewelbot-ionic.jpg)

Open source software allows wearers to program their Jewelbots to do just about anything, exposing tween and teen girls to the possibilities of coding, and opening their minds to STEM, at an age when many lose interest.

"Ionic gives us the ability to create a cohesive cross-platform mobile experience, and the wrappers provided by ngCordova make the plugin experience simpler to implement," says [George Stocker](https://twitter.com/gortok), VP of Software Engineering. "The real magic for us is being able to write cross-platform code against the plugin wrappers available in ngCordova. Since BluetoothLE is a big part of the experience, having a [plugin compatible with Cordova 5.0](https://github.com/Jewelbots/ng-cordova-bluetoothle) was crucial for us to be able to target multiple platforms as an early stage startup."

"Since we allow Jewelbots owners to add friends through their address book, having the [contact plugin](http://ngcordova.com/docs/plugins/contacts/) available is central to the user experience," adds Stocker. "A good user experience also depends upon background notifications, and to that end, we've also implemented the [push notifications plugin](https://github.com/driftyco/ng-cordova/issues/963) and integrated it with Parse, and we're currently working on [local notifications](http://ngcordova.com/docs/plugins/localNotification/). Because of the rapid development nature of Ionic, adding prototypes of new features has been easy, and that allows us to iterate faster for our users."

## Just the Beginning

These three use cases offer a tiny glimpse of the future of Ionic and the Internet of Things, and we know there are countless other devs using Ionic in creative ways for the IoT. This unique space is growing quickly, and it's exciting to see companies like these already taking advantage of Ionic's "speed-to-market" capabilities.

We'll keep this conversation going as we see more and more Ionic apps being built in this space. As always, [drop us a line](http://blog.ionic.io/cdn-cgi/l/email-protection#630802170a06230a0c0d0a004d0a0c) and let us know what you're building!
