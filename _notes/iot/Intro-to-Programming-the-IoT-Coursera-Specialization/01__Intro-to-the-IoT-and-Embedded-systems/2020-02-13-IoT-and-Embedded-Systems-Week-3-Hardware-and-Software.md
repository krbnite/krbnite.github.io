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


Storing data inside and outside a microcontroller.

We look at an example microcontroller data sheet: AVR ATmega2560
* 8-bit microcontroller
* Up to 16 MHz
* 256kB of flash memory
  - a type of non-volatile memory (i.e., you can turn off power to the device and the memory
    remains intact)
  - technically, a type of EEPROM, though distinguished from typical EEPROM since flash is
    optimized for high speed and high density at the
    cost of large erase blocks (~ 512 bytes or more) and relatively short lifetime (~10k 
    write cycles), whereas what is referred to as "EEPROM" typically has much smaller
    erase blocks and much longer write-cycle lifetime
  - typically used in a microcontroller's firmware
* 4kB EEPROM
  - electrically erasable programmable read-only memory
  - a type of non-volatile memory
  - used to store relatively small amounts of data
  - individual bytes can be erased and reprogrammed
  - flash memory is technically a type of EEPROM, though in practice "EEPROM" is reserved for
    non-volatile memory with small erase blocks (down to 1 byte) and a long lifetime (on the 
    order of 1M cycles)
  - typically used for storing parameters and history (whereas flash is used in firmware)
* 8kB SRAM
  - static random access memory (as opposed to dynamic RAM - DRAM)
  - used with moderately slow processing speeds, SRAM draws very little power and can have
    a nearly negligible power draw when idle
* Pin Diagram
  - shows map of the microcontroller's pins (where they are located) and what each one is for

Here is a datasheet for a similar device: [AVRmega640](https://ww1.microchip.com/downloads/en/devicedoc/atmel-2549-8-bit-avr-microcontroller-atmega640-1280-1281-2560-2561_datasheet.pdf)

Storage elements: you often have a speed/cost tradeoff and a power tradeoff with storage types.  For
example, you can have very fast access to memory, but it will be expensive and take up a lot of space
on the chip, whereas slower access to memory will take up very little room.  

The fastest (most expensive) storage is in a register, which stores only a single value, e.g.,
a 32-bit register stores a single 32-bit number.  A chip usually has a set of specific purpose
registers and a set of general purpose registers.  A [register file](https://en.wikipedia.org/wiki/Register_file) is a set of registers (e.g., 32 registers), which acts like memory.  

https://en.wikibooks.org/wiki/Microprocessor_Design/Register_File

Memories are meant to be much bigger than register files -- for storing a lot more, but with slower
access. 

Cache memory
* slower and cheaper than a register file
* but is still relatively fast and expensive as far as memories go in general 
* Cache memory stores like 10^5 more data than a register file, so it's relatively huge, but that 
  only amounts to about 1 Mbit of data, so not exactly "huge" in terms of other types of memory
* cache is usually on-chip memory (part of the integrated circuit)
  - in the [Harvard architecture](https://en.wikipedia.org/wiki/Harvard_architecture) we are using class, there is a data cache and an instruction cache
  - https://stackoverflow.com/questions/22394750/what-is-meant-by-data-cache-and-instruction-cache
* caches are generally used to avoid the von Neumann bottleneck 
  - system throughput is limited due to the relative ability of processors compared to top rates of data transfer
  - the limited throughput (data transfer rate) between the central processing unit (CPU) and memory compared to the amount of memory

Main memory
* very big (GBs)
* no in the CPU (not in-chip memory)
* connected to CPU via system bus
* relative to the cache and register file, memory access is slow (e.g., the difference between
  1 and 100 clock cycles for a task)


Machine Language: Processors understand machine language, e.g., x86 (Intel) processors understand x86 language.  At 
this level, the CPU instructions are in binary (or if looking at them in a text editor, you will see
them in hex). 

Assembly Language: This is a "human readable" version of machine language that employs simple mnemonics
to represent the CPU instructions (literally a one-to-one mapping between assembly and machine language). These 
are very simple instructions -- they do not include things like for loops that you may be used to from higher 
level languages (e.g., C, Python).

High Level Language:  these are the highly human readable, easy-to-use languages most of us are familiar with.
  - Compile language: code is translated once before running the code (e.g., C, C++); since code is compiled
    before runtime, everything is known at runtime and the code can run very fast; we'll be using C/C++ with
    the Arduino
  - Interpreted language: translate instructions while code is executed (e.g., Python); this translation occurs
    every time the code is run; often easier to use, but generally slower than a compiled script;  a lot is
    taken care of for you (e.g., memory management); we'll be using Python with the Rasberry Pi


Operating Systems

IoT devices do not always have an OS, e.g., the Rasberry Pi does have one, but the Arduino does not.

On a device without an OS, the code that you write (the "application") interacts directly with
the hardware; if you have multiple applications, you have to explicitly write how the hardware manages
its interactions with these applications -- but this is what an OS is for!  When a device has an OS,
the user can have many applications running, and the OS will orchestrate it.  Though it seems like
all the applications are running at the same time, this is not strictly true, e.g., the OS quickly
cycles through each running application in sequence, updating instructions, at a speed so fast it
seems like things are running simultaneously to the user.  An OS requires processing power and 
memory, and ultimately slows down the system a bit -- but it makes development way easier.

```
[ User ]
^    |
|    v
[ Application ]
^    |
|    v
[ OS ]
^    |
|    v
[ Hardware ]
```

The OS makes development much easier and more modular.  Instead of having to worry about how
different applications must work concurrently, the OS does this for you.  This means you can
develop applications independently of each other.  The main job of an OS is to support process
abstraction, where a process is an instance of a program (i.e., you can have one program that
is running for 10 users, thus have 10 processes).  Processes must have access to the CPU,
memory, and other resources (I/O, ADC, timers, network, etc).  There can be many processes running
on a system, so it's the job of the OS to manage resources fairly.

----------------------


Arduino OSs (not covered in HW)
* RTuinOS: 
  - [RTuinOS: A Real Time Operating System (RTOS) for Arduino 1.0.1](https://forum.arduino.cc/index.php?topic=138643.0)
  - [RTuinOS 1.0: Second Release of the Real Time Operating System (RTOS) for Arduino](https://forum.arduino.cc/index.php?topic=184593.0)
  - GitHub: https://github.com/PeterVranken/RTuinOS
* http://antipastohw.blogspot.com/2009/11/4-operating-systems-for-arduino.html
  - DuinOS
  - Pyxis OS
  - ArduinoMacOS
  - TaOS


Rasberry Pi OSs
* YouTube: [Top 8 Rasberry Pi Distros](Top 8 Raspberry Pi Distros)
* [The 20 Best Raspberry Pi OS Available to Use in 2020](https://www.ubuntupit.com/best-raspberry-pi-os-available/)
* [The Best Operating Systems for Your Raspberry Pi Projects](https://lifehacker.com/the-best-operating-systems-for-your-raspberry-pi-projec-1774669829)
* [Raspberry PI Operating Systems (OS) – Which one to use in 2020?](https://www.seeedstudio.com/blog/2019/10/29/raspberry-pi-operating-systems-os-which-one-to-use/)

Misc
* [What is an ARM Processor](https://whatis.techtarget.com/definition/ARM-processor)


### Microcontrollers


## HW Q1
What do the specs look like from one of the cheapest microcontrollers you can buy, and how do they compare to the specs from one of the most expensive microcontrollers you can buy?  To help answer this question, I specifically looked into AVR microcontrollers (originally developed by Atmel, now owned by Microchip as of 2016) and identified the cheapest and most expensive on the page: the ATtiny202 ($0.29/unit for 5000+ units; $0.40 for a single unit) vs the ATmega2561 ($8.51/unit for 5000+ units; $11.72 for a single uit).  


Clock Frequency
* ATtiny202: 20 MHz
* ATmega2561:  16 MHz

Bitwidth of the Datapath
* ATtiny202: 8 bit
* ATmega2561:  8 bit

Size of Flash Memory
* ATtiny202:  2KB
* ATmega2561:  256KB

Number of Pins
* ATtiny202:  8
* ATmega2561:  64

Analog-to-Digital Converter
* ATtiny202: Yes, 12-Channel 10-bit ADC
* ATmega2561:  Yes, 8-Channel 16-bit ADC

Comparing these 5 properties certainly gives some motivation for the price differential (e.g., the ATmega2561 has over 100x the Flash memory, and 8x more pins).  Other specs of interest might include:
* EEPROM: 64B (ATtiny202) vs 4KB (ATmega2561)
* SRAM: 128B (ATtiny202) vs 8KB (ATmega2561)
* Timers:  2x16-bit (ATtiny202) vs 2x8-bit + 4x16-bit (ATmega2561)

### General References 
* Definition of "bitwidth datapath" given by Professor Harris in Lecture 1.3 (Microcontroller Properties @ 3:22):  "Okay. Datapath bitwidth. Bitwidth, what that means is, it's a number of bits in each register. A register storage element to store as a number. So, the bitwidth basically tells you the size of most numbers in your system."
* AVR Microcontrollers:  https://www.microchip.com/design-centers/8-bit/avr-mcus

### ATtiny202 References
* https://www.microchip.com/wwwproducts/en/ATTINY202
* http://ww1.microchip.com/downloads/en/DeviceDoc/ATtiny202-402-AVR-MCU-with-Core-Independent-Peripherals_and-picoPower-40001969A.pdf

### ATmega2561 References
* https://www.microchip.com/wwwproducts/en/ATmega2561
* https://ww1.microchip.com/downloads/en/devicedoc/atmel-2549-8-bit-avr-microcontroller-atmega640-1280-1281-2560-2561_datasheet.pdf

------------------------------

## HW Q2

Both the Arduino and Rasberry Pi have multiple operating systems (OSs) available for use.

The use of an OS in an Arduino Uno appears to be fairly controversial since many Arduino developers consider an OS to be overkill within the typical Arduino Uno use cases.  However, controversial or not, OSs have been created for and used on the Arduino Uno.

ChibiOS (open source under GPL3/Apache2.0)
Website:  http://www.chibios.org/
Licensing:  http://www.chibios.org/dokuwiki/doku.php?id=chibios:licensing:start
Arduino port of ChibiOS:  https://github.com/greiman/ChibiOS-Arduino
Semi-recent article about ChibiOS:  https://hackaday.com/2016/09/22/arduino-sketch-the-next-generation/
YouTube Tutorial:  Arduino Real Time OS (ChibiOS):  https://www.youtube.com/watch?v=JXy86GrjVso&list=PL-VRW4ibM-f20B-dXzf6ogiwSzexkAPxY
Code to follow along with YouTube tutorial:  https://github.com/ItKindaWorks/sketches/tree/master/ChibiOS


FreeRTOS (open source under MIT open source license)
Website:  https://www.freertos.org
Licensing:  https://www.freertos.org/a00114.html
Feilipu's Arduino port of FreeRTOS (https://github.com/feilipu/Arduino_FreeRTOS_Library) and overview (https://feilipu.me/2015/11/24/arduino_freertos/) 
Several examples using Feilipu's Arduino port:  (i) https://create.arduino.cc/projecthub/feilipu/using-freertos-multi-tasking-in-arduino-ebc3cc;  (ii) https://www.mepits.com/tutorial/576/arduino/using-free-rtos-multi-tasking-in-arduino
Greiman's Arduino port of FreeRTOS:  https://github.com/greiman/FreeRTOS-Arduino


DuinOS (open source under a modified GNU General Public License (GPL))
Based on FreeRTOS
Most recent version:  https://github.com/DuinOS/DuinOS
Older versions:  https://code.google.com/archive/p/duinos/

If one considers Arduino platforms beyond the Uno, then there exist more OSs.  For example, the Arduino 101 has the Intel Arduino 101 RTOS (might be open source under LGPL, but I cannot find an exact statement on this):
Article about this: https://www.infoq.com/news/2016/04/arduino-101-fw-open-source/
Intel's blog about it: https://blog.arduino.cc/2016/04/21/intel-releases-the-arduino-101-firmware-source-code/
Source code for the Arduino 101 (referred to as ArduinoCore-arc32 in the repo), which Intel's Arduino 101 RTOS runs on: https://github.com/arduino/ArduinoCore-arc32
Licensing (LGPL) for the Arduino 101: https://github.com/arduino/ArduinoCore-arc32/blob/master/LICENSE

There exist other OS efforts on the Arduino I saw mentioned on webpages published 9+ years ago (e.g., 2009-2011), but no longer have much of a digital presence (e.g., several listed on one webpage I visited include Pyxis OS, ArduinoMacOS, and TaOS;  http://antipastohw.blogspot.com/2009/11/4-operating-systems-for-arduino.html).


Raspberry Pi Operating Systems

There are countless OS options on the Raspberry Pi -- so many, that articles dedicate themselves to the "top 10", "top 15", and even "top 20" operating systems (e.g., https://www.ubuntupit.com/best-raspberry-pi-os-available/).  This is not surprising: unlike the Arduino, the Rasberry Pi was intended to have an OS -- which OS to use depends strongly on the use case (and, of course, user preference).

Raspian (open source under GPL)
* The "official OS" of Raspberry Pi, based on Debian
* Website: https://www.raspbian.org/
* Download:  https://www.raspberrypi.org/downloads/

DietPi (open source under GPL 2.0)
* Very lightweight Debian
* Website: https://dietpi.com/
* Source Code: https://github.com/MichaIng/DietPi

LibreELEC (open source under GPL 2.0)
* This is a fork of OpenELEC, another (now defunct) open source OS available for Raspberry Pi 
* Basically, LibreELEC (and OpenELEC before it) turns Raspberry Pi into a Kodi media center 
* Website: https://libreelec.tv/
* What is Kodi (hint: formerly known as XBox Media Center):  https://www.tomsguide.com/us/what-is-kodi,review-4160.html

OSMC (open source under GPL 2.0)
* Like LibreELEC, OSMC turns Raspberry Pi into a Kodi media center
* However, unlike LibreELEC, OSMC is a fully featured OS (not ONLY a Kodi media center)
* Based on Debian
* Website: https://osmc.tv/

RISC OS (open source under Apache 2.0)
* A very idiosyncratic OS (not based on Linux or Windows) that has been used on ARM processors since 1987
* Poking around the internet, it seems like the user base is biased towards older engineers that learned this OS as a kid, or used it professionally early in their career
* Website: https://www.riscosopen.org/content
* Formerly closed, now open source:  https://itsfoss.com/risc-os-is-now-open-source/
* Who uses RISC OS (1):  https://www.raspberrypi.org/forums/viewtopic.php?t=162004
* Who uses RISC OS (2): https://www.riscosopen.org/forum/forums/5/topics/3008

Windows IoT Core (not open source)
* Very Windows-oriented; you need a Windows computer for this
* ARM compatible
* Does not appear to be open source (basically a stripped down version of Windows 10)
* About:  https://docs.microsoft.com/en-us/windows/iot-core/windows-iot-core
* Hooking up a Raspberry Pi with Windows IoT Core:  https://docs.microsoft.com/en-us/windows/iot-core/tutorials/rpi

Lakka (open source)
* Retro gaming-oriented lightweight Linux OS 
* Website: https://www.lakka.tv/

RaspBSD (open source under FreeBSD)
* Customized build of FreeBSD for single-board computers like Raspberry Pi
* Possible reason to use:  MacOS and PS4 are based off FreeBSD, so you can tinker with something similar

RetroPie (open source under GPL)
* Retro gaming-oriented OS that can be installed on top of other OSs
* Website: https://retropie.org.uk/

Ubuntu Core
* Download:  https://ubuntu.com/download/raspberry-pi

Kali Linux
* Security- and forensics-oriented OS (ethical hacking)

Kano (open source under GPLv2)
* This is an educationally-oriented OS designed specifically for teaching children how a computer works, how to write code, and how to put together some basic projects
* What is Kano OS: https://help.kano.me/hc/en-us/articles/360001546340-What-is-Kano-OS-

Chromium OS (open source)
* If all you plan on doing is browsing the web, this will basically turn your Rasberry Pi into something like a Chromebook 

HassOS (open source)
* Stripped down Linux OS for Raspberry Pi intended to run Home Assistant (i.e., this is SmartHome-focused OS)
* Home Assistant Website:  https://www.home-assistant.io/

Mozilla WebThings (open source)
* This OS also has a SmartHome focus
* https://iot.mozilla.org/


SO MANY MORE operating systems are available for Raspberry Pi:
* Ubuntu Mate (open source)
* Ubuntu Server (open source)
* PiNet (open source)
* Linutop (open source)
* OpenMediaVault (open source)
* Gentoo (open source)
* Rokos (open source, MIT)
* Minibian (open source, GPLv2)
* Chromium OS (open source)
* Alpine Linux (open source)

And this list can go technically go on and on (e.g., many more on this page alone:  https://elinux.org/RPi_Distributions).  However, it should also be noted that it seems many of the OSs you can learn and read about, including a few listed above, have become "dead projects" (no further updates or support).

-----------------------------

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

* RPi Projects:  https://www.ubuntupit.com/20-best-raspberry-pi-projects-that-you-can-start-right-now/

## Some Home Assistant Stuff
* https://github.com/home-assistant/operating-system
* https://www.reddit.com/r/homeassistant/comments/9z3nrr/whats_the_difference_between_hassio_hassbian/
* https://community.home-assistant.io/t/hass-vs-hass-io-vs-hassos/130174
* https://community.home-assistant.io/t/homeassistant-for-newcomers-what-it-is-what-is-hassio-hassos-hassbian-101-and-cookies/123004

----------------------------


AVR XMEGA: http://ww1.microchip.com/downloads/en/DeviceDoc/doc7925.pdf
https://www.kanda.com/blog/microcontrollers/avr-xmega-microcontroller-family/

Beetle: https://www.dfrobot.com/product-1075.html

