


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

### Object-Oriented Programming
Design patterns rely heavily on OOP.  Put another way, when people speak about design patterns,
they are most often talking about OOP best practices.  If you are using a non-OOP language (like
C) or style (e.g., functional), then it's likely that most design patterns no longer apply.
