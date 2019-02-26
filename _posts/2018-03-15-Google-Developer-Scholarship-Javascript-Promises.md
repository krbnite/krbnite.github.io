---
title: Google Developer Scholarship&#58; JavaScript Promises
layout: post
tags: webdev grow-with-google udacity wwe
---

To best understand JavaScript promises, one should first understand what a "callback" is,
and also what it is to be in "callback hell."

# Callbacks
In JavaScript, functions are first-class objects: they can be used just like a string, number,
or any other object.  This means they can be stored in variables, or be returned by another
function, or passed as an argument to a function.

When a function is passed as an argument to another function, the input function is called a 
"callback."  Callbacks help to make asynchronous code: basically, a callback is a function that is executed
if some other condition is met.  

From [JavaScriptIsSexy](http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/):
> A callback function, also known as a higher-order function, is a function that is passed to another function (let’s call this other function “otherFunction”) as a parameter, and the callback function is called (or executed) inside the otherFunction. A callback function is essentially a pattern (an established solution to a common problem), and therefore, the use of a callback function is also known as a callback pattern.

This is an extremely simple callback (the function is executed **after** 1 second):
```js
setTimeout(function() {
  console.log('Hi!');
}, 1000);
```

Another example of a callback:
```js
button = document.querySelector('#btn1');
button.click(function() {
  alert("You've won $1,000,000!");
});
```

A callback is just an argument to another function; it gets executed after something else 
happens, e.g., in the last case, after a button is clicked.

The `.foreach()` method of an array is another common example of callback:
```js
var numbers = [1,2,3,4,5];
numbers.foreach(x => console.log(x*x));
```

> Note that the callback function is not executed immediately. It is “called back” (hence the name) at some specified point inside the containing function’s body. ... Even without a name, it can still be accessed later via the arguments object by the containing function.

## Callback functions are Closures
Just leaving this here for later -- what is a closure?

> When we pass a callback function as an argument to another function, the callback is executed at some point inside the containing 
> function’s body just as if the callback were defined in the containing function. This means the callback is a closure.
* JavaScriptIsSexy: [Understand JavaScript Closures with Ease](http://javascriptissexy.com/understand-javascript-closures-with-ease/)


# Promises (Thenables)
Callbacks can get messy ("pyramid of doom"). The syntactically simpler and more readable way of doing
similar behavior is to use promises, which give you much better control over async activities. That
said, Promises do make use of callbacks, but they seem to be a well-structured, easy-to-read
way of doing so.

Basic outline of promises:
* first you wrap/construct a promise
* next, thenify the promise
* to be safe, catchify it too!
* optional: chainify like a boss

Here's what that looks like in code (minus additional chaining after `.catch()`):
```js
var promise = new Promise(
  function(resolve, reject) {
    if (success) {
      resolve(reward); // promise fulfilled
    } else {
      reject(shame); // promise rejected
    }
  }
).then(giveReward)
.catch(bestowShame);
```


The promise takes in an anonymous callback function with two of its own callbacks, resolve and 
reject (optional).  Inside the anonymous callback, things happen... If these things are successful,
then the promise is resolved by fulfillment of the promise.  If these things are unsuccessful,
then resolve the promise by rejecting it!  These resolutions are not actions: they are just
decisions to fulfill or reject the promise; they're corresponding actions are defined in the
subsequent `.then()` and `.catch()` methods.  

Technically, `.then()` can take up to two function arguments: 
```js
promise.then(giveReward, bestowShame)
```

However, a slightly more readable (and better way) is to chain them:
```js
promise.then(giveReward).catch(bestowShame)
```

The chaining behavior is similar to try/catch statements, and it is helpful to think
about promises as a non-blocking, async version of try/catch statements.

From the [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise):
> A Promise is a proxy for a value not necessarily known when the promise is created. It allows you to associate 
> handlers with an asynchronous action's eventual success value or failure reason. This lets asynchronous methods return 
> values like synchronous methods: instead of immediately returning the final value, the asynchronous method returns a 
> promise to supply the value at some point in the future.

As a recap, a promise can be:
* fulfilled - The promised action came into fruition
* rejected - The promised action failed to manifest
* pending - Hasn't yet been fulfilled or rejected yet
* settled - Has either been fulfilled or rejected



# Borrowed Example

This example from [scotch.io](https://scotch.io/tutorials/javascript-promises-for-dummies) is really helpful
in understanding how to construct a promise, and what the corresponding terminology means.

> Imagine you are a kid. Your mom promises you that she'll get you a new phone next week. You don't know if you will get that phone until next week. Your mom can either really buy you a brand new phone, or stand you up and withhold the phone if she is not happy :(.

```js
// Promise
var willIGetNewPhone = new Promise(
  function (resolve, reject) {
    if (isMomHappy) {
      var phone = {
        brand: 'Samsung',
        color: 'black'
      };
      resolve(phone); // fulfilled
    } else {
      var reason = new Error('mom is not happy');
      reject(reason); // reject
    }
  }
);
```

------------------------------------------------

## Some References
* Jake Archibald: [JavaScript Promises: an Introduction](https://developers.google.com/web/fundamentals/primers/promises)
* JavaScriptIsSexy: [JavaScript Callback Functions](http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/)
* JavaScriptIsSexy: [Understand JavaScript Closures with Ease](http://javascriptissexy.com/understand-javascript-closures-with-ease/)
* [JavaScript Promises for Dummies (scotch.io)](https://scotch.io/tutorials/javascript-promises-for-dummies) 
* https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming)
* http://callbackhell.com/
