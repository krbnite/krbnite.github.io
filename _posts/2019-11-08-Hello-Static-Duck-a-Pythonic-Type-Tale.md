---
title: Hello, Static Duck! (A Pythonic Type Tale.)
layout: post
tags: python easi
---

Let me just start out by saying: I'm proud of this title!  Oftentimes I get a little lazy with
the titles.  Other times I think a boring, straightforward title is simply the way to go.  But not 
this time!  No, this time I let the morning coffee do the talking.  (Thanks, morning coffee!)

Anyway, I've been using Python 3.X forever, yet I'm ashamed to say, I haven't been using all of its 
added functionality.  In this case, it's static and hello typing.  (Shout out to the [Full Stack Deep Learning](https://www.youtube.com/playlist?list=PL1T8fO7ArWlcf3Hc4VMEVBlH8HZm_NbeB)
bootcamp videos on YouTube, where I saw "hello typing" in use about 30 minutes in this 
[lecture](https://www.youtube.com/watch?v=mmlvGLSXKLc&list=PL1T8fO7ArWlcf3Hc4VMEVBlH8HZm_NbeB&index=3).)

As you probably know, Python has traditionally been a dynamically-typed language, and for the most part it
still is.  This is contrast to statically typed languages, like C.  Short and sweet: a language is called
statically-typed if all variable types are known at compile time, and dynamically-typed if this information
is witheld until runtime.  

As convenient as dynamic typing can be when messing around and ideating, many people who write
production code find it to be horrible for things like system maintenance and predictable system
behavior.  

This is where hello typing comes in: it's still dynamic typing, but in a useful-yet-superficial way, 
a hello-typed function says "hello" to its users and other team members working on the same codebase.  This
"hello" is simple: when defining a function in Python, you toss the traditional Python syntax in 
favor of one that takes on a statically-typed appearance (again, still not statically-typed, but useful
in that it tells users and developers what you were thinking when you wrote the code).

I think an example would be most useful here.

# Traditional Fucntion Syntax in Python
```python
def morningCoffee(
    code,
    name = 'Joseph Bean',
    age = 25
):
    return [name[:code]+str(age), name, age]
```

Now, as a user of this poorly-documented code, if I look for help, this is all I get:

```python
help(morningCoffee)
    Help on function morningCoffee in module __main__:

    morningCoffee(code, name='Joseph Bean', age=25)
    (END)
```

Ok, I get that `name` is supposed to be a `str` and `age` is probably supposed to be an `int`, but
what about `code`?  This is a simple function, so it can be easily figured out.  But what if
it wasn't?  It might also be helpful to know what type of output to be expected.


# Hello Function Syntax
``python
def morningCoffee(
    code: int,
    name: str = 'Joseph Bean',
    age: int = 25
) -> [str,str,int]:
    return [name[:code]+str(age), name, age]
```

Now, while this code is still poorly documented, if I look for help, I at least get quite a
bit of functional information:

```python
help(morningCoffee)
    Help on function morningCoffee in module __main__:

    morningCoffee(code: int, name: str = 'Joseph Bean', age: int = 25) -> [<class 'str'>, <class 'str'>, <class 'int'>]
    (END)
```

This looks so statically typed, yet it's not.  Nothing is enforcing these types, just hinting at what
they should be if the code is to be used as intended. 

For example:
```python
morningCoffee(3, name = 37, age = 'Tea Time!')
  ['DonTea Time!', 'Donut Dipper', 'Tea Time!']
```

If this was statically typed, we would not be able to define `age` as a `str` since 
it would be an illegal operation to do so.  

# Static Typing
So, what if you want something that is even more in the direction of static types?  

1. Rigorously use hello typing
2. `pip install mypy`
3. At the command line:  `mypy myCode.py`

# Hello Examples with the `typing` Package
The example I gave above is too simple to really demonstrate how much you can
do with hello typing, so here's a bit more code to get you going.

```python
from typing import Dict, List, Tuple, NamedTuple, Generator, Set
from collections import namedtuple

# Completely-Horrible-But-Demonstrative Example
def myFcn(
    groceryList: Set[str],
    directionsNSWE: List[str],
    demoData: Tuple[str, str, int] = ("Joseph", "Bean", 25),
    favoriteWords: Dict[str, int] = {
        'Morning': 'The time of day when the sun rises and the rooster sings praise.',
        'Rooster': 'A male chicken.'
    },
) -> NamedTuple:
    return namedtuple()
```

My advice is to look at the [Python Cheat Sheet](https://www.pythonsheets.com/notes/python-typing.html) on all
of this.  Much better examples than I could provide!


# References
* Real Python: [Python Type Checking](https://realpython.com/python-type-checking/)
* Medium: [How to Use Static Type Checking in Python 3.6](https://medium.com/@ageitgey/learn-how-to-use-static-type-checking-in-python-3-6-in-10-minutes-12c86d72677b)
* Python Cheat Sheets:  [Python Typing](https://www.pythonsheets.com/notes/python-typing.html)



