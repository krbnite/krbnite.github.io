---
title: Accuracy is not so Accurate
layout: post
tags: easi
---


Here's a weird thing: I could have sworn I wrote a post about this already, and that I called it the same: "Accuracy is
not so Accurate."  But I've looked and looked: it's not on my blog, it's not in my email, it's not...well, actually
I gave up looking after that.  But it probably would have not been in the next spot either!  Like many of my writings, it
likely exists somewhere -- but out there in a nebulous quantum state, or somewhere in the aether.  

Who knows!

What I do know is this: imbalanced classes are real, and they will mess with you if you are a fan
of blindly applying "accuracy" to every model you create.  At WWE, this is something I dealt with when working
on customer churn models.  In my current role, it has cropped up again when working on things like wakefulness detection
during sleep, or in detecting whether or not someone will commit suicide in the next several months.

In both cases, one can create a predictive model by just guessing the same thing every time:
* for sleep, just guess that during every 30-second epoch, the subject is asleep -- BAM! 95% accuracy!
* for suicide, just guess that every subject will choose to live -- BAM! 99.99% accuracy!

"Great predictions, bro.  You gonna tell me that the sun is going to rise tomorrow too?"

Oh Dear Reader, how I love your snarky, cocksure attitude!  But, yes, we're on the same page: why not predict the 
sun will rise tomorrow to boast a 100% predictive accuracy?  

Clearly, accuracy is not the metric we want in any of these cases.  Instead, one might consider balanced 
accuracy, area under the receiver-operator curve (AUROC), area under the precision-recall curve (AUPRC), 
precision, recall, F1, and so on and/or all of the above!  The pros and cons of these things, and when
they are relevant, could fill up several articles -- and are not exactly the point of this article.

Below, I simply break it down a bit more why accuracy sucks quite a bit for imbalanced classes.

-----------------------------------------------------------------------------

UPDATE: I think I found the post I was thinking of!  
* [Imbalanced Classes](https://krbnite.github.io/Imbalanced-Classes/)

-----------------------------------------------------------------------------


Polysomnography (PSG) is an instrumental set up used to measure sleep, with the intention of assessing
one's sleep quality and whether one might have some sleep disorder. One of the outputs of such a study
is a time series depicting when one was awake or asleep in bed (often in 30-second time steps, called
epochs).  Obviously, for most people, when you
go to sleep, you are asleep for most of the night.  The key is detecting when a subject is aroused 
temporarily from sleep, and how frequently this occurs.

For us, all we care about right now is that this is an imbalanced class problem, where we assume that
sleep epochs far outnumber wakeful epochs.  Here are two ways to break down how the wake/sleep class imbalance 
drives the accuracy.
 
### Approach #1: A Bunch of Algebraic Manipulations
For starters, let P = PSG Sleep, N = PSG Wake, and let TP, FP, TN, and FN correspond to how Actigraphy 
matches the PSG in the usual way.

```
Definition of Accuracy:  A = (TP+TN)/(P+N)
```

Assume the number of PSG wake epochs is some proportion of the PSG sleep epochs:   
```
N = kP, where 0 <= k <= 1
```

Note that we can then simplify accuracy’s denominator:   
```
D = P+N = P + kP = (1+k)P
```

Note that the true positives are just some proportion to the actual PSG positives:  
```
TP = aP, where 0 <= a <= 1
```

Similarly, the true negatives are just some proportion of the PSG negatives:  
```
TN = bN
```

We can further simplify this by plugging in info from above:  
```
TN = bN = bkP, where 0 <= b <= 1
```
Now we can rewrite accuracy:  
```
A = TP/D + TN/D= a(P/D) + bk(P/D)  = (a+bk)(P/D) = (a+bk) / (1+k)
```
 
So, we have:  
```
A = (a+bk)/(1+k)
```

In this study, sleep epochs make up roughly 90% of the population, leaving 10% for wake epochs.  That is, 
hat k=0.1 (10% Wake, 90% Sleep).  
 
Importantly, this imbalance bounds the accuracy: A = (a + 0.1b)/1.1.  
 
Here, b is a stand in for specificity. Assume it is 0, then A = a/1.1.  If a=0.97, then A = 0.88.  With no 
ability to detect wakeful states, we have an amazing accuracy of 88%. 
 
But if we just guessed “sleep” every time, then a=1 and b=0, giving A = 1/1.1 = 91%.  Wow, not even trying 
we did even better.  Very good work, guys!  :-p
 
What is our sensitivity was really bad, but specificity was perfect?  Let a=0.1 and b=0.95, then A = 17.7%.  Well, 
that doesn’t seem fair, does it?  We were doing so well, but have nothing to show for it.
 
All of this is to point out that accuracy was extremely inappropriate to base their conclusions off of.
 
If you care to keep going, I show another way of looking at accuracy as a linear combination of sensitivity and 
specificity below.
 
 
 
### Approach #2:  Rewriting Accuracy in terms of Sensitivity and Specificity
```
Sx = sensitivity = TP / P
Sy = specificity = TN / N
A = accuracy = (TP+TN) / (P + N) = TP/D + TN/D 
```

Rewrite accuracy
```
A = (P/P)(TP/D) + (N/N)(TN/D)
    = (P/D) (TP/P) + (N/D) (TN/N)
    = c1*Sx + c2*Sy
```

The coefficients write sleep and wake as proportion of total population of epochs, so again let’s look at a 10%/90% class imbalance.

```
A = 0.9*Sx + 0.1*Sy
```

Let’s say we have zero specificity (Sy=0) and 97% sensitivity (Sx=0.97), then A = 0.9*0.97 = 0.873.
 
You get the point!  Accuracy is meaningless for imbalanced classes like this, and in this case the classes are so imbalanced that sensitivity is almost meaningless too.  Specificity in this case is the best measure of quality for this tool:  you have very little chance of getting great specificity by chance.
 
