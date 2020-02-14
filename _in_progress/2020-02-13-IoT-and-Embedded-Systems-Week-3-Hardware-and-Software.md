---
title: IoT and Embedded Systems (Week 3: Hardware and Software)
layout: post
tags: iot edge embedded-systems easi
---


In this module, we're covering the basic hardware used in IoT devices and embedded systems,
and how the hardware and software typically interact in these devices (hint: they are more
in sync and integrated than found in general purpose computers specifically due to their
"special purpose" nature).  We will also cover the role of operating systems in IoT devices,
and how these differ from more familiar general purpose OS's (e.g., Linux, Windows, MacOS, Android, etc).

In IoT design, you have to think about the hardware and software development 
process together -- in step.  

Whenever you start a project, you have to give careful thought to the hardware components.  The
software you write is sensitively dependent on the hardware you've chosen.  Unlike a GP computer,
for an IoT device you are not writing software that does a whole lot independent of the hardware -- the hardware
is NOT abstracted away as if it doesn't exist, while you train a model to classify cats and dogs (or
whatever!).  Instead, you are writing software for the sole purpose of breathing life into
the hardware.

It's advisable to look at hardware data sheets when planning a project:  What type of 
power draw is necessary? What size is just too large?  How much speed do you really need?  What
is the max current on a component?  Even with proper planning, you will get things wrong
and have to order a new part or two -- but you want to minimize that!  

> "In preparing for battle I have always found that plans are useless, but planning is indispensable."
- Dwight D. Eisenhower

You do not have to understand everything on a datasheet, and first starting out -- you won't.  But 
you should be able to pick things out.  For example, the size of the component or the pin spacings (e.g.,
if your board has pin holes spaced 1/10 of an inch, then your component should have a pin spacing that is
an integer multiple of that spacing, n/10).  

There is a lot of information you won't really need, e.g., the thermal parameters (unless you are hoping
to deploy your device in extreme conditions).  

Electrical information is important.  Understanding the min/max current and voltage for a device
is necessary in properly planning how to piece together your circuit.  For example, you might
find that a component comes in several voltage ranges -- then you would select the component 
that matches your needs.  If you could not find a component with the right voltage requirements,
you might have to plan in stepping the voltage down/up with a transformer.

Datasheets for integrated circuits (ICs) can be very complicated -- up to 100's of pages!  When
using Arduino and Rasberry Pi, a lot of this is abstracted away for you, so we needn't be too
concerned about it in this class.  


IoT systems are tightly constrained, which must be taken into account when selecting a microprocessor.  In
fact, these constraints can be helping in narrowing down the selection space, which is HUGE.  Professor's advice:
be cheap!  Look through the datasheets and find the components that just barely work for your needs.

Look at all these microcontrollers:
* https://www.sparkfun.com/categories/300

In this class, we use an Arduino -- and in the next class a Rasberry Pi.  So we will not have to
pick a microcontroller.  However, this info is important in the case that I want to make my
own IoT devices after the classes are over.

SparkFun is a cool website in general:
* https://www.sparkfun.com/


Terms
* Datapath Bitwidth
  - number of bits in each register (storage of number)
  - determines accuracy and data throughput
  - note that not all systems need high accuracy (i.e., 64-bit numbers); the various projects
    we work on in this class use 8-bit numbers -- and it's just fine
* Input/Output Pins
  - limited amount of pins stemming from a microcontroller is also the bottleneck in a system
  - you can easily create a circuit that requires more pins than the microcontroller has available
  - it's important to plan early on and sketch out your design so that you have an idea of how many pins
    will likely be necessary
  - for example, you might estimate a need of 38-40 pins, then look through some datasheets and find
    a 40-pin microcontroller
* Performance
  - clock times on microcontrollers are slower than desktop 
  - for many special purpose needs a fast processor is not necessary
  - consider audio: the highest frequency a human can hear is about 20kHz; if we interpret this
    through the lens of the Nyquist frequency, then it might be accurate to say that our internal
    auditory processing is running at 40kHz;  typical processors on GP computers are around 2GHz, 
    which is 50,000x faster than 40kHz;  in other words, a fairly shitty processor (say 100kHz-1MHz)
    can process audio information much faster than a human
  - exception:  video processing (e.g., game-specific devices might higher than GP computers, e.g., 4GHz)
* Timers
  - needed for real-time applications
  - need to consider what timing accuracy you need and the bitwidth of the timer to determine which timers you should purchase/use
* Analog-to-Digital Converters
  - necessary for reading analog signals (e.g., sensors like accelerometers, gyroscope, thermometers, etc)
  - note that the Rasberry Pi doesn't come stock w/ an ADC
  - https://learn.adafruit.com/raspberry-pi-analog-to-digital-converters
  - YouTube: Ralph S Bacon: [Analog inputs for your Raspberry Pi 🥧Model 3B+](https://www.youtube.com/watch?v=x_86hTwqEMk)
  - YouTube: rdagger68: [Raspberry Pi Analog Water Sensors ADC Tutorial](https://www.youtube.com/watch?v=wJgyszOSoQU)
* Low-Power Modes
  - modes where the microcontroller is not fully on, but not fully off
  - helps save power
  - will not cover in this class
* Communication Protocol Support
  - the microcontrollers must communicate with other ICs, and they do so via some protocol (certain ordering, timing, etc)
  - e.g., UART, I2C, SPI



-------------

Watched through Wk3 L2&L3 videos... Go back and take some notes.





----------------------


# Misc

Some Arduino videos I've watched/skimmed:
* [You can learn Arduino in 15 minutes](https://www.youtube.com/watch?v=nL34zDTPkcs)
* YouTube: Eli the Computer Guy: [Arduino Introduction](https://www.youtube.com/watch?v=bY03PJihDMw)
  - playlist: https://www.youtube.com/watch?v=5rr-UmADohs&list=PLJcaPjxegjBUGZ6ao0JRoUwRlYmG9XWKf

More Playlists from Eli the Computer Guy:
* [Arduino Introduction](https://www.youtube.com/watch?v=5rr-UmADohs&list=PLJcaPjxegjBUGZ6ao0JRoUwRlYmG9XWKf)
* [Arduino](https://www.youtube.com/playlist?list=PLJcaPjxegjBUsCc8PDvalF9j9dvc1RpUh)
* [Arduino Sensors](https://www.youtube.com/watch?v=KjJFf_DnKeA&list=PLJcaPjxegjBWG6w_2uccJpXHQa3N6W-P2)
* [Arduino Vehicle](https://www.youtube.com/watch?v=l2WCKQoVdcU&list=PLJcaPjxegjBU9LLgF9dGiIhVTPHrh82N5)
* There a bunch of playlists...


