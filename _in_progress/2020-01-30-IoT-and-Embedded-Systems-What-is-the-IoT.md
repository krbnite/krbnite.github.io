---
title: IoT and Embedded Systems (What is the IoT?)
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


