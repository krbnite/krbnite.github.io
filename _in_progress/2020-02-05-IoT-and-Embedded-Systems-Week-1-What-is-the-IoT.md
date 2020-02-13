---
title: IoT and Embedded Systems (Week 1: What is the IoT?)
layout: post
tags: edge iot easi
---

Having completed Phase 1 of the Intel's Edge AI scholarship with quite some
time before Phase 2 begins, I figured I'd backtrack some and get my hands
dirty with the Rasberry Pi.  This interest led me to two relevant Coursera courses: (i)
[The Raspberry Pi Platform and Python Programming for the Raspberry Pi](https://www.coursera.org/learn/raspberry-pi-platform)
and [Interfacing with the Raspberry Pi](https://www.coursera.org/learn/raspberry-pi-interface). Both
courses are a part of the same specialization by UC-Irvine: 
[An Introduction to Programming the Internet of Things (IOT)](https://www.coursera.org/specializations/iot).  It
introduces embedded systems, C programming, networking, Arduino, Rasberry Pi, and -- well -- the general makeup of the 
Internet of Things (IoT).  I figured, "Why not back waaaay up!"  

So here I am, at the first week of the first course in the sequence:  [Introduction to the Internet of Things and Embedded Systems](https://www.coursera.org/learn/iot?specialization=iot).

In this course, we cover what IoT actually is, embedded systems (interface, hardware and software components),
IoT devices, IoT operating systems, networking, and something called "MANETs".  

Let's get started.

------------------------------------------------

We start by reading [The 2018 SANS Industrial IoT Security Survey](https://forescout-wpengine.netdna-ssl.com/wp-content/uploads/2018/07/2018-SANS-Industrial-IoT-Security-Survey.pdf).

"The digital transformation of industry, infrastructure and cities has clearly begun.
Whether it’s called Industrial Internet of Things (IIoT), Industry 4.0 or digitalization,
companies are developing new business improvement strategies based on analytics,
artificial intelligence (AI) and machine learning. These efforts are widespread and farreaching."

"The term IoT broadly refers to the connection of devices—other than the typical
computational platforms (workstations, tablets and smartphones)—to the Internet. IoT
encompasses the universe of connected physical devices, vehicles, home appliances
and consumer electronics—essentially any object with embedded electronics, software,
sensors, actuators and communications capabilities—that enable it to connect and
exchange data. Within this universe, Industrial IoT (IIoT) focuses specifically on industrial
applications that are often associated with critical infrastructure, including electricity,
manufacturing, oil and gas, agriculture, mining, water, transportation and healthcare."

"These IIoT efforts will invariably lead to violations of implicit cyber
security assumptions, including well-defined perimeters and architectures, which need
to be addressed."

"Predictive maintenance and operational improvements are the primary focus of
most of their IIoT efforts. Both involve broad-based connection of existing and new
plant sensors with cloud-based solutions and service providers. Cloud connectivity
is a concern, but most companies believe they can deal with this through network
segmentation and isolation of control networks. The security of new endpoints is clearly
more troublesome. Few organizations believe they can rely on the sensors’ original
equipment manufacturers (OEMs) in this emerging market to provide secure devices. "

"The individuals
probably the most knowledgeable about IIoT implementation, the OT team, appear the
least confident in their organization’s ability to secure these devices, while company
leadership and management, including department managers, appear the most assured." LOLOLOLOL.

Facts:
* "32% of IIoT devices connect directly to Internet, bypassing traditional IT security layers."


The Industrial Internet Consortium (IIC) Vocabulary
defines an endpoint as a “component that
has computational capabilities and network
connectivity.”6
IIoT endpoints support two basic connection types:
“Direct: where the [endpoint] can either talk as a
client ... to whatever remote online application it
interfaces with or where it can be seen online as a
server; and indirect, where communication to the
IIoT is mediated by some method other than IP.”


Major takeway is "Oh fuck."

---------------------------

Internet of Things:
* Thing - anything besides acommon computer (laptop, desktop, tablet, etc)
* A refrigerator with an arduino has computational intelligence; add some network connectivity 
  (e.g., WiFi) and it's an "internet thing"
  - a regular fridge keeps stuff cold
  - a "smart fridge" does that and more (e.g., alerts you when door is ajar, when water filter
    needs replacement, when you're low on butter, recipes that match its contents); anything
    possible with local computational power and local sensors
  - a fridge "internet thing" can do all that and more (e.g., order food when stock is low, 
    lists lowest food prices from local grocers, anticipates meals and pre-emptively
    orders food, etc); anything made possible for a "smart fridge" w/ internet connectivity
* Modern cars are IoT devices
  - cars in 1950s had a simple interface (steering wheel, brakes, sound system buttons, etc)
  - modern cars have basically the same interface, but have so much computational intelligence
    "under the hood" (e.g., the anti-lock braking system has its own dedicated CPU, the
    fuel injection system, and so on)
  - this simple interface is characteristic of IoT devices
  - the most modern of cars are networked (e.g., you can remote start a car through the internet,
    call 911, etc)
 
Internet access gives simple devices access to computation and data not available on
the device itself (i.e., from the cloud).

RFID tags: improvement over barcodes.

An IoT device is a computer, but that's not its main point or main function: the computer
plays a supporting role.  Computers are general purpose, while IoT devices are very
purpose specific (both software and hardware).  A car does computation -- but only for
car-related tasks.  

IoT
* convergence of trends
  - cost of hardware (e.g., a computer in 1945 might have cost $0.5M, but a more capable
    laptop today might only run you $500; better, since IoT devices do not need the full
    general purpose power of a laptop, you'll pay much less;  this makes it easier today
    to say, "Hey, let's put a computer in this thing.")
  - size of hardware (again, the computer in 1945 might be the size of a room; now, it can
    be the size of a postage stamp)
  - computational throughput and speed (1945: 5k/s; now 18B/s)
  - internet access (1945: didn't exist; now: can access it almost anywhere)
  - wireless networking --> wireless internet access
  - data cost --> cheap, wireless internet access

Networking allows remote devices to have access to powerful servers, databases, and analytics.

What kind of IoT or smart devices are in your life:
* fridge
* microwave
* dvr
* tv
* game machine
* phone
* watch
* home automation systems
* motion sensors
* health trackers (Fitbit)
* medical devices (pace maker, insulin pump)
* RFID tags
* traffic lights
* security cameras


---------------------

Thermostats are important elements in the heating and cooling systems used to control the temperature in our houses.  The internal workings of a thermostat can vary quite a bit, but many heating systems use bimetallic switching thermostats.  However, from a user perspective, the inner workings are not so important: only the user interface.  This is similar to driving a car:  understanding what's under the hood is not essential.  Two cars can have very different engines, electrical systems, tire sizes, and so on, but still have the same user interface.   

The user interface for a thermostat is simple.  On a dial thermostat, the user simply rotates a numbered dial to set a desired temperature.  For a digital thermostat, a user inputs the desired temperature -- sometimes via keypad, but more often via up/down arrows or even a dial.   

Household thermostats were simple in the 1950s: one set the temperature on the device itself, mounted on some wall in the house.  By the 1980s, we had digital, programmable thermostats: one could set the temperature on the device itself, like the older thermostats, but could go a step further and put the set temperature on a regular, daily and/or weekly schedule.  Smart thermostats are now available: these allow you to put the set temperature on a regular schedule as well, if desirable, but has a few additional features: (i) it has the ability to learn what you like (e.g., over the first two weeks, it will learn your preferences as you manually increase and decrease the temperature throughout the day);  (ii) it has the ability to be monitored and manipulated from afar via your smartphone;  (iii) some have the ability to go into "eco" mode when you're out of the house;  (iv) some have the ability for voice control;  (v) some have the ability to be networked throughout the house using additional temperature sensors.

The ability to monitor and set your temperature from afar is certainly an improvement.  For example, if your thermostat is downstairs and you're already in bed upstairs -- it might be lazy, but it's super convenient to be able to pick up your phone from your nightstand and lower/raise the temperature.  Better, it's nice to be able to put the system into "eco mode" when you're away from the house and remember you left the heat on. 

The learning feature is of debatable importance.  For one, it isn't very difficult to develop a sensible weekly heating/cooling schedule.  It's also not clear that the learning feature truly makes things more convenient: in order for learning to take place, you do have to commit to manually tweaking the thermostat throughout the day and week.  The end result will probably look something like the schedule you would have programmed anyway.  Secondly, from an anecdotal perspective, I've personally found that it "over learns" and "over controls" the system.  For example, say at 4pm I usually have the heat in the dining room to 69*F, however one day I turn it down to 65*F because I'm feeling too hot:  I often find that after a certain amount of time, the system just kicks back to the "learned" temperature for that day and time.  

The various features of a smart thermostat allow for additional improvements not mentioned above, but these improvements come at a cost.  For example, allowing the mobile app to use location services on your phone in order to automatically have the system go into eco mode can become a privacy/security issue pretty quickly in the case of a data breach (which, let's face it, happens way too often).  

The cost of a simple dial thermostat these days is about $20-$40, while a smart thermostat can easily run $200-$400.  However, this price is not far off from what the dial thermostat cost in the 1950s:  a thermostat such as the Honeywell Round T832 day-night thermostat cost about $13 in 1950, which has the equivalent purchasing power of about $140 in 2020.  


-----------------------

If a smart speaker is hacked, prominent fears include being spied on by government ("Big Brother"), or by criminals who want to gain access to private conversations hoping to capture sensitive information, or by nefarious marketing firms with similar goals, or by hackers who want to stream your home life on internet sites for other perverted means (e.g., there are websites dedicated to streaming the footage of hacked security cameras hooked up to the internet; is there the same for smart speakers?).



SmartWatch:
The IoT watch has important features you missed, but which provide so much additional functionality (covered in next question).  Namely, an IoT Watch usually has:
* physical buttons and/or dials to navigate options and screens
* touchscreen capability

Sensors could have been fleshed out a bit:
* GPS
* accelerometer
* gyroscope
* magnetometer
* photoplethysmograph

The feature list is a bit short here.  Other features are worth mentioning:  pedometer (step counting), activity trackers (e.g., running performance), sleep monitoring and scoring, the ability to make calls or send/receive text messages, the ability to check the weather, the ability to use navigational features (e.g., find nearest restaurant and provide detailed directions), and so on.  

An important diminishment is "battery life":
* on an old, non-IoT battery-powered watch, the battery might last 2 years without any need for intervention
* on an IoT watch (e.g., Apple Watch), the battery needs to be charged every day; furthermore, if you go too long without charging (e.g., do not have access to), then the IoT watch doesn't even do the basic function of telling time

Location detection is a key concern, however the specifics can be fleshed out.  For example, by continuously recording location data, your highly personal and specific time tables and activity patterns can be leveraged against you in the case of a hacker/spy or data breach (e.g., it can be ascertained during which times of day and week are best for breaking into your house).  



