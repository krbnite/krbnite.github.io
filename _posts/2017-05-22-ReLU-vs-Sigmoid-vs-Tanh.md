---
title: ReLU vs Sigmoid vs Tanh
layout: post
---

 
## In terms of performance: ReLU > SoftSign > Tanh > Sigmoid 

Glorot & Bengio [2006]: 
* Bad Sigmoid: "We find that the logistic sigmoid activation is unsuited for deep networks with random initialization 
because of its mean value, which can drive especially the top hidden layer into saturation." 
* "Sigmoid activations (not symmetric around 0) should be avoided when initializing from small random weights, because they yield 
poor learning dynamics, with initial saturation of the top hidden layer."
* "The softsign networks seem to be more robust to the initialization procedure than the tanh networks, presumably because of their 
gentler non-linearity."


## In terms of biological analogy:  ReLU > Sigmoid > Tanh

In a later paper, Glorot, Bordes, & Bengio [2011] show that the Tanh and SoftSign functions do not have necessary and desirable 
properties.  Tanh and SoftSign often do not deactivate, and it is shown both biologically and in deep nets that deactivation 
(or activation sparsity) is necessary.  L1 regularization helps with this, but ReLUs have it built in:
* "While logistic sigmoid neurons are more biologically plausible than hyperbolic tangent neurons, the latter work better for training 
multi-layer neural networks"
* "We propose to explore the use of rectifying nonlinearities as alternatives to the hyperbolic tangent or sigmoid in deep 
artificial neural networks, in addition to using an L1 regularizer on the activation values to promote sparsity and prevent 
potential numerical problems with unbounded activation. … Our experiments on image and text data indicate that training proceeds 
better when the artificial neurons are either off or operating mostly in a linear regime. "

 

## ReLus bridge a gap between ML/AI and biological neural networks
Glorot, Bordes, & Bengio [2011]: "Studies on brain energy expense suggest that neurons encode information in a sparse and distributed way (Attwell and Laughlin, 2001), 
estimating the percentage of neurons active at the same time to be between 1 and 4% (Lennie, 2003). This corresponds to a trade-off 
between richness of representation and small action potential energy expenditure. Without additional regularization, such as an 
L1 penalty, ordinary feedforward neural nets do not have this property. For example, the sigmoid activation has a steady state 
regime around 1/2 , therefore, after initializing with small weights, all neurons fire at half their saturation regime. This is 
biologically implausible and hurts gradient-based optimization (LeCun et al., 1998; Bengio and Glorot, 2010)."

 

## Further motivations and advantages of sparsity:
* Disentangle Information Flow:  a dense representation is highly entangled and difficult to understand; sparsity allows one to 
more easily track information flow

* Model Flexibility/Adaptability:  "Different inputs may contain different amounts of information and would be more conveniently 
represented using a variable-size data-structure, which is common in computer representations of information. Varying the number 
of active neurons allows a model to control the effective dimensionality of the representation for a given input and the required 
precision."

 

## ReLUs benefit from both linearity and nonlinearity!

Glorot, Bordes, & Bengio [2011]: The "only non-linearity in the network comes from the path selection associated with individual neurons being active or not. 
For a given input only a subset of neurons are active. Computation is linear on this subset: once this subset of neurons is 
selected, the output is a linear function of the input (although a large enough change can trigger a discrete change of the 
active set of neurons). The function computed by each neuron or by the network output in terms of the network input is thus 
linear by parts. We can see the model as an exponential number of linear models that share parameters (Nair and Hinton, 2010)."

 

## How to use the ReLU:  
Use L1 regularization on activation values and 2x the number of hidden units that you would w/ Tanh or Sigmoid.

Glorot, Bordes, & Bengio [2011]: "Another problem could arise due to the unbounded behavior of the activations; one may thus want to use a regularizer to 
prevent potential numerical problems. Therefore, we use the L1 penalty on the activation values, which also promotes additional 
sparsity. Also recall that, in order to efficiently represent symmetric/antisymmetric behavior in the data, a rectifier network would 
need twice as many hidden units as a network of symmetric/antisymmetric activation functions."

 

## Some References
* Glorot & Bengio [2009]: [Understanding the difficulty of training deep feedforward neural networks](http://proceedings.mlr.press/v9/glorot10a/glorot10a.pdf)
* Glorot, Bordes, & Bengio [2011]: [Deep Sparse Rectifier Neural Networks](http://proceedings.mlr.press/v15/glorot11a/glorot11a.pdf)
* Quora: [What does it mean that activation functions like ReLUs induce sparsity in the hidden units?](https://www.quora.com/What-does-it-mean-that-activation-functions-like-ReLUs-in-NNs-induce-sparsity-in-the-hidden-units)
* Cross Validated: [Relu vs Sigmoid vs Softmax as hidden layer neurons](https://stats.stackexchange.com/questions/218752/relu-vs-sigmoid-vs-softmax-as-hidden-layer-neurons)
 

----------------------------

## Unrelated/Random Quotes from my Readings
* "It has been long known (LeCun et al., 1998b; Wiesler & Ney, 2011) that the network training converges faster if its inputs are whitened – i.e., linearly transformed to have zero means and unit variances, and decorrelated."
* "Batch Normalization adds only two extra parameters per activation, and in doing so preserves the representation ability of the network."
  - this is probably why batch norms don't work well for me; extra degrees of freedom mean, at the least, more training epochs;  at worst, I'm not using enough data
* RNN as DNN: "We can see that the variance of the gradient on the weights is the same for all layers, but the variance of the backpropagated 
gradient might still vanish or explode as we consider deeper networks. Note how this is reminiscent of issues raised when 
studying recurrent neural networks (Bengio et al., 1994), which can be seen as very deep networks when unfolded through time."

 

 
