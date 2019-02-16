---
layout: post
title: TMux + Vim + AWS
tags: vim aws tmux python
---

TMux is finally useful to me.

This is great because TMux has interested me for years.  But most times I finagled with it, 
I couldn't really justify using it other than thinking, "This feels important."

The first time it started to appear extremely useful to me was several months back.  I was working
on various projects in R and Python.  While working in R I got pretty comfy with RStudio, which has
a nice Vim mode in the editor, a nearby commandline to use R interactively, and a bunch of bells
and whistles that can be quite helpful on the fly (e.g., the command history search feature, the
ability to send bits of code from the editor to the commandline, etc).  However, while working in 
Python, I found Spyder, Rodeo, and Jupyter Notebooks to all be OK, but all lacking enough of a 
something-something to slowdown my workflow.  I managed to customize the JNBs quite a bit, but
overall they just don't feel right to interactively develop ideas and code in.  

Potential solution?  I once again came across TMux and found out I could just use Vim in one pane,
and have iPython opened in the other screen.  Then there are ways to send bits of code from Vim
to iPython...  And by fancying up your .tmux.conf file, there are ways to make the entire Tmux/Vim/Python
experience feel very vimmy in general.  

 
## Cloud Computing
Prior to training neural networks with large data sets on a GPU in the cloud, I had
 thought for that Vim tabs were kind of pointless.  I mostly relied on the buffer commands
:ls and :b for switching between scripts associated with the same project.  But often
when I wanted to check out some code in another project, I'd just cmd+T open another Bash
tab, cd to the relevant directory, and start up a new Vim session.  But having 
two Vim sessions open in two separate Bash tabs gets annoying pretty quickly
 when you can't yank-and-paste something from one Vim session into the other.
Having two independent Vim sessions open is mostly stupid, and I only continued
doing it out of some form of laziness or momentarily perceived convenience.

Cloud computing has changed all that:  when I SSH into AWS, I can't just "cmd+T" on my
MacBook Pro to open another instance of Bash in the cloud.  Instead, that just opens
another instance of Bash on my local computer.  This forced me to forgo my bad habit 
and exclusively rely on my buffer commands.  Vim tabs still seemed kind of pointless though.

However, Bash tabs are not pointless! Sure, you can do that whole ":!bashCommand" thing
in Vim, but sometimes you really want to have two instances of Bash open.  This is where
Tmux comes in: without Tmux (or a similar software) it is (to my incomplete statel of knowledge)
impossible to have two Bash tabs side-by-side.  Better than tabs, though, are panes: one pane
with a Vim session open, another with iPython, and another yet with a Bash terminal.   

Tmux breathes life into remote computing: tabs and panes are great, but there's more! 

When I programmed in IDL during my graduate studies, I'd have an IDL session open in one bash tab and Vim open in another.
Tmux could have instantly made this experience better by having two side-by-side panes.  Better, Tmux can give you consistent
keyboard commands across your open applications -- in other words, you can make everything very vimmy, and cool AF.  Instead
of using the OS's copy-and-paste command in Vim to copy some editor code into the IDL session, I could have just used y and p.
Tmux even has its own "command mode", which allows you to pivot from pane to pane, window to window.  So you never really
have to take your hands off the keyboard!  To be clear: if you like that about Vim, then you'll love that about Tmux.  Period.

Anyway, point is (1) using various commandline instances for Bash, Python, Vim, etc, is sometimes the way to go, but (2) you 
can't really do this easily without Tmux when SSH'ing into a cloud computer, so (3) Tmux is awesome.

## RStudio-ishness for Python
Basically, the basics of what you want to do is:

```bash
# 1. open a Tmux session 
tmux
# 2. open Vim in first pane
vim file
# 3. Open another pane and open iPython
<C-a>| 
iPython
```
```python
# 3. Use some iPython magic for inline plotting/images
%matplotlib qt5  # in iPython
```
The 3rd point above is not ideal... I was hoping I could use "%matplotlib inline," but that only works
for Jupyter Notebooks and Jupyter's QTConsole.  The QTConsole is awesome, but it can't exist in a
side-by-side pane in a TMux session running in Terminal on OS X.  On your local machine, you can do
the Vim/iPython/TMux thing still, but your images/plots will pop up in an external window.  Fine, but
not even this is possible when remoting into AWS.  The command above (%matplotlib qt5) will crash your
iPython session and even make things in your Bash shell screwy (at least it did for me).

I tried this in both my SSH shell and the Terminal you can generate in the browser after launching Jupyter Notebook.

Seems like a possible solution might be to set up SSH tunnels, which I already do to access Jupyter Notebooks.  But
the process seems a bit more complex --- 
[this GitHub page](https://github.com/ipython/ipython/wiki/Cookbook:-Connecting-to-a-remote-kernel-via-ssh)
seems to point in the right direction, but I haven't yet tried it.


There are other desirable features for an RStudio-like experience.  
For example, in R you can just look at a function's source code
by typing "function" into the R interpreter instead of "function(args)".  You can also easily reload a package
you are working on (devtools::install(package)), simple script (source(myScript)), or some edits you made
in the editor (highlight, ctrl+enter).  

#### In Python
* to see source code:  
```python
from inspect import getsourcelines
```
* to reload a package/library/script you are working on:  
```python
from importlib import reload
```

#### In Vim 
* use the mouse to copy-and-paste stuff from Vim to somewhere else: 
```vim
" while in Vim (will need to do every time)
:set mouse=v 
" if you like it, put it in .vimrc so its a default feature
set mouse=v  
```

#### In Tmux 
* to send a command from Vim to iPython:  <nontrivial>
 - Copying a command from Vim to python in a vimmy way requires some tweaks to your .tmux.conf file (see Appendix).

Goes like this:
```tmux
# Activate TMux's command mode
<C-a>+[   
# Copy/Yank the code you want just as you would in Vim, e.g.
v # then select section by moving around pane w/ h,j,k,l
y  # yank!
# Move from Vim pane to iPython pane, e.g.
<C-a>, <shift>k
# Paste into iPython
p
```

# Going Forward
1. [Learn more about SSH Tunnels](https://github.com/ipython/ipython/wiki/Cookbook:-Connecting-to-a-remote-kernel-via-ssh) and hooking up a local QTConsole session to AWS.
2. Is it possible to create a TMux session that allows one to place QTConsole side-by-side with Vim, Bash, etc?  
  - I suspect it is, but not using a simple Bash shell.  Probably have to use XQuartz or something similar.  
  - Not sure it is possible when remoting into AWS, which is why point \#1 is important.
3. I'd probably benefit from learning way more about [using JNBs like a ninja](https://github.com/jupyter/jupyter/wiki/A-gallery-of-interesting-Jupyter-Notebooks#entire-books-or-other-large-collections-of-notebooks-on-a-topic)

--------------------------------------------------------------------------------
--------------------------------------------------------------------------------

# Appendix

### Vimify your TMux experience
Add these commands to ~/.tmux.conf:

```bash
# Change leader key
unbind C-b
set -g prefix C-a

# Make Vimmy
setw -g mode-keys vi

# Make more Vimmy
bind Escape copy-mode
bind Enter paste-buffer
bind -t vi-copy 'v' begin-selection
bind -t vi-copy 'y' copy-selection
#--to paste: <Ldr> <Enter>

# Move between panes w/ Vimminess
#  --i.e., <Ldr>h move left a pane, etc
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Resize panes w/ vimminess
#  --i.e., <Ldr> <Shft>+h drags L-border left
bind -r H resize-pane -L 2
bind -r J resize-pane -D 2
bind -r K resize-pane -U 2
bind -r L resize-pane -R 2

# Intuitive hsplits and vsplits
bind | split-window -h
bind - split-window -v

```

### More Stuff for .tmux.conf
Play with this stuff.  I'm no expert.  This shizzle has been copied-and-pasted
from many different blogs and .tmux.conf files on GitHub.

```bash
# Allow mouse usage
set -g mouse on
set -g default-terminal "screen-256color"
### COLOUR (Solarized 256)

# default statusbar colors
set-option -g status-bg colour235 #base02
set-option -g status-fg colour136 #yellow
set-option -g status-attr default

# default window title colors
set-window-option -g window-status-fg colour244 #base0
set-window-option -g window-status-bg default
set-window-option -g window-status-attr dim

# active window title colors
set-window-option -g window-status-current-fg colour166 #orange
set-window-option -g window-status-current-bg default
set-window-option -g window-status-current-attr bright

# pane border
set-option -g pane-border-fg colour235 #base02
set-option -g pane-active-border-fg colour240 #base01

# message text
set-option -g message-bg colour235 #base02
set-option -g message-fg colour166 #orange

# pane number display
set-option -g display-panes-active-colour colour33 #blue
set-option -g display-panes-colour colour166 #orange

# clock
set-window-option -g clock-mode-colour colour64 #green
```


### Some basic TMux commands
```bash
# Add New Window  
{tmux leader}+c
# Add Named Tab:
{tmux leader}+,  # --> enter name
# Move Down (Up) a Pane:
{tl}+j  
{tl}+k
# Resize a Pane:  Smaller (Bigger):
{tl}+<shft>+j  
{tl}+<shft>+k
# HSplit a New Pane:  
{tl}+{-}
# VSplit a New Pane:  
{tl}+|
# Focus on one Pane/Window:   
{tl}+z
# Move into Tmux Copy Mode:  
{tl}+[             # just like Vim's Normal/Command mode
```


