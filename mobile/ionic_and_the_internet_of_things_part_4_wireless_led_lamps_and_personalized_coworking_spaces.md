# Ionic and the Internet of Things, Part 4: Wireless LED Lamps and Personalized Coworking Spaces

[Original URL](http://blog.ionic.io/ionic-and-the-internet-of-things-part-4-wireless-led-lamps-and-personalized-coworking-spaces/)

> By Katie on December 22, 2015 It's really exciting to us that so many developers are creating Ionic apps for The Internet of Things. This week, we wanted to feature two Ionic developers who...

<span class="author-icon">
  <img src="http://0.gravatar.com/avatar/69a31e08b02e80b0b370f2068d0a3e97?s=32&amp;r=g" class="avatar avatar-32 wp-user-avatar wp-user-avatar-32 photo avatar-default" width="32">
</span>

 

<span class="author vcard">By Katie</span>

 on 

<span class="entry-date">December 22, 2015</span>

![unlamp-header-2](http://blog.ionic.io/wp-content/uploads/2015/12/unlamp-header-2.jpg)

It's really exciting to us that so many developers are creating Ionic apps for [The Internet of Things](http://blog.ionic.io/ionic-and-the-internet-of-things/). This week, we wanted to feature two Ionic developers who are taking it upon themselves to do cool IoT projects with Ionic apps. Let's take a look.

## Making a Wireless LED Lamp

Andrea Macchieraldo created a wireless LED lamp controlled by an Ionic app, which he calls the YunLamp.

"It's still super experimental, but all I did was build a simple sketch with Arduino Yun–Arduino + WiFi–that worked with the Spacebrew API–Socket, basically–and was linked to an Ionic app to turn the lamp on or off, or to set a custom color based on RGB sliders," says Macchieraldo, who is based in Biela, Italy. "This is a personal project so far, but I actually formed a [startup](http://koodit.it/) last year, where I develop all my personal and commercial projects."

The YunLamp was inspired by a project Macchieraldo and some friends worked on two years ago for their friends' band.

"The goal was to build an Arduino light set for a live performance that could be controlled via iPad or something similar," he says. "We ended up using the Arduino Yun and some LED strips. It was great."

Macchieraldo wanted to bring the same technology to his home, so he took his Arduino Yun out of the box and launched a new app using his custom-made [Ionic starter](https://github.com/macchie/koonic).

"All I needed was a way to connect the Arduino and the Ionic App connect," says Macchieraldo. "After some failed attempts, I found out about Spacebrew, an awesome toolkit to get real things on the internet. It's such an easy way to give your products a voice. It comes with a great Arduino Library to get your board online in five minutes or so, but the real thing is the JavaScript Library. I finally managed to make my Ionic app to talk to the Arduino Board, in no more than 30 lines of code. It was great. Building the UI to control the lamp with Ionic was super fast, and I'm really astonished at how fluid and responsive it is."

Check out Macchieraldo's code [here](https://github.com/macchie/yunlamp).

"I love Ionic and Angular; it's so easy to jump from idea to app in a little time," Macchieraldo says. "The open source community is wonderful and packed with great people and great minds that just want to improve and help."

## Personalizing a Coworking Space

Jon Senterfitt, a web and mobile developer at the [Belfort Design Agency](http://belfortagency.com/), worked with colleagues to set up a dashboard using iBeacons at the [Fort Work](http://www.dallasfortwork.com/) coworking space in Dallas, where the agency is based.

"Whenever you get off our elevator at the coworking space, we can see who is on the office floor, and when you walk in, you get a welcome message, which we hope to push into showing three-second, digestible, relevant headlines, based on interest," says Senterfitt. "We set up an [Udoo](http://www.udoo.org/) with a live dashboard and beacons, so when you get off the elevator to the office, it checks you in and then shows you some relevant content. It's simplistic currently but should be fun later down the road, especially with events."

Belfort Design agency does UX research, along with development.

"We're currently building a coworking map with Ionic that provides a pass to enter coworking spaces across the U.S., as a result of the Global Coworking Conference," adds Senterfitt. "The app will end up on the front page of the coworking space's wiki."
