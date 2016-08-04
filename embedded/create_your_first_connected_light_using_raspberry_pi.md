# Create your first connected light using Raspberry Pi

[Original URL](http://lelylan.github.io/lab-projects/raspberry-pi-light/)

> To better understand how the Raspberry Pi sketch works, follows a brief description of what most important sections do. GPIO Setup Set GPIO mode to BOARD and set pin mode for the led and the button...

To better understand how the Raspberry Pi sketch works, follows a brief description of what most important sections do.

## GPIO Setup

Set GPIO mode to `BOARD` and set pin mode for the led and the button pins. This will import the necessary libraries in the GPIO namespace and set the pin numbering to correspond to your breakout board.

## MQTT Callbacks

When a user updates a device status using Lelylan Dashboard a message is published to the topic `devices/<DEVICE-ID>/get` (in topic) and it's received from the Raspberry PI through the `on_message` function.

Every message exchanged with Lelylan is made up by a list of properties where each of them contains the property ID and the property value. For the "basic light" type we only have the light status property which has ID `518be5a700045e1521000001` and accepts the values `on` and `off`.

What we do is to check the received message and parse the JSON to see if the status property is `on` or `off`. Based on this value, the light is turned on or off and a message is sent back to Lelylan to confirm that the physical changes were successfully applied (if not, the device will keep being pending).

## Device Credentials

To make the Raspberry PI communicate with Lelylan create a new MQTT client passing a MQTT client ID as param, a random string not longer than 23 bytes used to identify your Raspberry Pi.

The `device_id` and `device_secret` are used to authenticate the physical object (Raspberri PI) with Lelylan. To get the device credentials open the [Dashboard](http://lelylan.github.io/devices-dashboard-ng), select the desired device, click on settings and copy the Device ID and the Device Secret.

Lelylan uses MQTT, a publish subscribe protocol for the Internet of Things. To make Lelylan communicate with Raspberry Pi you need to set two topics: one receiving messages from Lelylan (`in_topic`) and one sending messages to Lelylan (`out_topic`). These topics are unique and identified by the device id.

## MQTT Connection

Set the Lelylan MQTT server credentials and the MQTT server IP where to connect. Once the connection is completed, the MQTT client subscribes to the `in_topic`. This way the `on_message` callback function is called every time a message is received from Lelylan.

## Button logic

Here we define the loop function where we use a pushbutton as a switch. Each time you press the button the led is turned on or off. To make it work we need to add a debounce, otherwise everytime you press the button a single press would be recognized as multiple press.

Once the push button is pressed (the led status changes) we publish a message to Lelylan with the updates.
