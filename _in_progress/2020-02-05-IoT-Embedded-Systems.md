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





