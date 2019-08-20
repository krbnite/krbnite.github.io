---
title: Playing with Hyperparameters
layout: post
tags: deep-learning machine-learning easi
---

In the latest edition ([Aug 14, 2019](https://info.deeplearning.ai/the-batch-optimization-tutorial-plus-greener-ai-better-recommenders-generative-models-claiming-patents))
of [The Batch](https://www.deeplearning.ai/thebatch/), Andrew Ng's AI newsletter, there is a 
[hyperparameter/optimization tutorial](https://www.deeplearning.ai/ai-notes/optimization/) that 
includes a bunch of interactive tools to help develop an intuition for optimization.  For example,
there is one tool that helps show how a batch size of 1, 24, or the entire data set will affect
the convergence and trajectory of the cost function's value while seeking its minimum.  The same tool
allows one to tweak the learning rate (small, medium, large) and training set size as well.  

The really cool interactive tool, however, is at the end of the article.  It allows you to choose from a variety of cost
function landscapes and compare how different optimizers traverse the space given some learning rate, learning 
rate decay, and starting position (weight initialization).  It was interesting to see the differences in 
gradient descent, momentum, RMSProp, and Adam, in terms of trajectory, solution speed, and robustness across 
landscapes.  In this short article, I just wanted to record some of my observations.

(Btw, another cool tool is [TensorFlow Playground](https://playground.tensorflow.org/), which I found incredibly
helpful a few years ago when I was learning this stuff.)

------------------------------------------

