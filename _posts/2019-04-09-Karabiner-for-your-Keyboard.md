---
title: Karabiner for your Keyboard
layout: post
tags: macbook vim misc easi
---

My thumb joints have been bothering me... And my wrists.  Turns out, I'm probably plugging away
at the keyboard way too much, teaching machines to learn and whatnot. 

I noticed that I often crook my wrists outward a bit to properly type on my Macbook.  I'm also
guilty of being obsessed with keyboard shortcuts for everything, which means frequent contortion
of my hands.  

To solve the first issue, I bought a [Kinesis Freestyle2 Keyboard](https://kinesis-ergo.com/keyboards/freestyle2-keyboard/),
which is pretty cool as is.  I also set up a laptop stand, figuring I might as well work in good
posture while I'm at it.  

But a problem emerged... I found myself going for the inconveniently elevated laptop keys over and over, despite having
the Freestyle2 right where my hands should want to be!  

"Time to toss the training wheels," I thought, figuring it would be easy to just disable my laptop's keyboard
while an external keyboard was in use.  Nope: not easy.  I ignored it the first 2-3 times, but forum after forum 
had users saying things like, "Hey, you should check out [Karabiner](https://pqrs.org/osx/karabiner/)."  So finally
I gave it a try...

# Disable Internal Keyboard
I'll admit, still didn't figure this one out... Supposedly there is some switch or button or something.  But
I haven't seen it -- probably due to the glaring blaze of fiery awesome blinding my mortal eyeballs.

# Keyboard Remapping
The first thing I noticed after downloading and opening [Karabiner](https://pqrs.org/osx/karabiner/) is that 
it gives you the power to modify your internal keyboard, as well as any external keyboards,
individually or all at once.  It is truly amazing, so follow along!

## Too much space, not enough command
One thing about the Kinesis Freestyle2 Keyboard that bugs me is where the left "command" button is -- just a bit more
left than I'd like!  In its usual spot is a left hand space bar, which I find unnecessary since the right hand
has one -- and the right space bar is the only one I use. 

"Hmm, maybe I can map the left space bar to the command key..."  Short answer: no.  Slightly longer answer: it's
possible if you're willing to go to hell and back.  

That's a setback, but not all was lost.


## Better Directional Pad: right_command + hjkl (Vimmy!)
Another thing that I thought would be nice is to be able to use the arrow keys, but without having
to move my hands from keyboard to the arrow keys (on the Freestyle2, the arrow keys are far away).  Well,
this feature was immediately available in Karabiner as an example of a "complex Modification."

```
1. Open up Karibiner-Elements.
2. Click on tab "Complex Modifications"
3. Click on "Add Rule" at bottom left.
4. Enable the option: "Change right_command+hjkl to arrow keys"
```

## Even Better Directional Pad: ctrl + hjkl (Vimmier!)
Ok, cool.  Definitely a better location with `right_command + hjkl`... But do I really have to hold on to the command
key with my thumb to move the cursor around with h/j/k/l?

Answer: No. I can instead hold down `control` with "Vi Mode".

```
1. Click on "Add Rule" on "Complex Modifications" tab.
2. Click on "Import More Rules from the Internet"
3. Look for and import "Vi Mode [left_control + hjkl]"
4. ...click through various "yes, ok, do it" things...and "Enable" this feature.
```

Very awesome -- this doesn't have the weird thumb bend on my Kinesis2, and I never use ctrl w/ hjkl except
in Vim insert mode.  So, great!  It's like having Vim insert mode everywhere!

Since there is no reason to have both these features enabled, I disabled the first one above (command + 
hjkl)... But, hey, you can also keep both.  Whatever works for you!

NOTE: one setback I found with the keys mapped like this is when open up Vim, which I have open up
with a NERDTree nav pane.  Before I remapped the keys, I was able to switch between the NERDTree
nav pane and my main editor pane using ctrl+l and ctrl+h.  Now I can't... Not sure how I'll
resolve this.

## Beyond Cursor Navigation: Better Scrolling 
Sometimes arrow keys move the cursor around a text box.  Other times, the arrow keys will scroll
a page.  But what if you want to scroll through a text box?  

The "System-Wide Vimium" option gives you the feel of Vim's command mode: simply press `escape` and
use the normal keys to move around in Vim:  h, j, k, l, ctrl+u, ctrl+d, and so on.  


```
1. Click on "Add Rule" on "Complex Modifications" tab.
2. Click on "Import More Rules from the Internet"
3. Look for and import "System-wide Vimium"
4. ...click through various "yes, ok, do it" things...and "Enable" this feature.
```

To my mind, the only lacking feature is having to press `escape` to go into Vimium mode:  it's ok on my internal, but horrible on my Kinesis2.  

I wondered, "Couldn't I just hit `right_command` to enter Vimium mode?"  

While on the Karabiner "Complex Modifications" webpage, I saw that you can choose to inspect the Karabiner
JSON file associated with the modification.  It actually looks simple enough to make this modification, but
there was a much simpler solution: I simply mapped the `escape` key itself to the `right_command` key.

```
1. Click on "Add item" on "Simple Modifications" tab.
2. Click on the new "From key" dropdown bar and select "escape"
3. Click on the associated "To key" dropdown bar and select "right_command"
```

## Beyond Cursor Navigation and Scrolling: Mouse Keys
With `ctrl + hjkl` and system-wide Vimium features enabled, the keyboard was feeling hot.  In addition
to my knowledge of Mac, Chrome, and other application-specific keyboard shortcuts, it felt like I could do 
almost anything... Anything but move the damn mouse!

I googled how to do this and found that it should be possible (https://support.apple.com/en-us/HT204434),
but I couldn't get it to work...

No worries though: Karabiner to the rescue!

The most complex thing you can personally do is concoct a list of "simple modifications," which is
what I did first.

```
left_arrow --> mouse_left
down_arrow --> mouse_down
up_arrow --> mouse_up
right_arrow --> mouse_right
right_option --> button1 ("left click")
page_down --> button2 ("right click" / "two finger click")
```

This mapped awesomely to my Kinesis Freestyle2 Keyboard, but not so perfectly to the Macbook's
internal keyboard (no "page down" next to directional keys for "right click").  Also, the mouse pointer
moved kind of slow... A possible fix was to choose a faster mouse speed:

```
left_arrow --> mouse_left (fast)
down_arrow --> mouse_down (fast)
up_arrow --> mouse_up (fast)
right_arrow --> mouse_right (fast)
right_option --> button1 ("left click")
page_down --> button2 ("right click" / "two finger click")
```

The mouse pointer now moved pretty fast... Almost too fast sometimes.  And it had poor resolution, making
it hard to pinpoint specific locations on the screen.  There are 0.5x and 2x speed modification buttons,
which I tried...but it started feeling complex.  I also grew weary of moving my hand to the directional 
keys in general:  can I just get another hjkl mapping for the mouse?   

Somebody had to have already made a "complex modification" for all of this, right?!

Right.

## Better Mouse Keys (and scrolling!)
On the Karabiner's "complex modifications" page, there are two options:  
* one of them, you cmd+m to turn on the hjkl keys (among others)
  - you have to hold on to cmd for this
  - releasing cmd ends the mouse session
* the other, you hold down the "d" key, then use the hjkl keys (among others)

I chose the second option b/c it seemed like that would make for a more seamless experience... So far, it has been nice.

```
While holding "d", "d+f" (fast mode), or "d+g" (slow mode)...
* h,j,k,l: move mouse pointer left, down, up, right

While holding "d" down...
* v, b, n:  left, middle, right mouse click

While holding "d+s", "d+s+f" (fast), "d+s+g" (slow)...
* h,j,k,l:  scroll left, down, up, right
```

# Final Set Up
The d-mode version of mouse keys gives hjkl scrolling and pointer features, and I have `crtl+hjkl` mapped
to the arrow keys (e.g., for moving cursor around a text box).  These cover everything I needed and wanted... 

I found no real need to keep "System-wide Vimium" activate, which I was largely using for scrolling...  I already know the Mac and Chrome keyboard shortcuts well enough, so Vimium mode is basically unnecessary.   Another weakness of "System-wide Vimium" is that it uses `escape` for going into Vimium/Command Mode...  Turns out in various applications, you can get odd behaviors b/c it is still interpreting the `escape` key as the `escape` key, while you may also be going into Command Mode...   `escape` was overall a poor choice, despite it making it as Vimmy as possible....

Moral: get "Mouse keys Mode v4" and "right_control + hjkl"

# Recap
Final reference for my sanity:

```
While holding "d", "d+f" (fast mode), or "d+g" (slow mode)...
* h,j,k,l: move mouse pointer left, down, up, right

While holding "d" down...
* v, b, n:  left, middle, right mouse click

While holding "d+s", "d+s+f" (fast), "d+s+g" (slow)...
* h,j,k,l:  scroll left, down, up, right

While holding right "ctrl" down...
* h,j,k,l:  arrow keys (e.g., move cursor left, down, up, right)
```
