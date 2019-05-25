# Tasmota Template for 28BYJ-48 & ULN2003

This repo outlines the use of my Tasmota Template for 28BYJ-48 stepper motors with the ULN2003 stepper motor driver.

## Back story

I wanted to automate Ikea Roller Blinds like Martin Engström:
https://www.instructables.com/id/Motorized-WiFi-IKEA-Roller-Blind/

Martin's project is an excellent guide, and I prefer his solution over others I've seen. 

But Martin used custom Lua firmware that he wrote for his NodeMCU (ESP8266). I prefer Tasmota since it is much more mature and I have 
many other devices running Tasmota.

So even though this Template should work for many applications, my use case is the focus of this document.

## Hardware Overview

![image](https://images-na.ssl-images-amazon.com/images/I/7115Yl4jXQL._SL1001_.jpg)
https://www.amazon.com/gp/product/B072M57XQ2/

The 28BYJ-48 ULN2003 stepper motor/driver combo is one of the cheapest you can find. And because of that it's a popular choice 
(readily available).

Nikodem Bartnik created an excellent overview video that explains how to work with this hardware:
https://www.youtube.com/watch?v=avrdDZD7qEQ

## Hardware Requirements

The ULN2003 needs *four output pins* to control the stepping plus GND and 5-12 VCC. A standard Sonoff doesn't have the full range of 
16 GPIO pins availabele on a header. So keep that in mind if you're wanting to use something like a Sonoff. I chose to use a NodeMCU 
like Martin to have easy access to the GPIO pins.

The 28BYJ-48 ULN2003 has 2048 steps per revolution and that value is baked into the firmware. If you're using this for a different stepper
motor (NEMA17 has 200 steps) you'll need to modify the code.

Microstepping is not supported for the same reasons Nikodem outlines.

## Tasmota Setup

 1. Flash Tasmota to your ESP8266
 2. Add the Stepper 28BYJ-48 Template
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

