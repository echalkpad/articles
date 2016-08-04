# Output voltage control of DC/DC converters

[Original URL](http://www.fischl.de/dcdccontrol/)

> Some applications needs to control the output voltage of a dc/dc converter instead using a fixed output voltage. For example battery chargers has to adjust the output voltage to the current battery...

_Some applications needs to control the output voltage of a dc/dc converter instead using a fixed output voltage. For example battery chargers has to adjust the output voltage to the current battery level. This page shows how to add such a control function to a buck converter circuit._

## Control output via external voltage source

Typically a voltage divider is used in dc converters to adjust the output voltage to the needed feedback voltage. To control the feedback signal by an external voltage source, a third resistor is added to the circuit.<br>
A cheap LM2596 converter board is used (pin 4 of LM2596 is the feedback pin):<br>
![LM2596 board](http://www.fischl.de/dcdccontrol/lm2596_board.jpg) ![LM2596 board bottom view](http://www.fischl.de/dcdccontrol/lm2596_board_bot.jpg)

## Calculate resistor network

If there is a fixed resistor to ground and a poti to control the feedback voltage, the additional "control"-resistor can be calculated:

![dc/dc converter resistor network](http://www.fischl.de/dcdccontrol/dccontrol_resistors.png)

Feedback voltage: V<br>
Resistor R3: Ohm<br>
Output voltage range: V ... V<br>
Control voltage range: V ... V

## Trim poti for wanted output voltage range

If you use a dc/dc converter board with potentiometer, please note that calibrating the poti with a multimeter is not possible because the resistance cannot be measured in circuit.

Steps to set the upper voltage bound:

- Set control voltage to 0V. Note: At 0V I2 is negative.
- Add input voltage to converter board.
- Measure output voltage with multimeter and trim the poti to get the required maximum output voltage.

## Use DAC to control output voltage

Connect a DAC output via resistor (R2) to the feedback pin of the dc converter chip. Here, a ![en](http://www.fischl.de/pics/en.png) [MCP4725 Breakout Board](https://www.sparkfun.com/products/12918) is used which is controlled via an ![de](http://www.fischl.de/pics/de.png) [ELV USB-I2C-Interface](http://www.elv.de/usb-i2c-interface-komplettbausatz-inkl-gehaeuse-bearbeitet-und-bedruckt-usb-kabel-3-anschlusskabel.html). The output voltage of the DAC can be set through this interface with serial commands. Example to set 0V DAC voltage (-> maximum output voltage on dc/dc converter): SC0 00 00 P

Set maximum DAC voltage (-> minimum output voltage on dc/dc converter): SC0 0F FF P

![DAC controlled LM2596 converter board](http://www.fischl.de/dcdccontrol/dac_controlled_converter.jpg)

## Links

![en](http://www.fischl.de/pics/en.png) [Arduino Forum - Control the voltage of a buck converter](http://forum.arduino.cc/index.php?topic=133001.msg1000888#msg1000888)
