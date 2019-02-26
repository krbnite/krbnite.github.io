---
title: Hello, Node.js!
layout: post
tags: webdev grow-with-google udacity wwe
---

There are no DOM or window objects in Node; no webpage you are working with!  The essence of Node is
"JavaScript for other things."  Get out of the browser and onto the server!

But why Node?  Why not just rip V8 out of chrome and put it on a server?  Sure, it would be possible to run JavaScript code, 
but you couldn't do simple print statements, e.g., the intro-level "Hello, World!" script below would not print to screen.  This is 
b/c, by itself, V8 doesn't do that kind of thing...  It doesn't have a concept of I/O.  What Node does is provide V8 with a 
non-browser hosting environment.

# Installation
If you're on Mac, then getting Node is as simple as:
```bash
brew install node
```

# Interactive Mode
Afterwards, revving up an interactive Node session is just like Python or R: just type the name!
```bash
node
```

# Running a Node Script
Interactive mode is great, but perhaps you want to write an application!

Your first server side JavaScript script:
```bash
echo 'console.log("Hello, World!");' > hello.js
node hello.js
```

`console.log` provides niceties.  We could access the stdout and stdin streams more directly in their raw form.

```bash
echo 'process.stdout.write("Hello, World!\n")' > hello2.js
node hello2.js
```

`console.log` is just a wrapper around `process.stdout.write`: it adds the newline character for you!

## Input

```bash
echo 'const name = process.argv[2];'  > input.js 
echo 'console.log("Hello, " + name);' >> input.js
node input.js Kevin
```

We used the 3rd argument on the command line (`argv[2]`) because the first is node itself (or, more specifically the path to node) and the second is the script path for input.js.  See for yourself:

```bash
# You can write this all in Vim (or whatever) / I just use echo statements
#    so you can directly copy-and-paste into your Bash shell w/ no hassle!
echo 'const node_path = process.argv[0];'  > argv.js 
echo 'const script_path = process.argv[1];'  >> argv.js
echo 'const argument_given_to_script = process.argv[2];'  >> argv.js 
echo 'console.log("Node: " + node_path);' >> argv.js
echo 'console.log("Script: " + script_path);' >> argv.js
echo 'console.log("Script Argument: " + argument_given_to_script);' >> argv.js
node argv.js 'Hello, World!'
```

# Serving an App w/ Node
Say you clone an app and want to serve it w/ Node:
```bash
git clone https://github.com/jakearchibald/wittr
cd wittr
npm install
```

You will get a ton of warnings and scary looking shame-on-you's! Luckily, these are pretty standard and harmless, and to 
(mostly/usually) nothing to worry about it.

Inside the wittr directory, you can serve the app:
```bash
# inside Wittr directory
npm run serve
```

-------------------------------------------

## References
* https://www.lynda.com/Node-js-tutorials/Real-Time-Web-Node-js/573614-2.html
* https://www.udacity.com/course/offline-web-applications--ud899
