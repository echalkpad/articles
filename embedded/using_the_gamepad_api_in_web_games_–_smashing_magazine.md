# Using The Gamepad API In Web Games – Smashing Magazine

[Original URL](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/)

> The Gamepad API is a relatively new piece of technology that allows us to access the state of connected gamepads using JavaScript, which is great news for HTML5 game developers. A lot of game genres,...

The Gamepad API is a relatively new piece of technology that allows us to access the state of connected gamepads using JavaScript, which is great news for HTML5 game developers.

A lot of game genres, such as racing and platform fighting games, rely on a gamepad rather than a keyboard and mouse for the best experience. This means these games can now be played on the web with the same gamepads that are used for consoles.

A [demo is available](http://charliejwalter.net/gamepad/demos/main)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#1">1</a>
</sup>

, and if you don't have a gamepad, you can still enjoy the demo using a keyboard.

## Keyboard Fallback [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#keyboard-fallback)

There are also apps like [Joypad](http://getjoypad.com/legacy/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#2">2</a>
</sup>

 (iOS) and [Ultimate Gamepad](https://play.google.com/store/apps/details?id=com.negusoft.ugamepad&hl=en)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#3">3</a>
</sup>

 (Android) that allow you to connect a smartphone to the computer via a "receiver" app. Receiver applications simulate keyboard input, as opposed to a gamepad.

### Which Gamepads Work? [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#which-gamepads-work)

Any gamepad that is understood by the operating system as either an XInput or DirectInput device will work with the Gamepad API. Getting other console controllers to work is possible, but that would require either hardware converters or additional software.

[![Gamepads](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/01-gamepads-opt-preview.jpg)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/01-gamepads-opt.jpg)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#4">4</a>
</sup>

<br>
Gamepads. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/01-gamepads-opt.jpg)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#5">5</a>
</sup>

)

### Browser Support [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#browser-support)

Because this API is relatively new and experimental, [browser support is limited](http://caniuse.com/#feat=gamepad)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#6">6</a>
</sup>

 and the [W3C documentation](http://www.w3.org/TR/gamepad/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#7">7</a>
</sup>

 is still a working draft.

Nevertheless, browser implementation is [above 50%](http://caniuse.com/#feat=gamepad)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#8">8</a>
</sup>

, which includes all major browsers. The API is even making its way into the mobile world, with Chrome for Android being the first mobile browser to support it.

On Nintendo's Wii U browser, the Wii U gamepad is accessible. Unfortunately, Nintendo hasn't yet implemented the Gamepad API; instead, the [device has its own property](https://www.nintendo.com/wiiu/built-in-software/browser-specs/extended-functionality/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#9">9</a>
</sup>

 on the window (`window.wiiu.gamepad`) and stores its button states as hexadecimal values, with specific flags for each button.

#### Feature Detection [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#feature-detection)

We can check whether a browser supports the Gamepad API with this snippet:

```
if(!!navigator.getGamepads){
 // Browser supports the Gamepad API
}
```

### Reading The Gamepad States [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#reading-the-gamepad-states)

The `Gamepad` states are accessible with:

```
var gamepads = navigator.getGamepads();
```

Currently, in Chrome and Opera, this will return a `GamepadList` of up to four `Gamepad` objects.

```
GamepadList {0: Gamepad, 1: Gamepad, 2: undefined, 3: undefined}
```

Firefox, instead, returns a JavaScript `Array` of `Gamepad`s, which, theoretically, is infinite. (I've had nine gamepads connected at the same time.)

```
Array [ Gamepad, Gamepad ]
```

#### Polling [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#polling)

We can poll the `Gamepad` states using `requestAnimationFrame`. Depending on the number of gamepads you want to support, this will increase the complexity of how you use the `Gamepad` states. If you want to support only one gamepad, you could poll for the first gamepad in the collection like so:

```
var gamepad = navigator.getGamepads()[0];
```

This means it will grab the first gamepad that is connected or, if all gamepads are already connected, the first gamepad to have a button pressed.

#### Each `Gamepad` object looks like this: [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#each-gamepad-object-looks-like-this)

```
axes: Array[4]
buttons: Array[16]
connected: true
id: "Xbox 360 Controller (XInput STANDARD GAMEPAD)"
index: 0
mapping: "standard"
timestamp: 12
```

This is the `Gamepad` object of a [Microsoft Xbox 360 controller for Windows](https://www.microsoft.com/hardware/en-gb/p/xbox-360-controller-for-windows)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#10">10</a>
</sup>

.

**Note:** The `id` is not consistent across browsers. For example, it's `45e-28e-Wireless 360 Controller` in [Mozilla Firefox](https://www.mozilla.org/en-GB/firefox/new/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#11">11</a>
</sup>

, which is different from what [Google Chrome](http://www.google.com/chrome/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#12">12</a>
</sup>

 provides above.

Property    | Description
----------- | ----------------------------------------------------------------------------------------------------------------------------------------
`connected` | This is a boolean that indicates the gamepad's connectivity
`id`        | This string contains identifying information about the gamepad
`index`     | This is a unique auto-incremented integer for each gamepad
`mapping`   | This string tells us whether the browser has remapped the device to a known layout
`timestamp` | This increments when the device's state changes. Some devices<br>
constantly poll, which means the timestamp is constantly incrementing.
`axes`      | This is a collection of numbers that represent the state of each analogue stick or button.
`buttons`   | This collection of objects represents the state of each button.

#### Axes [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#axes)

On gamepads that have analogue joysticks, the `axes` array will contain numbers that range between a minimum and maximum for each axis, usually `-1` and `1`.

#### Applying a Dead Zone [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#applying-a-dead-zone)

Because the analogue stick varies between `-1` and `1`, if the stick is not being touched and is in its center position, then, theoretically, the value should be `0`. However, this isn't always the case on cheap controllers or gamepads that are worn or damaged or have "wiggly" thumb sticks (yes, that gamepad your friend always conveniently tries to give you).

A "dead zone" is a threshold used to prevent values below a certain amount from being used to control the game.

The following function applies a dead zone. The threshold is also subtracted from any value _above_ the threshold, so that the value at the threshold is 0, rather than a sudden 0.25, for example.

```
var applyDeadzone = function(number, threshold){
 percentage = (Math.abs(number) - threshold) / (1 - threshold);

 if(percentage < 0)
 percentage = 0;

 return percentage articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii (number > 0 ? 1 : -1);
}
```

It can be used like this:

```
var joystickX = applyDeadzone(gamepad.axes[0], 0.25);
```

This ignores values between -0.25 and +0.25 and calculates the decimal percentage, taking into consideration the given threshold. If you applied this to both the x and y axes, this would give a dead zone that looks something like this when graphed:

[![deadzone](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/02-deadzone-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/02-deadzone-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#13">13</a>
</sup>

<br>
Dead zone. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/02-deadzone-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#14">14</a>
</sup>

) **Note:** Axes aren't always analogue. Some controllers will toggle between values. For example, some D-pads toggle between a maximum, minimum and center value for each axis, such as `-1`, `0` and `1`.

#### Buttons [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#buttons)

For the `buttons` array, a `GamepadButton` object is provided for each item. In most cases, the `value` property directly correlates with the `pressed` property. For example, `value` will toggle between `0` and `1` because `pressed` toggles between `false` and `true`.

#### Released Button [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#released-button)

```
GamepadButton
 pressed: false
 value: 0
```

#### Pressed Button [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#pressed-button)

```
GamepadButton
 pressed: true
 value: 1
```

#### Analogue Buttons [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#analogue-buttons)

Most seventh-generation gamepads (like the Xbox 360 controller) have analogue buttons, such as the left and right triggers. In this case, `value` will range from 0 to 1, respectively. Although `pressed` works, using it isn't recommended for non-digital buttons; instead, use a threshold against the `value`, which could be as simple as this:

```
if(gamepad.buttons[7].value > 0.5){
 // FIRE!
}
```

**Note:** Because the button is also analogue, a dead zone might need to be applied here, too.

### Varying Layouts [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#varying-layouts)

Each controller is different, which means the length of the `axes` and `buttons` will vary; this also applies to different connection converters and/or drivers. According to the specification, browsers should map the `axes` and `buttons` as closely as possible to the suggested "[standard gamepad](http://www.w3.org/TR/gamepad/#remapping)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#15">15</a>
</sup>

." Not all gamepads will have all of these buttons; missing buttons will appear as being in an unpressed state (for buttons) or neutral state (for axes) in the object. Adding a "control settings" area to your game would be a good idea if you plan to support weird and wonderful gamepads, giving players the freedom to configure their gamepad how they want and giving users with different gamepads the opportunity to manually map their controls to your game.

#### Microsoft Xbox 360 Controller for Windows [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#microsoft-xbox-360-controller-for-windows)

The driver I use to connect to a Mac is [available on GitHub](https://github.com/d235j/360Controller/releases)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#16">16</a>
</sup>

.

[![360 Layout](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/03-360layout-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/03-360layout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#17">17</a>
</sup>

<br>
Microsoft Xbox 360 layout. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/03-360layout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#18">18</a>
</sup>

)

#### NES Controller [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#nes-controller)

Here is the layout of an original NES controller, using a 15-pin-to-USB converter (the layout may differ by manufacturer). This particular one uses axes for directional input, rather than buttons.

[![Nes Layout](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/04-neslayout-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/04-neslayout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#19">19</a>
</sup>

<br>
NES layout. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/04-neslayout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#20">20</a>
</sup>

)

#### Saitek SP550 USB Stick and Pad [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#saitek-sp550-usb-stick-and-pad)

This has 12 buttons (14 on the device, with the two pad triggers having the same functionality as the joystick's trigger and thumb button) and three axes (the third axis being the throttle slider next to the joystick). The joystick part of the controller can be detached, leaving just the pad. Doing this will disconnect the controller and reconnect it as a different controller, meaning that details about the returned `Gamepad` object will change, too: The `id` will change from `SP550 Stick & Pad Combo (Vendor: 06a3 Product: 100a)` to `SP550 Pad (Vendor: 06a3 Product: 100b)`, and the layout will be different also. In "stick and pad" mode, the directional pad is in the `buttons` array (four buttons), but in "pad" mode, the directional pad is in the `axes` array (two axes).

[![Saitek SP550](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/05-sp550layout-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/05-sp550layout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#21">21</a>
</sup>

<br>
Saitek SP550\. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/05-sp550layout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#22">22</a>
</sup>

)

#### N64 Controller [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#n64-controller)

This is one of my favorite controllers, mainly because of nostalgia, not ergonomics.

[![N64 Controller](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/06-n64layout-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/06-n64layout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#23">23</a>
</sup>

<br>
N64 controller. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/06-n64layout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#24">24</a>
</sup>

)

#### Nintendo Wiimote and Nunchuck [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#nintendo-wiimote-and-nunchuck)

With these drivers, it was possible to use the Wiimote and Nunchuk with the Gamepad API. Different drivers will result in different layouts.

For **Windows**, see Julian Löhr's "[HID Wiimote: A Windows Device Driver for the Nintendo Wii Remote](http://julianloehr.de/educational-work/hid-wiimote/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#25">25</a>
</sup>

."

For **Mac**, see [Wjoy](https://github.com/alxn1/wjoy)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#26">26</a>
</sup>

, a Nintendo Wiimote driver for Mac OS X.

[![Wiimote Layout](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/07-wiimotelayout-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/07-wiimotelayout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#27">27</a>
</sup>

<br>
Wiimote layout. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/07-wiimotelayout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#28">28</a>
</sup>

) [![Wiimote Nunchuck Layout](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/08-wiimotenunchucklayout-opt-preview.png)](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/08-wiimotenunchucklayout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#29">29</a>
</sup>

<br>
Wiimote Nunchuck layout. ([View large version](https://media-mediatemple.netdna-ssl.com/wp-content/uploads/2016/01/08-wiimotenunchucklayout-opt.png)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#30">30</a>
</sup>

) Charlie J. Walter has a [demo for Wiimote](http://charliejwalter.net/gamepad/demos/wiimote/car/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#31">31</a>
</sup>

, which works only with the Windows driver.

For this demo, I created a **calibration** feature, because the drivers think that the Wiimote gyroscope's neutral position (dead center) is the value when the controller is connected. This means you have to connect the controller when the controller is on a flat surface. However, you can recalibrate the controller easily enough by reading the value of the gyroscope (a value in `axes`) when the controller is on a flat surface, and then storing that value and subtracting it from any further readings.

### Events [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#events)

The Gamepad API comes with two window events, `gamepadconnected` and `gamepaddisconnected`, which trigger on connection and disconnection, respectively. The user must press a button on the connected gamepad in order for a `gamepadconnected` event to be recognized. The relevant `Gamepad` object is available on both event objects with `e.gamepad`.

#### Connection Events [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#connection-events)

```
window.addEventListener("gamepadconnected", function(e) {

 // Gamepad connected
 console.log("Gamepad connected", e.gamepad);
});

window.addEventListener("gamepaddisconnected", function(e) {

 // Gamepad disconnected
 console.log("Gamepad disconnected", e.gamepad);
});
```

#### State Change Events [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#state-change-events)

State change events haven't made it into the specification yet and require more discussion. However, Firefox already has three state change events: `gamepadbuttondown`, `gamepadbuttonup` and `gamepadaxismove`. These event names take on a similar naming convention to keyboard and mouse events. (Other suggestions include `gamepadchanged` and `gamepadaxischanged`.)

```
window.addEventListener("gamepadbuttondown", function(e){
 // Button down
 console.log(

 "Button down",
 e.button, // Index of button in buttons array
 e.gamepad

 );
});

window.addEventListener("gamepadbuttonup", function(e){
 // Button up
 console.log(

 "Button up",
 e.button, // Index of button in buttons array
 e.gamepad

 );
});
```

**Note:** Currently, these events work only in Firefox.

```
window.addEventListener("gamepadaxismove", function(e){

 // Axis move
 console.log(

 "Axes move",
 e.axis, // Index of axis in axes array
 e.value,
 e.gamepad

 );
});
```

**Note:** Currently this event works only in Firefox Nightly.

### Using The API In A Game [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#using-the-api-in-a-game)

However you render your game, you can get the state of the gamepad in every game loop and apply it to a controllable entity. Below is a very simple demo where the `value` of `axes[0]` is applied to the CSS `left` property.

See the Pen [Gamepad API – DOM Element Demo](http://codepen.io/cjonasw/pen/MwRQQW/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#32">32</a>
</sup>

 by Charlie Walter ([@cjonasw](http://codepen.io/cjonasw)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#33">33</a>
</sup>

) on [CodePen](http://codepen.io)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#34">34</a>
</sup>

.

Of course, a complex game would be very demanding, so this approach probably isn't the best idea. Instead, you could use WebGL or Canvas or use a rendering library such as [Three.js](http://threejs.org/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#35">35</a>
</sup>

 or [Babylon.js](http://www.babylonjs.com/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#36">36</a>
</sup>

. If the element doesn't move, then your controller is probably mapped differently. This means `axes[0]` does not represent the joystick or analogue button that you are wiggling or is simply not mapped at all; try changing `axes[0]` to another element in the array. [HTML5 Gamepad Tester](http://html5gamepad.com/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#37">37</a>
</sup>

 has a visual breakdown of your connected gamepads and their properties.

#### Control Settings and Mapping Problems [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#control-settings-and-mapping-problems)

Mapping differences can be fixed with a "control settings" utility as shown in [Charlie J. Walter's demo](http://charliejwalter.net/gamepad/demos/main)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#38">38</a>
</sup>

.

#### How the Demo's Control Settings Work [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#how-the-demos-control-settings-work)

In the demo, the user clicks on an input box relating to their gamepad and the control they want to change or remap. The gamepad's state is stored and constantly compared to the current state of that gamepad until an axis or button has changed more than `0.5` of its stored state. At that time, we will know which input the user wants to use for that game action (for example, "left," "right," "jump") and the way they want to use it to represent that game action. The reason I say this is because the "left" and "right" actions will most likely be on the same axis, but the user will use the joystick in a slightly different way to represent a different action (for example, moving the joystick left for left and moving it right for right). This can be figured out by using the stored state of the changed button (but rounded, so that it is `-1`, `0` or `1`) to represent the game action's minimum state, and using the new (also rounded) state to be its maximum. These states are halved for `buttons` thresholds, the result being `-0.5`, `0` or `0.5`.

### How To Provide A Keyboard Fallback [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#how-to-provide-a-keyboard-fallback)

The window events, `onkeydown` and `onkeyup`, make it easy to provide a keyboard fallback. We could add the code to make the player do something in these events, but because we are reading the gamepad's `each` loop and using the `axes` and `buttons` arrays from the gamepad to then make the player do something, putting it here, too, makes sense. It also means less duplicate code.

One way we can do this is by having a `keys` collection to store the current keys down, which gets updated by `onkeydown` and `onkeyup`, respectively.

We can then do simple checks in the game loop to action the player.

```
// If right key down
if(keys[39]){

 // Move player right
}
```

### Wrapping Up [Link](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#wrapping-up)

As with all experimental technologies, results with the Gamepad API are unstable. However, by using it (and providing feedback), you are sculpting the future of the technology.

This represents a huge opportunity for the game industry. Many games already use front-end technologies like [NW.js](http://nwjs.io/)

<sup>
  <a href="https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#39">39</a>
</sup>

 to create native apps; coupling them with the Gamepad API will make for games with a close-to-native experience. However, this is just one element of a rapidly growing gaming platform. Web technologies are now capable of many of the features we see in native games, including audio manipulation (via the Web Audio API), mouse movement input without screen restrictions (via the Pointer Lock API), touch gestures (via the Touch Event APIs) and many more.

In the future, I hope the Gamepad API will be able to access things like the rumble, internal speaker, microphone and other inputs.

To contribute to the future of the API, get involved in discussions and announce bugs when you find them in a given browser. I'd love to hear what you create with it!

_(ds, ml, al, jb)_

[↑ Back to top](https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/#top) [Tweet it](https://twitter.com/intent/tweet?original_referer=https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/&source=tweetbutton&text=Using%20The%20Gamepad%20API%20In%20Web%20Games&url=https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/&via=smashingmag)[Share on Facebook](http://www.facebook.com/sharer/sharer.php?u=https://www.smashingmagazine.com/2015/11/gamepad-api-in-web-games/)
