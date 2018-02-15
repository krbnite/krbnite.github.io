---
title: Google Developer Scholarship&#58; Back to the Basics of JavaScript
layout: post
---

Some of the JavaScript being thrown around in the web development course is actually a little more complex 
than I anticipated...  So I figured it might be a good time to review some JavaScript basics by taking Udacity's
[Intro to JavaScript](https://www.udacity.com/course/intro-to-javascript--ud803) course.  In this post, 
I summarize my [course notes](https://github.com/krbnite/GoogleDeveloperScholarship2018/tree/master/Round1/Supplementary-Courses/Intro-to-JavaScript).

--------------------------------------------------

# 1. Intro
## What's all this about ECMAScript?
JavaScript was originall called LiveScript, but was changed to JavaScript as a marketing decision (to piggyback off Java's popularity 
at the time). As time went on, the language evolved in multiple directions, with no one true JavaScript. This is when ECMA stepped in 
and standardized releases of JavaScript. This standardized version of JavaScript is technically called ECMAScript, but is still referred 
to as JavaScript. The ECMA prefix, however, is still retained in JavaScript version numbers. For example, ES5 is the version that has 
been in use for a while now, though some people are starting to employ ES6. To promote a more consistent release cycle, the nomenclature 
has moved in the direction of ES2016, ES2017, etc. Point is, the naming scheme of JavaScript can get confusing!

## Interactive JavaScript
### Chrome Developer Tools
There is a JavaScript console in Chrome's Developer Tools, which is a nice way for a beginner to test some lines of code. To get to 
the JavaScript Console in Chrome on a MacBook Pro, you can 2-finger click a webpage, click on "Inspect", then click on"Console" tab in 
the window that opens up. There is also a keyboard shortcut: cmd+alt+j. 

The JavaScript console in Chrome is pretty sweet for learning about your favorite webpages, which is true in general
for the Chrome Developer Tools.  Using the JavaScript console allows you to directly interact with a page's DOM, and
experiment with adding or modifying JavaScript functions on the page.  The console is also good for trying things out
in general: how to initialize a variable, define a function, etc.  However, for more advanced testing and use cases, 
Chrome's console can become less than ideal, e.g., for multiline code snippets, you must always remember to press shift+enter to
write the next line (just pressing "enter" will submit the command as if you were done typing it). 

### Node JS
Also found you could brew install Node JS, which comes with a JavaScript interpreter.


-------------------------------------------------------------

# 2. Data Types
https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/Intro-to-JavaScript/Lesson02__Data-Types-and-Variables.md

-------------------------------------------------------------

# 3. Conditionals

### If-Else Statement
Looks like JS if/else is like R's if/else.

```js
// Desire: want hammer ($15)
// In Pocket: $20
var hammer = 15
var inPocket = 20

// Do I have enough money?
if (inPocket >= hammer) {
  console.log("Buy the hammer!");
} else {
  console.log("Insufficient funds :-(");
}
```

### If-Else-If Statement
```js
var weather = 'sunny';

if (weather === 'snow') {
  console.log('Bring a coat!');
} else if (weather === 'rain') {
  console.log('Better bring a rain coat!');
} else {
  console.log('What you've got on is good enough -- let's go!');
}
```


# Logical Operators
Again, similar to R.

JS:
```js
if ((1<2) && (2<3)) { console.log('Amen!'); }
```

R:
```r
if ((1<2) && (2<3)) { print('Amen!') }
```
JS:
```js
if ((3 > 4) || (4 > 3)) { console.log('Let there be order in the universe!'); }
```
R:
```r
if ((3 > 4) || (4 > 3)) { print('Let there be order in the universe!'); }
```

JS:
```js
if (!false) {print('Ain't that the truth!);}
```

R:
```r
if (!FALSE) {print('Ain't that the truth!')}
```

### Short Circuiting
For &&, both its arguments must be true to evaluate to true. Therefore, if we compute the first expression to be false, there is no need to compute the second expression: we can "short circuit" the computation.

For ||, only one of its arguments must be true to evaluate to true. Therefore, if we compute the first expression to be true, there is no need to compute the second expression:again, we can "short circuit" the computation.

## Truthy and Falsy Values
This bit is more like what is found in Python: each value in JS has an inherent boolean value, which it is converted 
to if being evaluated in the context of a logical expression.

To be clear, from Python we often see:
```python
if var: # do something
```

Bascially, if that var exists, do something.  For example, all strings except the empty string
evaluate to true:
```python
if 'A string with text': print('Not empty!') 
  Not empty!
if '': print('Cats and dogs!')  # will not print
if None: print('Meh.')  # will not print
```

This is how things work in JavaScript too:
```js
if ('A string with text') { console.log('Not empty!'); } 
  Not empty!
if ('') { console.log('Cats and dogs!'); } // will not print
if (null) { console.log('Meh.') }  // will not print
```

Falsy JS things include: false, null, undefined, 0, '', and NaN.

The concept of truthiness is similar between JS and Python, but the particulars do not
always line up, e.g., in Python {} is an empty dictionary and is falsy, while in JS
empty curly brackets evaluate as truthy.

Python:
```python
if {}: print('Hell yea!')
  Hell yea!
```

JS:
```js
if ({}) { console.log('Hell yea!'); }  // will not print
```

As for R, it doesn't have this awesome feature.

## Ternary Operators
These seem similar in various languages... And oftentimes I forget them anyway.  But here it is
in JavaScript.

Example:
```js
var isGoing = true;
var color = isGoing ? "green" : "red";
console.log(color);
  green
```

This is a shorthand for an if-else statement.  It's pretty dang similar to IDL from what I 
remember.  Python has a similar construct.

JS:
```js
var color = isGoing ? "green" : "red";
```

Python:
```python
color = 'green' if isGoing else 'red'
```

And R has a shorthand function:
```r
ifelse(isGoing, 'green', 'red')
```

## The Switch/Case Statement
I don't know why, but I have found the switch/case syntax and terminology gets jumbled between languages.

In JS:
```js
switch(option) {
  case 1:
    console.log('Case 1');
    break; // necessary, otherwise all subsequent cases activate
  case 2:
    console.log('Case 2');
    break;
  case 3:
    console.log('Case 3');
    // no break statement necessary on final case
}
```

In IDL:
```idl
CASE <expression> OF
  expression: BEGIN
    ; statement
    END
  expression: BEGIN
    ; statement
    END
ENDCASE

; There is also a SWITCH statement in IDL...
; http://northstar-www.dartmouth.edu/doc/idl/html_6.2/SWITCH.html
```

R has a switch function:
```r
switch(expression, case1, case2, case3)
```

In Python, you just have to use if-elif-elif-else chains, or get clever with a dictionary.  


## The Strong Equality of Switch
If you're like me, you must be wondering, "Switch is cool and all, but does it evaluate
logical expression strongly --- or like a wimp?"  

We recently learned that `2=='2'` is true, while `2==='2'` is false, and that in most cases 
we likely just want to use the strong equality operator.  Then we get shown the switch statement,
and switch's strength not 100% clear from the course.

So I tested it!

```js
switch(2) {
  case 1: console.log('case 1');
  case '2': console.log('Do\'h!');
  case 2: console.log('Alright!');
}     
```

If switch was weak, we would see both "Do'h!" and "Alright!" printed to screen since I do
not use break statements. But we only see "Alright!" so switch is strong!



## Falling Through
Often when using switch, you want to use the break statement for each case.  It might seem
extraneous, but from a more positive perspective switch gives you two tools in one:
1. The "case" statement that chooses only one case:  switch-case-break
2. The "switch" statement that chooses all cases from the "on switch" forward: switch-case-continue

The "switch on" utility of the switch statement is called "falling through" in the lecture. It is
useful when you have a program flow that should run from a met-condition on, e.g.:
```js
benefits = function(tier) {
  var output='';
  switch (tier) {
    case 1: output += '\t* exclusive access to live interviews and AMAs\n';
    case 2: output += '\t* access to exclusive content and limited-time offers\n';
    case 3: output += '\t* a monthly subscription\n'
  }
  console.log('Benefits include:\n'+output);
}
```

--------------------------------------------------------

# 4. Loops
Here's an example of a while loop:
```js
var x = 9;
while (x >= 1) {
    console.log("hello " + x);
    x = x - 1;
}
```

Here's an example of a for loop that does the same thing.
```js
for (var x=9; x >= 1; x-=1) {
    console.log("hello " + x);
}
```

This for loop is kind of like how to do it in Bash...

--------------------------------------------------------------

# 5. Functions
Interestingly, functions can be written to ways.

One way is like how it's done in R, or using lambda in python. The other way is more similar to how its usually done in Python.
## Function Definition: Method 1

JavaScript:
```js
myFcn = function(x) {
  return(x*x);
}
```

This is similar to how you might define the function in R:
```r
myFcn = function(x) {
  return(x*x)
}
```

Here is a more complex comparison:

JavaScript:
```js
evenOrOdd = function(num) {
  if (number % 2 === 0) {
    console.log('even');
  } else {
    console.log('odd');
  }
}
evenOrOdd(3);
  odd
```

R:
```r
evenOrOdd = function(num) {
  if (num %% 2 == 0) {
    print('even')
  } else {
    print('odd')
  }
}
evenOrOdd(3)
  odd
```

### Side Note
I was testing JavaScript a bit more and found out you don't even need the semicolons
inside the curly brackets for this language either... Yet the instructors of the course always seem to
use them, so it must be a best practice...b/c, for example, you need them for multiline statements w/in 
curly brackets (not necessarily true in the Chrome JS console, but maybe true in general).

### Python Lambdas
You might also say it's similar to how you can define a Python function using the lambda
method:
```python
myFcn = lambda x: x*x
```

Later on in the lesson, I learned that this technique in JavaScript is called 
a "function expression."  It assigns an anonymous function to a variable.



## Function Definition: Method 2
This is the way they showed how to define a function in the course.  This technique
is called "function declaration."  All function declarations in a script a hoisted
to the top (see section on hoisting below); this is not true of function expressions.

JavaScript:
```js
function myFcn(x) {
  return(x*x);
}
```

This seems to result in the same thing, so one might wonder why there would be two
equivalent syntaxes... But then again, Python also has a second, similar method as well!

```python
def myFcn(x):
  return x*x
```

## Parameters vs Arguments
* A **parameter** is always going to be a variable name and appears in the function declaration. 
* An **argument** is always going to be a value (a number, a string, a boolean, etc.) and will always appear in the code when the function is called or invoked

* **Parameters** are variables that are used to store the data that's passed into a function for the function to use 
* **Arguments** are the actual data that's passed into a function when it is invoked

Example:
```js
function add2(x,y) { 
  var z = x + y;
  return z
}
add2(5,9)
  14
```

Here, x and y are parameters of the function `add2`, while 5 and 9 are arguments.


### Math Analogy
In terms of math, take a polynomial function: 
* f(x;a,b,c) = ax^2 + bx + c
* g(x) = f(x;1,2,3) = x^2 + 2x + 3

Here, a, b, and c are parameters of a quadratic polynomial. Without specifying them, the
function represents an infinite class of polynomials.  By specifying parameter arguments 1,
2, and 3, we select a single polynomial from the class.  

## More in the Notes
Check out my [notes on functions](https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/Intro-to-JavaScript/Lesson05__Functions.md)
for info on undefined function return, console.log vs return, global vs function scope, shadowing / scope overriding, hoisting,
function expressions, inline functional expressions...


# 6. Arrays
https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/Intro-to-JavaScript/Lesson05__Functions.md


# 7. Objects
https://github.com/krbnite/GoogleDeveloperScholarship2018/blob/master/Round1/Supplementary-Courses/Intro-to-JavaScript/Lesson07__Objects.md










# Some Misc Code
### Change the color of headings...
```js
document.getElementByTagName('h1')[0].style.color = '#ff0000';
```

### Cats Everywhere
Give power to the click: this function was used to add cat pictures to a webpage every time a user clicks.  
```js
document.body.addEventListener('click', function () {
     var myParent = document.getElementById("Banner"); 
     var myImage = document.createElement("img");
     myImage.src = 'https://thecatapi.com/api/images/get?format=src&type=gif';
     myParent.appendChild(myImage);
     myImage.style.marginLeft = "160px";
});
```
