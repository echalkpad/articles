# yoannmoinet/nipplejs

[Original URL](https://github.com/yoannmoinet/nipplejs)

> A virtual joystick for touch capable interfaces Install npm install nipplejs –save // OR bower install nipplejs –save Demo Check out the demo here. Usage Import it the way

[![alt tag](https://github.com/yoannmoinet/nipplejs/raw/master/test/nipplejs.png)](https://github.com/yoannmoinet/nipplejs/blob/master/test/nipplejs.png)

> A virtual joystick for touch capable interfaces

## [](https://github.com/yoannmoinet/nipplejs#install)Install

```
npm install nipplejs --save

// OR

bower install nipplejs --save
```

## [](https://github.com/yoannmoinet/nipplejs#demo)Demo

Check out the [demo here](http://yoannmoinet.github.io/nipplejs/#demo).

## [](https://github.com/yoannmoinet/nipplejs#usage)Usage

Import it the way you want into your project :

```
// CommonJS
var joystick = require('nipplejs').create(options);

// AMD
define(['nipplejs'], function (nipplejs) {
 var joystick = nipplejs.create(options);
});

// Global
<script type="text/javascript" src="./dist/nipplejs.min.js"></script>
<script type="text/javascript">
 var joystick = nipplejs.create(options);
</script>
```

## [](https://github.com/yoannmoinet/nipplejs#options)Options

You can configure your joystick in different ways :

```
var options = {
 zone: Element,
 color: String,
 size: Integer,
 threshold: Float,
 fadeTime: Integer
};
```

All options are optional.

### [](https://github.com/yoannmoinet/nipplejs#zone-defaults-to-body)[`zone`] defaults to 'body'

The dom element in which your joystick will be injected.

```
<div id="zone_joystick"></div>

<script type="text/javascript" src="./nipplejs.min.js"></script>
<script type="text/javascript">
 var options = {
 zone: document.getElementById('zone_joystick');
 };
 var joystick = nipplejs.create(options);
</script>
```

This zone also serve as the mouse/touch events handler.

It represents the zone where your joystick will be active.

### [](https://github.com/yoannmoinet/nipplejs#color-defaults-to-white)[`color`] defaults to 'white'

The background color of your joystick's elements.

Can be any valid CSS color.

### [](https://github.com/yoannmoinet/nipplejs#size-defaults-to-100)[`size`] defaults to 100

The size in pixel of the outer circle.

The inner circle is 50% of this size.

### [](https://github.com/yoannmoinet/nipplejs#threshold-defaults-to-01)[`threshold`] defaults to 0.1

This is the strength needed to trigger a directional event.

Basically, the center is 0 and the outer is 1.

You need to at least go to 0.1 to trigger a directional event.

### [](https://github.com/yoannmoinet/nipplejs#fadetime-defaults-to-250)[`fadeTime`] defaults to 250

The time it takes for joystick to fade-out and fade-in when activated or de-activated.

## [](https://github.com/yoannmoinet/nipplejs#api)API

Your `nipplejs` instance will come back as :

```
{
 el: Element,
 on: Function,
 off: Function,
 options: Object,
 ui: Object
}
```

### [](https://github.com/yoannmoinet/nipplejs#el)`el`

Dom element in which the joystick gets created.

```
<div class="nipple">
 <div class="front"></div>
 <div class="back"></div>
</div>
```

### [](https://github.com/yoannmoinet/nipplejs#on)`on`

If you whish to listen to internal events like :

```
joystick.on('event', function (evt, data) {
 // Do something.
});
```

### [](https://github.com/yoannmoinet/nipplejs#off)`off`

To remove an event handler :

```
joystick.off('event', handler);
```

### [](https://github.com/yoannmoinet/nipplejs#options-1)`options`

Simply the options you passed at its creation.

### [](https://github.com/yoannmoinet/nipplejs#ui)`ui`

The object that store its ui elements

```
{
 el: <div class="nipple"></div>
 back: <div class="back"></div>
 front: <div class="front"></div>
}
```

## [](https://github.com/yoannmoinet/nipplejs#events)Events

### [](https://github.com/yoannmoinet/nipplejs#start)`start`

The joystick is activated. (the user pressed on the active zone)

### [](https://github.com/yoannmoinet/nipplejs#end)`end`

The joystick is de-activated. (the user released the active zone)

### [](https://github.com/yoannmoinet/nipplejs#move)`move`

The joystick is moved. Comes with data :

```
{
 position: { // absolute position of the center in pixels
 x: 125,
 y: 95
 },
 force: 0.2, // strength in %
 distance: 25.4, // distance from center in pixels
 angle: {
 radian: 1.5707963268, // angle in radian
 degree: 90
 }
}
```

### [](https://github.com/yoannmoinet/nipplejs#dir)`dir`

When a direction is reached after the threshold.

Direction are split with a 45° angle.

```
// \ UP /
// \ /
// LEFT RIGHT
// / \
// /DOWN \
```

You can also listen to specific direction like :

- `dir:up`
- `dir:down`
- `dir:right`
- `dir:left`

In this configuration only one direction is triggered at a time.

### [](https://github.com/yoannmoinet/nipplejs#plain)`plain`

When a plain direction is reached after the threshold.

Plain directions are split with a 90° angle.

```
// U|P
//LEFT____|___ RIGHT
// |
// DO|WN
```

You can also listen to specific plain direction like :

- `plain:up`
- `plain:down`
- `plain:right`
- `plain:left`

In this configuration two directions can be triggered at a time, because the user could be both `up` and `left` for example.
