# Gobot

[Original URL](http://gobot.io/blog/2015/10/27/gobot-0.10-less-is-more/)

> We've just released the new 0.10 version of Gobot, our open source robotics and Internet of Things (IoT) framework written in the Go programming language. This new version incorporates many changes...

We've just released the new 0.10 version of Gobot, our open source robotics and Internet of Things (IoT) framework written in the Go programming language. This new version incorporates many changes and community contributions to make Gobot much faster, leaner. And of course, add lots more hardware support.

We've got a list of the new changes below, but if you want to see the gory details, please check out the compare view on [GitHub](https://github.com/hybridgroup/gobot/compare/0.8.0...0.10).

## What Happened To The 0.9 Release?

There were so many important changes, especially ones worked on by the amazing participants at the "sold-out" [Gophercon](http://www.gophercon.com/) [hardware hackday](https://www.eventbrite.com/e/gophercon-gobot-hardware-hackday-tickets-17696426488), that we decided to hold off on that version, and instead go right on to this new 0.10 release.

## Changelog

- **Refactor core to cleanup robot initialization and shutdown**
- **Remove unnecessary goroutines spawned by NewEvent**
- **api**
- **bebop**

  - Add support for the Parrot Bebop drone

- **keyboard**

  - Add support for keyboard control

- **gpio**

  - Support added for 10 new Grove GPIO devices:

    - Grove Touch Sensor
    - Grove Sound Sensor
    - Grove Button
    - Grove Buzzer
    - Grove Led
    - Grove Light Sensor
    - Grove Vibration Sensor
    - Grove Rotary
    - Grove Relay
    - Grove Temperature Sensor

- **i2c**

  - Support added for 2 new Grove i2c devices:

    - Grove Accelerometer
    - Grove LCD with RGB backlit display

- **gort**

  - Update `bluetooth` Linux support for BlueZ 5.x
  - Bugfixes

- **docs**

  - Many useful fixes and updates for docs, mostly contributed by our wonderful community.

## Contributors

So many people helped with this release, both formally via code or documentation contributions, as well as helpful feedback, comments, and hallway chats. Gobot could not be what it is without all of us in this community working together. We really appreciate it!

## Issues

As always, if you find any issues with the new Gobot release, please let us know by entering a [GitHub issue](https://github.com/hybridgroup/gobot/issues) and we'll get back to you as soon as possible. We're also around on IRC (#gobotio on freenode) as much as we can be.

To stay informed on our rapid progress, please follow us on Twitter at [@gobotio](http://twitter.com/gobotio), as our team and contributors continue our work together.
