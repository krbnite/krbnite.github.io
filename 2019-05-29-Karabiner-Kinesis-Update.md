---
title: Karabiner + Kinesis Update
layout: post
tags: rsi easi
---

A little [while back](https://krbnite.github.io/Karabiner-for-your-Keyboard/), I bought a new,
ergonomic keyboard called the Kinesis Freestyle2.  In tandem, I installed Karabiner so that I can
really customize the keyboard.  Ultimately, I had found a great set up where my hands could relax
on the keys all day -- even had the mouse motion and clicks mapped! 

All was good, or so it seemed...

One huge pet peeve I've developed about the Kinesis Freestyle2 is where the left-hand "command"
button is... I use it frequently, switching through applications (command+tab, command+backtick),
copying and pasting (command+c, command+v), exiting and creating tabs in Chrome (command+w, command+t),
and on and on and on!  Unfortunately, the Freestyle2 has a huge left-hand spacebar that I never
use (right or wrong, this job is pretty exclusive to my right hand).  This makes for some pretty 
awkward left-hand thumb stretches...  I could get used to awkward, but this further unleased
hell and fury:  my left hand and arm have become weak and unresponsive!  With some experimentation,
I figured out the biggest culprit was this inconveniently placed left-hand command key.

Ok, no big deal, right?  This is where Karabiner comes in.  Just map that key somewhere else!

Few things: (1) there is the petulant "but I don't want to" feeling.  Oh well, no choice really.  (2) I've
already customized my keyboard quite a bit -- where to map it?  

In my original set up, I had the `escape` key mapped to the `caps_lock` key.  I really liked this...but it
seemed like the most obvious place to map the left-hand command key to: it's easy to access and I use the left-hand 
command key way more than `escape`.  

Done!

Or was I?  Now, any time I needed `escape`, I was reaching to its original, way far off location... Not ideal!

But now I was really in a bind: I really didn't have any keys left for one of Karabiner's simple mappings... I needed
to create a custom "complex mapping."  So I had to learn a bit!

# Karabiner Complex Mappings
First thing to know is where these are located:
```bash
cd ~/.config/karabiner/assets/complex_modifications/
```

Next, if you already have some complex modifications installed from the website, you can see how
it's done.  For example, I have "Vi mode [left_control + hjkl]" and "Mouse Keys Mode 4 (rev 1)" installed,
which I found saved as JSON files 1554824774.json and 1554846562.json, respectively.  Looking through those,
I learned how to create my own (shown below).

Basically, I had to think, "What key combo do I never use?" Answer: shift + shift.  This is also 
easy on the hands to perform.  Great!  One thing I found is that in the JSON file, there is
a primary key and modifier key -- so you can technically make this mapping as "hold on left shift, then
press right shift" or the other way around.  What I did was use both so it works either way.

```json
{
  "title": "Shift, shift, escape!",
  "rules": [
    {
      "manipulators": [
        {
          "description": "Esc = L-shift + R-shift",
          "from": {
            "key_code": "left_shift",
            "modifiers": {
              "mandatory" : [
                "right_shift"
              ]
            }
          },
          "to": [
            {
              "key_code": "escape"
            }
          ],
          "type": "basic"
        },
        {
          "description": "Esc = R-shift + L-shift",
          "from": {
            "key_code": "right_shift",
            "modifiers": {
              "mandatory" : [
                "left_shift"
              ]
            }
          },
          "to": [
            {
              "key_code": "escape"
            }
          ],
          "type": "basic"
        }
      ]
    }
  ]
}
```

As a last note, I had my directional keys mapped to mouse motions, but wasn't using them since
I've since installed the complex mapping "Mouse Keys Mode 4 (rev 1)" from the website.  I found that
having `caps_lock` mapped to `command` is pretty good, but not perfect -- there were still some awkward
finger motions involved.  So I mapped all the directional keys to `command` as well, and getting used
to all of it... 

We'll see how it goes.  So far, if today serves as a good metric, then things are looking good!  My fingers,
wrists, and forearms are all feeling fine :-)
