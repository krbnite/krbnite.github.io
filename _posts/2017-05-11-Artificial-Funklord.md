---
layout: post
title: Artificial Funklord
tags: deep-learning machine-learning udacity wwe
---


As a kid, I loved to play Toe Jam & Earl with my brother.  The game's music was epic and inspirational, at least as far as 
we were concerned.  Later on, in the future--now the past!--we would learn some musical instruments and jam out funk-rock 
style improvs largely inspired by TJ & E sounds.

Little did guitarKev know that futureKev (currently known as nowKev) would one-up him: I’m going to show you some 
brand-funking new TJ & E music generated in the depths of an artificial intelligence whose style is so funky TJ & E would surely 
recognize this interplay of man and machine as a Funk Lord, or at least a Rap Master.

## A little bit about RNNs
Basically, easiest approach is just considering input/output of an RNN cell.  The internals can be confusing at 1st, 2nd, or 
3rd glance, but understanding the input/ouput is not so bad.  Consider for a moment that your mind is an RNN.  At each moment 
you have certain memories and thoughts, and these lead to certain actions.  Any actions taken become but a memory, which may 
be something you wish to forget -- or something you wish to tell and re-tell, and maybe embellish a little bit!  As an RNN, 
at each moment you consider your current thoughts/memories and what action you’ve just taken; this leads to taking a new 
action and impacting your thoughts/memories; rinse and repeat.

More on all this later... Have to get back to work.

## References
These links will probably be helpful: 
* https://medium.com/@shiyan/understanding-lstm-and-its-diagrams-37e2f46f1714
* https://magenta.tensorflow.org/2016/06/10/recurrent-neural-network-generation-tutorial/
* https://github.com/tensorflow/magenta/tree/master/magenta/models/melody_rnn
  - translate MIDI file into TF data format
  - simple example of music generation
* https://www.youtube.com/watch?v=4DMm5Lhey1U
* https://github.com/llSourcell/How-to-Generate-Music-Demo
