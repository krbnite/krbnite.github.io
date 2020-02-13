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


------------------------

# HW

The Apple Watch (Series 4)
To a first approximation, the interface of an Apple Watch is fairly simple and familiar feeling: like a more traditional watch, it straps on the wrist and it has a screen that displays the time (and other information).  However, the similarities with a classical watch end rapidly after this superficial consideration of appearance.  

The Apple Watch has a screen that senses both touch and force, distinguishing between a light tap and a more firm press.  These sensing features provide context-dependent capabilities, from tapping in a security code to unlock the watch, to navigating through various screens, to tracing out alphanumeric characters and punctuation that are transcribed into digital text.  For additional control over screen navigation, the Apple Watch also has (i) a physical oblong button that serves different functions depending on how it's pressed (e.g., 1 press vs 2 quick presses) and (ii) a knob (called the "Digital Crown") that can be used to scroll through and/or zoom in and out of on-screen content, and which can also be pressed as a button for additional functionality (e.g, 1 press takes you to home screen).  The Apple Watch has a microphone that allows the user to issue voice commands (activated by holding down the digital crown), dictate text messages (an option provided by an on-screen button on the associated text messaging app screen), or take phone calls.  When issuing voice commands, a voice assistant (named "Siri") will provide cues and response outputs via the onboard speaker.  Both the text messaging and phone call functionality (as well as additional features and extensions) are enabled by pairing the Apple Watch with the user's iPhone via Bluetooth or WiFi.  The Apple Watch also supports Near Field Communication (NFC), which gives the device additional functionality, e.g., to be used in place of a credit card (via Apple Pay) or as a plane boarding ticket (via an airline's app).  A haptic feedback engine along with the speaker provide context-dependent tactile and auditory cues and feedback (e.g., for an incoming phone call, a notification, and so on). Onboard sensors (specifically, an accelerometer, gyroscope, and photoplethymograph) and built-in GPS track the user's activity and heart rate throughout the day, which is summarized and presented on screen as 3 rings surrounding the clock face of the watch (further details can be accessed by pressing on the rings to bring up the associated app).  The built-in GPS allows one to navigate their surroundings, especially when paired with the user's iPhone.  Due to the overwhelming amount of functionality, the Apple Watch has a relatively short battery life as far as fitness trackers go, often requiring daily instead of weekly (or better) charging.  Depending on the version of the Apple Watch one has, many more interface features may be provided (e.g., from Series 4 on, one may use the Apple Watch as an electrocardiogram (ECG) to monitor heart health; e.g., from Series 5 on, there is onboard compass functionality).  

iLife A4 Robot Vacuum Cleaner
For my second embedded system, I've went with something much simpler than an Apple Watch: the iLife A4 Vacuum Cleaner.  This smart vacuum is just a notch above a traditional "dumb" vacuum: like a traditional vacuum, it has a power button to turn it on and off.  However, once on, it finds its way around a room on its own -- and when it's running low on battery, it automatically navigates back to its charging station. Furthermore, the suction mechanism automatically adjusts depending on surface (e.g., carpet, tile, hardwood, and laminate).  Multiple smart sensors serve to help the robot avoid bumping too hard into walls or falling down stairs.  The user may manually control the vacuum wirelessly with a standard (infrared) remote control using forward, right, and left buttons (there is no "back" button).  The remote control is also used to: (i) change the cleaning mode using the various mode button (spot cleaning, edge cleaning, small room);  (ii) program in a cleaning schedule using the "plan" and "clock" buttons;  (iii) start a cleaning session using the "clean" button;  and (iv) initiate a deep cleaning session for carpets using the "max" button.  This particular model does not connect to the internet (no WiFi connectivity), nor does it learn its environment like smarter robot vacuums do.


The visual inputs/outputs to the Apple Watch include:
* ambient light (input)
* light variations indicative of changes in blood flow (inward facing optical sensor (photoplethysmography)) (input)
* information on screen (output)

The audio inputs/outputs to the Apple Watch include:
* microphone inputs (input)
* sounds from speaker (output)

The tactile inputs/outputs to the Apple Watch include:
* touches, taps, and swipes on the screen (input)
* physical button (input)
* physical knob turning signal (input)
* haptic feedback (vibrations) (output)

The electronic (and other sensory) inputs/outputs to the Apple Watch include:
* near field communication signals (radio frequency) (input/output)
* Bluetooth signal (communication with iPhone) (input/output)
* WiFi signal (input/output)
* GPS signal (input)
* accelerations (accelerometer) (input)
* rotations (gyroscope) (input)
* variations in electrical current associated with the heart (measured by resting a finger from the opposing hand on the knob ("digital crown"), which completes the arm-to-arm circuit with a sensor on the bottom of the watch face) (input)

-------------------------------------------------------------------------------

iLife A4 Robot Vacuum Cleaner
The visual inputs/outputs (including infrared sensors for environment understanding) to the iLife A4 Robot Vacuum Cleaner include:
* the front bumper houses infrared sensors for detecting walls (input)
* drop sensors are on the underside to help detect stairs, etc (input)
* on/off light indicator (output)
* battery charge state indicator (fully charged (green), middle range charge (orange), needs charging (red)) (output)
* the remote control itself is a separate embedded system, which has a screen for visual feedback (e.g., when programming in a cleaning schedule)

The audio inputs/outputs to the iLife A4 Robot Vacuum Cleaner include:
* beeps from speaker (for turning on/off, indicating it needs charging, and arriving at the charging station) (output)

The tactile inputs/outputs to the iLife A4 Robot Vacuum Cleaner include:
* has "auto clean" button to start a cleaning session (input)
* has power button to turn robot vacuum on/off (input)
* the remote control itself is a separate embedded system, which includes physical buttons to input commands (input)
* motion (transportation, spinning brushes) (output)

The electronic (and electromagnetic signal) inputs/outputs to the iLife A4 Robot Vacuum Cleaner include:
* receives infrared signal from IR remote control (input)
* state change (on/off) (output)
* the remote control itself is a separate embedded system, which outputs infrared signals to the robot cleaner (output)

https://www.pcmag.com/reviews/ilife-a4s-robot-vacuum-cleaner

----------------------

Gaming Console

Visual (for gaming console):  the content on the screen might be considered a visual output, but that's only if you consider the TV screen as a component of the video game console, which is true for handheld devices, like Game Boy.  However, for devices like X Box, the video game console itself does not visually output the content on the screen, but outputs an electronic signal that is received by the TV, which it then visually outputs, etc.  Visual outputs of a console that do exist for most consoles include things like lights to indicate if the console is on or off.

Audio (for gaming console):  similar to visual in that the gaming music and sound effects are often outputs of a TV speaker, though the speaker on handheld devices is responsible for this type of output.  That said, some audio outputs stemming from the console itself include beeps (e.g., errors on start up, etc).  

Tactile (gaming console):  if you consider the controller as a part of the console (and not itself a separate embedded system), then the physical buttons on the controller are tactile inputs to the system.

Electronic (gaming console):  this would include WiFi signals (connection to router), Bluetooth (connection to controller), etc.

* optical variations read off the DVD by an optical sensor

DVD Player

For example, take the DVD Player.  Audio outputs include beeps (e.g., due to an error on start up) and visual outputs include the on/off indicator light.  Tactile inputs include the various buttons to play, pause, rewind, etc.  Electronic inputs include infrared signals received from the remote control; you might also classify the optical variations read off the DVD as electronic inputs (though one could argue these are visual inputs).  The list goes on.

------------------


LG Smart Washing Machine + ThinQ app

* https://lgcommunity.us.com/discussion/3933/how-to-connect-wt7300cw-washer-to-your-wifi
  - I didn't have to check "front load" instead of "topload", but I did have to pull the plug out of the wall

I now have this washing machine connected to my iPhone.  Cool, but anti-climactic.  It's not like
the clothes are going to load themselves in, then from the comfort of my bed I click "go!"

The major use case for this that I can think of is when you've left the clothes in for a day or two,
and they've probably gotten that stank -- maybe I could tap a wash cycle on that (assuming the stank
vanishes without having to go put more soap in the machine).



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



* [How the Apple Watch Works](https://electronics.howstuffworks.com/gadgets/high-tech-gadgets/apple-watch2.htm)
* Quora: [What are the most popular embedded systems?](https://www.quora.com/What-are-the-most-popular-embedded-systems)
* [Understanding of Embedded Systems](https://www.edgefxkits.com/blog/embedded-systems-with-applications/)
