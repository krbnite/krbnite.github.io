---
layout: post
title: Jupyter Notebook + Console
---

In my previous post, I was trying to figure out how to use Tmux to integrate a remote 
vim and iPython session, while displaying images locally.  For example, I wondered, 
"Is it possible to create a TMux session that allows one to place QTConsole side-by-side with Vim, Bash, etc?"

I actually found a good solution, but it's a bit different than how I was originally thinking.

```
1. [local]  ssh user@ip
2. [remote]  tmux
3. [remote]  <C-a>|   
3. [remote, right]  jupyter notebook --no-browser &
4. [browser] http://ip:8887
5. [browser] open a JNB
6. [remote, right]  jupyter console --existing
7. [remote, right] <C-a>h   # move to left pane
8. vim  
```

This allows me to:
1. develop python scripts, functions, libraries in Vim
2. interact w/ ipython kernel in jupyter console
3. use jupyter notebook to see visualizations, if you want
4. use notebook to develop a corresponding project log/file/etc

Heck, for exploring and developing a data story, vim is not even fully necessary.  Simply starting a JNB and
connecting to its kernel with jupyter console is often good enough.  I think one of the things I was really
missing when using JNBs is having an interactive terminal, which this gives you:

```
1. [local]  ssh user@ip  
2. [remote]  jupyter notebook --no-browser &
3. [browser] http://ip:8887 
4. [browser] open a JNB
5. [remote]  jupyter console --existing
```

To be clear, this is useful off AWS as well.  It's just damn nice to have an interactive 
console when messing around with / building a Jupyter Notebook.

```
jupyter notebook
jupyter console --existing
```
