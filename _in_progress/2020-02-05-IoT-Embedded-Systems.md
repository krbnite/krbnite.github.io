---
title: IoT (Embedded Systems)
layout: post
tags: iot edge easi
---

In this module, we take a look at embedded systems: what's the typical
interface of an embedded system?  What are its components?  Where are they 
used in the real world?

https://www.nytimes.com/2018/10/10/technology/future-internet-of-things.html

"...the economic and technical incentives of the internet-of-things industry do not align with security and privacy for society generally. Putting a computer in everything turns the whole world into a computer security threat — and the hacks and bugs uncovered in just the last few weeks at Facebook and Google illustrate how difficult digital security is even for the biggest tech companies. In a roboticized world, hacks would not just affect your data but could endanger your property, your life and even national security."

"business models for these devices don’t often allow for the kind of continuing security maintenance that we are used to with more traditional computing devices. Apple has an incentive to keep writing security updates to keep your iPhone secure; it does so because iPhones sell for a lot of money, and Apple’s brand depends on keeping you safe from digital terrors.  But manufacturers of low-margin home appliances have little such expertise, and less incentive. That’s why the internet of things has so far been synonymous with terrible security"


-----------

IoT devices are typically embedded systems -- computer-based systems that
do not appear to be computers (simple user interface hides any complexity).
* digital camera (same basic interface as an old camera, but lots more going on inside)
* TV
* cell phone

Some embedded systems do not directy interact with a human:
* USB stick 
* anti-lock breaking system

Embedded systems put a high emphasis on efficiency: it not only must
do what it's designed to do, but it must do so as efficiently as possible.  For example,
we might not fault the camera on your laptop for not being as memory efficient as
it could be, but in a low-resource device like a digital camera, inefficiency is
no longer a convenience: it must be fast, power efficient, memory efficient, etc.

Efficiency is important because these systems tend to be in cost-crticical or
power-critical devices...

The constraint set for an embedded system is much more stringent that systems deployed
on general purpose computers:
* manufacturing cost
* design cost
* performance
* power
* time-to-market

However, to offset a stringent constraint set, also note that an embedded system (like
an IoT device) is usually made to do one thing -- it is application specific.  Some
devices, like a cell phone, are exceptions to this rule.

For a GP computer, for almost any task, its potential is underutilized, e.g., you do
not need a quadcore CPU running at a few GHz to do a PowerPoint presentation.  This is
b/c the GP computer has to be ready for anything -- later on, you may be watching a movie,
while streaming on Google Hangouts and surfing the internet (with 23 tabs open).

Another aspect of embedded systems:  software and hardware co-design (GP systems usually
use hardware and software developed by different companies). So design is much harder: you
have to be both a SW and HW designer!

The embedded system diagram:
```
                     ___________________
[Sensors]-->[ADC]-->| [microcontroller] |-->[DAC]-->[Actuators]
        |__________>|   ^         ^     |______________|
                    |   v         v     |
                    | [IP] <--> [FPGA]  |
                     -------------------
```

* Sensors: Receive data from outside world
  - a microphone is a sensor
  - a webcam is a a sensor
  - a car's brake pedal is a sensor
* Actuators: Portray results or effects to outside world
  - a speaker is an actuator
  - a computer screen is an actuator
  - a car's brake lights are actuators
* IP: Intellectural Property Core
  - designed for very specific purpose
  - a reusable unit of logic or functionality 
  - expensive to make if you only need one, but cheap to mass produce
  - we won't design IP cores in this class, but what we will go over is buying ready-made ("off the shelf") IP cores
* FPGA: Field Programmable Gate Array
  - can be reconfigured for different purposes
  - complex; won't be used in this class

# Microcontrollers
A "microcontroller" can be thought of as a smaller, weaker "microprocessor".  The microcontroller
is used for specific purpose devices (embedded systems, IoT), so doesn't need to be as powerful:
usually it's slower than a microprocessor, has less memory, etc.  

Microcontrollers need to be programmed (usually in C); such programs are written on a regular 
computer (called the host, e.g., your MacBook Pro), then transferred from the host to 
the microcontroller (placed in `mctrlr` memory).


Some Components of Embedded Systems work
* development board - 
  - https://en.wikipedia.org/wiki/Microprocessor_development_board
* processors
  - GP: overdesigned; can do a little of everything; tends to be expensive
  - DSP: supports DSP functions; speed vector instructions (GPs usually have scalar instructions); cheaper, but limited https://en.wikipedia.org/wiki/Digital_signal_processor
* simple sensors
  - thermistor: reports temp
  - photoresistor: reports light intensity
  - potentiometer
* complex sensors
  - CMOS camera: captures images (special purpose light sensor)
  - ethernet controller: enables network communication (listens)
* simple actuators
  - LEDs, LCD displays
* complex actuators
  - servo motor (moves things)
  - ethernet controller:  enables network communication (output)
* ADC - Analog-to-Digital Converter
  - example: sound is an anolog quantity; when going into microphone, the pressure
    waves are converted to voltage waves, whose value are then digitized into discrete voltage 
    levels (this process is also happening with time too:  continuous time --> discrete time steps)
* DAC - Digital-to-Analog Converter
  - example: after a microphone's input has been digitized and processed (e.g., some reverb is added),
    then it must be "continuized" before blasting out of speaker
    
Things we'll be using in class:
* development board (has microcontroller w/ some code running on it)
  - Arduino
  - Rasberry Pi
  - might be interested in boards with cool things, like compasses, accelerometers,
    magnetometers, touch sensor screens, etc (though none of this is required)
* cables
  - USB cable
  - jumper wires
* inputs
  - potentiometer
  - photoresistor
  - keypad
  - buttons
* breadboard 
  - used for quick, impermanent wiring 
  - holes fit 24-gauge wiring
  - see diagram below
  
Breadboard
https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard/all

* all holes in leftside column are connected (this is your power supply and ground)
* all holes in a row are connected (this is how you connect components)
```
* *   * - * - * - * - *
| |
* *   * - * - * - * - *
| |
* *   * - * - * - * - *
| |
* *   * - * - * - * - *
```





-------------------

Some Smart Devices (& Related Stuff):
* [Home Assistant](https://www.home-assistant.io)
  - [Getting Started](https://www.home-assistant.io/getting-started/)
  - YouTube (TheHookUp): [Home Assistant Beginners Guide](https://www.youtube.com/watch?v=sVqyDtEjudk)
  - YouTube (DrZzs): [Intro to Home Assistant & Smart Home hubs: Hassio vs Alexa vs Google Home](https://www.youtube.com/watch?v=pVxoSXeC2Jw)
* [IFTTT (If This Then That)](https://ifttt.com/)
* [Arlo Security Cameras](https://www.arlo.com/en-us/default.aspx)
* [Philips Hue (Smart Lighting)](https://www2.meethue.com/en-us)
  - [Philips Hue White & Color Ambiance A19 LED Smart Bulb (Bluetooth & Zigbee Compatible)](https://www.amazon.com/dp/B07QWB3H1Q)
* [GE Enbrighten Z-Wave Plus Smart Dimmer](https://www.amazon.com/GE-Enbrighten-Repeater-SmartThings-14294/dp/B01MUCZA1C)
* [GE Enbrighten Z-Wave Plus Smart Switch](https://www.amazon.com/GE-Enbrighten-SimpleWire-SmartThings-46201/dp/B07RRBT6W5/)
* [Wyze Bulb](https://wyze.com/wyze-bulb.html)
* [Wyze Smart Home Plug](https://www.amazon.com/Wyze-Labs-WLPP1-Smart-Two-Pack/dp/B07XZT24B8)
* [Wyze Indoor Smart Home Camera](https://www.amazon.com/Wyze-1080p-Indoor-Camera-Vision/dp/B07DGR98VQ/)
* [Best smart bulbs for your connected home](https://www.techhive.com/article/3129887/best-smart-bulbs.html)
* [LampUX Smart LED WiFi Color Changing Light Bulbs](https://www.amazon.com/dp/B07PY5ZFM7/)
* [Magic Hue (RGBCW) Multicolor, Dimmable Smart Light (LED)](https://www.amazon.com/dp/B07VJL4MDH/)


