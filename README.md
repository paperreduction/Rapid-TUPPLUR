# Rapid TUPPLUR

## HomeAssistant MQTT Cover Firmware for ESP8266 with 28BYJ-48 & ULN2003  

<img src="https://www.ikea.com/PIAimages/0602894_PE680588_S5.JPG" width="300px" />

This project is yet another TUPPLUR Roller Blind automation project. It's based on an ESP8266 which utilizes MQTT for communication with HomeAssistant. It supports command, postion, availability, and set_position topics with the default payloads used by the MQTT Cover Platform. There are also a few configuration/calibration topics (details further down).

HomeAssistant configuration to operate Rapid TUPPLUR: 

```yaml
# Example configuration.yaml entry
cover:
  - platform: mqtt
    name: "Shade 1"
    command_topic: "shade1/set"
    position_topic: "shade1/position"
    availability_topic: "shade1/availability"
    set_position_topic: "shade1/set_position"
    qos: 0
    retain: false
    payload_open: "OPEN"
    payload_close: "CLOSE"
    payload_stop: "STOP"
    position_open: 100
    position_closed: 0
    payload_available: "online"
    payload_not_available: "offline"
    optimistic: false
    value_template: '{{ value.x }}'
```
Source: https://www.home-assistant.io/components/cover.mqtt/#full-configuration-state-topic-without-tilt

At some point I hope to migrate this firmware to a Tasmota template.

## Hardware Overview

Besides the Ikea TUPPLUR Roller Blind you'll need an ESP8266, like a NodeMCU, and a s28BYJ-48 stepper motor with ULN2003 driver. You'll also want to select a mounting bracket.

<img src="https://images-na.ssl-images-amazon.com/images/I/7115Yl4jXQL._SL1001_.jpg" width="300px" />
https://www.amazon.com/gp/product/B072M57XQ2/

Nikodem Bartnik created an excellent overview video that explains how to work with this stepper motor:
https://www.youtube.com/watch?v=avrdDZD7qEQ

The 28BYJ-48 ULN2003 has 2048 steps per revolution and that value is baked into the firmware. If you're using this for a different stepper
motor (NEMA17 has 200 steps) you'll need to modify the code.

Microstepping is not supported for the same reasons Nikodem outlines.

*IMPORTANT BLIND SIZE NOTE:* TUPPLUR come in widths from 23" to 48". The 28BYJ-48 ULN2003 stepper motor/driver combo is one of the cheapest you can find. But it has limited torque (functionally only 300 g/cm). Retracting the blind from a fully closed position can be a problem for the larger blinds. The 28BYJ-48 comes in a 5v and 12v version. The 5v version should be able to operate blinds up to 30", however, the 12v version is advisable for larger blinds. And the 48" blind might not work at all. Though Samir Sogay seems to have gotten his to work with some effort: https://youtu.be/7t00NgeBgO8?t=120. 

### ESP8266/NodeMCU

The ULN2003 needs *four output pins* to control the stepping plus GND and 5-12 VCC. You can repurpose commercial hardware for this project (like a Sonoff). But a standard Sonoff doesn't have the full range of 16 GPIO pins availabele on a header. So keep that in mind if you're wanting to use something like a Sonoff (you'll need to do some soldering). I chose to use a NodeMCU like Martin Engström to have easy access to the GPIO pins.

### Mounting Brackets

Here are two options that should work depending on how you want your blinds mounted.

* Inside mount (w/ gear reduction): https://www.thingiverse.com/thing:2392856
* Outside mount (w/ gear reduction): https://www.thingiverse.com/thing:2530155
* Outside mount: https://www.thingiverse.com/thing:2065722

*Gear Reduction*: The inside/outside mounts with gear reduction includes a 10:19 gear reduction to help increase the output torque of the 28BYJ-48. If you're using one of the larger sized blinds these options are advisable. The reduction almost doubles the torque but also reduces the operation speed by half.

## Prior Art

### Martin Engström Feb 1, 2017

https://www.instructables.com/id/Motorized-WiFi-IKEA-Roller-Blind/

Martin's project is an excellent guide and I prefer his solution over others automated blind systems I've seen. 

Martin used Lua firmware that he wrote for his NodeMCU (ESP8266). His manual calibration method with a single button (long/short presses) is nice, but I didn't like the form factor of the extra box to house the switch. I also wanted MQTT calibration. And I wanted "native" HomeAssistant MQTT Cover Platform support.

### Peter Göthager Jun 19, 2017

https://www.youtube.com/watch?v=Dka4of30YOY

Peter's mounting hardware allows for inside window frame mounting and introduces a small gear reduction to help overcome the problems with larger blinds. 20:38

## Blind Setup

 1. Configure your MQTT and Wifi details in the firmware
 2. Compile and flash to your ESP8266
 3. Configure the device
   1. With power off, manually pull your blind all the way down and count the number of rotations needed to get to the bottom.
   2. Power up your hardware and open the device configuration
   3. Enter the total number of rotations required to go from fully closed to fully open. **NOTE:** There are no limit switches included in this template so I recommend using a value that's 1/2 turn less than the actual.
   4. Check the calibrate box and adjust the slider to 100%
   5. Uncheck the calibrate box and adjust the slider to all the way up.
   6. This will run the stepper motor to pull the blind up. It should stop short about 1/2 turn.
   7. Repeat: 1-6 with fine tuning the number of rotations in #3 until the blind retracts as desired.
 
## Home Assistant Integration

 1. Add device as a shutter

