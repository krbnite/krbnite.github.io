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
The first thing I noticed after downloading and opening[Karabiner](https://pqrs.org/osx/karabiner/) is that 
it gives you the power to modify your internal keyboard, as well as any external keyboards,
individually or all at once.  

## Too much space, not enough command
One thing about the Kinesis Freestyle2 Keyboard that bugs me is where the left "command" button is -- just a bit more
left than I'd like!  In its usual spot is a left hand space bar, which I find unnecessary since the right hand
has one -- and the right space bar is the only one I use. 

"Hmm, maybe I can map the left space bar to the command key..."  Short answer: no.  Slightly longer answer: it's
possible if you're willing to go to hell and back.  

That's a setback, but not all was lost.


## Better Directional Pad: Take 1
Another thing that I thought would be nice is to be able to use the arrow keys, but without having
to move my hands from keyboard to the arrow keys (on the Freestyle2, the arrow keys are far away).  Well,
this feature was immediately available in Karabiner as an example of a "complex Modification."

```
1. Open up Karibiner-Elements.
2. Click on tab "Complex Modifications"
3. Click on "Add Rule" at bottom left.
4. Enable the option: "Change right_command+hjkl to arrow keys"
```
