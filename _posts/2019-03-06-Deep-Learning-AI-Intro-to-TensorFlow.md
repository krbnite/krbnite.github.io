---
layout: post
title: deeplearning.ai's Intro to TensorFlow
tags: deep-learning machine-learning tensor-flow python coursera
---

Just started working on a new-to-me TensorFlow-oriented project at work.  The project is
dusty, having been on the shelf for a year or so.  I'm also dusty having been working on
other, non-TF-y things for the past 6 months.  For the past few days, I've waded through
another man's Python code, editing, googling, and finally getting things to run -- and that's
when I signed onto LinkedIn and saw Andrew Ng's post about this new course.

Crazy timing, Ng!  Thank you.  TensorFlow has the habit of drastically changing
release to release.  For this project, I'm using an old version, but in general I want want to keep 
up with the latest version (the 15 deprecation warnings I'm currently getting are a great
motivator).  In general, I could definitely use a refresher -- so here we go!

## Fitting a Function
If I give you some simple linearly related data, you can probably pick out the formula pretty quickly.  In
class, they give the example:

```
x = -1, 0, 1, 2, 3, 4
y = -3, -1, 1, 3, 5, 7
```

You can see  the derivative dy/dx almost immediately: y increments 2 integers for each integer incremented in x. In
other words, `dy/dx = 2`.  For a linear equation, the derivative is the slope: `y = (dy/dx)x + b = 2x + b`.  So we just
have to figure out b, which is done by plugging in a (x,y) pair:  `y = -3 = 2(-1) + b = -2 + b`, which gives us
`b = -1`.  

Given any perfectly non-stochastic, low-degree polynomial function, we can find the fit in a similar way.  I say "low
degree" because no one is going to sit around trying to figure out a 10,000-degree polynomial! (Or whether it is
really a 10,001-degree polynomial :-p).  So quadratics, cubics, quartics, maybe even quintics.  

With a real data set, we likely do not have a perfectly non-stochastic function.  In other 
words, there is likely noise.  And so we actually look to fit a regression function -- some kind of "best fit". But
a best fit to what?  Do we cycle through polynomials of varying degree until the fit is perfect?  No, that's
likely not useful and will result in overfitting.  Furthermore, though many functions can be represented as polynomials, 
many cannot be -- and for those that can, it may not be a very efficient or useful representation.

Point is, the above example was easy and unrealistic.  

That said, how would we estimate the formula using Keras in TensorFlow?!


```python
import numpy as np
from tensorflow import keras

# Model
# The simplest neural network: 1 neuron
model = keras.Sequential([keras.layers.Dense(units=1, input_shape=[1])])
model.compile(optimizer = 'sgd', loss = 'mean_squared_error')

# Data
xs = np.array([-1.0, 0.0, 1.0, 2.0, 3.0, 4.0], dtype=float)
ys = np.array([-3.0, -1.0, 1.0, 3.0, 5.0, 7.0], dtype=float)

# Train/Fit Model
model.fit(xs, ys, epochs = 500)

# Predict Values (i.e., implement the learned formula)
print( model.predict( [10.0] ))
  array([[18.983627]], dtype=float32)
```

First thing you will notice is that the answer is wrong: it should be `19` since we know the
formula is `y = 2x - 1`.  And that's the second thing you should notice: we didn't necessarily 
learn the formula.  Actually, because this model is so simple, we can easily print out the
coefficients and see how the formula relates to the one we know.  But in general, with many
more layers, activation functions, and regularization techniques, the idea of learning the exact
formula breaks down -- instead, it becomes clear that we are learning a representation.  The hope is that
the learned representation models that data very well.

If you think in terms of probability and statistics, a machine learning model is almost always providing
an associational prediction -- that is, something like `output = E(Y|X=x)`.  So, the number we
got technically has some sort of confidence interval too.  However, often you'll just see the best guess
provided (without the associated uncertainty).

### Excursion 1
I liked how the instructor described the training session -- kind of like a back-and-forth conversation
between the optimizer and loss function:
* the loss function tells the optimizer how good/bad an initial guess at the correct model is
* the optimizer uses this info to generate a better guess
* rinse and repeat

Or, in his words:
> "The LOSS function measures the guessed answers against the known correct answers and measures how well or 
> how badly it did. It then uses the OPTIMIZER function to make another guess."

### Excursion 2
In the Google Colab, after showing simple code for a rules-based linear function, the instructor says:
> "So how would you train a neural network to do the equivalent task? Using data! By feeding it with a set of Xs, and a 
> set of Ys, it should be able to figure out the relationship between them. This is obviously a very different paradigm 
> than what you might be used to, so let's step through it piece by piece."

I've heard things like this in the past several years of mixing with data scientists and machine learners.  To be clear,
I've mostly only heard this by course instructors on Coursera and Udacity, not by actual data scientists or machine
learners...  Specifically, I've definitely not heard actual data-oriented 
research scientists say this... I put myself in that camp.  This paradigm is 101
to us.  Who works with data that hasn't at least worked with a linear or logistic regression?  My only guess is
that this language is reserved for web designers, or something...
  
## Google Colab
I've been meaning to try out Colab for a while, so I'm grateful I was given the opportunity here:
* [Colab for Week 1](https://colab.research.google.com/github/lmoroney/dlaicourse/blob/master/Course%201%20-%20Part%202%20-%20Lesson%202%20-%20Notebook.ipynb)

It is much like a regular Jupyter Notebook, but smoother -- everything is set up for you.  You don't even have to
think about where the computer is or whether tensorflow is installed.  Pretty cool. 

Cooler -- Colab is integrated with Google Drive, so sharing a Jupyter notebook is as simple as sharing a Google Doc.  If
Google Drive isn't your thing, you can also export the JNB to Github, or download as a local JNB.

Cooler yet -- Colab has a search bar in its margin that allows you to search for visualization code snippets.  

Also, it's free to use... That is, I wasn't just charged for using it.  And it says "free to use" in the 
[Google Colab FAQs](https://research.google.com/colaboratory/faq.html).  I'll have to push that to its limit...


## Further Refs
* https://colab.research.google.com/notebooks/welcome.ipynb
* [Getting Started w/ Google Colab](https://www.youtube.com/watch?time_continue=180&v=inN8seMm7UI)
* Colab/TensorFlow playlist: [Coding TensorFlow](https://www.youtube.com/playlist?list=PLQY2H8rRoyvwLbzbnKJ59NkZvQAW9wLbx)
* [Colab's SeedBank](https://research.google.com/seedbank/)
  - a really cool collection of interactive machine learning examples
    * [Neural Style Transfer](https://research.google.com/seedbank/seed/neural_style_transfer_with_tfkeras): Use deep learning to transfer style between images.
    * [EZ NSynth](https://research.google.com/seedbank/seed/ez_nsynth): Synthesize audio with WaveNet auto-encoders.
    * [Fashion MNIST with Keras and TPUs](https://research.google.com/seedbank/seed/fashion_mnist_with_keras_and_tpus): Classify fashion-related images with deep learning.
    * [DeepDream](https://research.google.com/seedbank/seed/deepdream): Produce DeepDream images from your own photos.
    * [Convolutional VAE](https://research.google.com/seedbank/seed/convolutional_vae): Create a generative model of handwritten digits.
* Build interactive [forms](https://colab.research.google.com/notebooks/forms.ipynb) or 
[widgets](https://colab.research.google.com/notebooks/widgets.ipynb)
* Use Colab/TF w/ [GPU](https://colab.research.google.com/notebooks/gpu.ipynb) or [TPU](https://colab.research.google.com/notebooks/tpu.ipynb)
