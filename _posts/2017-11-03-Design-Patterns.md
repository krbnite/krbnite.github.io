---
title: Design Patterns (Notes)
layout: post
tags: python wwe
---


In Lynda's course, 
"[Programming Foundations: Design Patterns](https://www.lynda.com/Developer-Programming-Foundations-tutorials/Foundations-Programming-Design-Patterns/135365-2.html)," 
we covered the following patterns:
* Strategy 
* Observer 
* Decorator
* Singleton
* Iterator 
* Factory

Put simply, a design pattern is a solution or strategy to a given problem and context.

Other commonly used patterns that we did not cover include:
* Command
* Adapter
* Facade
* Template
* Composite
* Proxy

There are, in fact, even more patterns out there than this. Then there are compound patterns, like the MVC 
pattern (Model-View-Controller pattern).  

Important design principles that were emphasized:
* encapsulate what varies
* favor composition over inheritance
* program to an interface
* strive for loosely coupled designs
* classes should remain open for extension, and closed for modification
* a class should have only one reason to change


--------------------------------------------

I did watch over many of the pattern implementation sections, but they were in Java, which
I haven't used for years...and only ever used in an undergraduate course on computer science.  So,
at points seeing them implement the code in Java was helpful, but more often it felt 
obscure.  My goal was to learn about "design patterns" that I often hear about on the 
"[Talk Python to Me](https://talkpython.fm/)" podcast, not to learn Java.  

Fortunately, Lynda also has a course on design patterns in Python!  I purposely saved it for 
second.  I wanted to watch over the Java course first so I can just focus on the concepts
my first time through them.  During the Python course, my goal is to not only learn more conceptually,
but to get comfortable implementing the patterns in code.  So I will be going over this course
much more slowly.


----------------------------------------------

In this course, we will be going over some OO basics like inheritance, which establishes a relationship between two 
classes (e.g., parent and child), as well as structural and behavioural design patterns.

A design pattern is simply a well-known, well-thought out solution to recurring problems in software 
design.  Moreover, they are well-known strategies and form shared vocabulary among software 
developers.  They are language-agnostic: design patterns are conceptual -- they are strategies and
empirically-determined best practices.  Importantly, a design pattern is precise enough to be useful, 
but vague enough to be dynamic and customizable given new contexts and nuances.

Design patterns are important in the same way that reusing code is important: when using a design pattern, 
you are reusing hard-won experience from those who have come before you.  Sometimes we face a challenge and
we want to figure it out all on our own.  Often, we learn quite a lot from this: what works, what doesn't,
and what seemed alright, but made things tricky down the road.  This is great experience for sure, but at one
point we must approach software design in a more mature manner: it is important to understand the recurring
challenges that are faced in software design and how these challenges were best resolved.......

Furthermore, by learning design patterns, you will often be able to more quickly interpret someone else's
code on a project that you have taken over.  That is, you will either be able to understand which pattern
they used and why, or that the problem is amenable to a certain design pattern, but the coder who came before
you did not implement it.  Etc.

Interestingly, the idea of design patterns was borrowed from architecture and repurposed for software 
development.

### Creational Patterns
Creational patterns serve as a guide for creating highly-utilitarian objects in a systematic 
way.  These patterns strive for flexibility, e.g., the ability to derive different object subtypes from
the same class.

Creational patterns often employ polymorphism...

### Structural Patterns
A [structural pattern](https://en.wikipedia.org/wiki/Structural_pattern) is one meant to ease
the design by identifying simple ways implement relationships between software components.

Structural patterns often employ inheritance..

### Behavioral Patterns
A [behavioral pattern](https://en.wikipedia.org/wiki/Behavioral_pattern) is one that identifies
a communication pattern between objects and the best way to implement this pattern in code.

Behavioral patterns heavily focus on methods and their signatures...

### Interfaces
Many design patterns employ interfaces.  An interface is basically the part of a class
that is exposed to the public.  Usually the interface is understood by the public, and
you have a class inherit an interface with the intention of giving the public
something they know how to use.  

A simple example is an interface with get() and give() methods.  You could have an
email class inherit/implement this interface, as well as a bank account.  

Or, in Python, whenever you have an iterator, you always have the .__next__() method.

-------------------

## Object-Oriented Programming
Design patterns rely heavily on OOP.  Put another way, when people speak about design patterns,
they are most often talking about OOP best practices.  If you are using a non-OOP language (like
C) or style (e.g., functional), then it's likely that most design patterns no longer apply.

The basics of OOP:
* object: represents an entity in the problem or solution domain
* problem domain: consists of entities and moving parts interacting with your software
* solution domain: consists of entities and moving parts relevant to your software solution
* class: a template for a type of object, e.g., Kitty() is an object of type Cat()
  - attributes: properties and current state of an entity
  - methods: behaviors of an entity 
* Inheritance: establishes a relationship between two classes as parent and child
  - Child Class 
    * keeps the attributes and methods of its parent
    * can add new attributes or methods of its own 
    * can override existing methods of its parent
  - Example: 
    * say you have a Pet() class with a speak() method
    * say that Cat() is a child class of Pet()
    * automatically, this commits the Cat() class to have a speak() method
    * from the programmer's perspective, it commits one to making a choice about the speak() method: keep the default or override it
* Polymorphism
  - relies on inheritance
  - allows a child class to be regarded as the same type as its parent
  - basically, it allows you to pass a parameter to a class at runtime, which determines which subtype it will be

## Pattern Context
You cannot just employ design patterns willy-nilly.  You must develop a sense of where different design
patterns work best.  It must become a part of your vocabulary and socialization: most people do not go to the
beach in a tuxedo or to a wedding in a bathing suit.  From context, we know how to dress and behave.  That's not
to say that we shouldn't be flexible in our dress and behavior: if you were having your wedding on a beach, you 
might wear a bathing suit or a tuxedo.

A pattern context consists of:
* participants
  - refers to the classes involved to form a design pattern
  - each of these classes plays its own role in executing the pattern
* quality attributes (nonfunctional requirements)
  - usability, modifiability, reliability, performance
  - these affect the final product and so must be considered (e.g., if a certain design pattern seems to apply, but is known slow things down, you might not want to use it if your application's bread-and-butter come from its speed)
* forces
  - various trade-offs to consider
  - typically manifested in quality attributes (e.g., speed as discussed in previous bullet)
* Consequences
  - side effects
  - e.g., better performance, but worse secruity  (here "performance" and "security" are trade-offs, and "better" and "worse" are consequences of the trade-off)
  - design patterns can cause more problems than they solve if they are used incorrectly or in the wrong context

## Pattern Language
A [pattern language](https://en.wikipedia.org/wiki/Pattern_language)  consists of 
* name
* context
* problem
* solution
* related patterns

We use a pattern language to discuss the patterns in this course... Once you are familiarized with
the pattern language, you can use it to define new patterns if you so please.






