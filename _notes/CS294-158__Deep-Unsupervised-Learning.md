Course Instructors: Peter Abbeel, Peter Chen, Jonathan Ho, & Aravind Srinivas


* [Week 1](https://www.youtube.com/watch?v=zNmvH6OXDpk)
* [Week 2](https://www.youtube.com/watch?v=mYCLVPRy2nc)
* [Week 3](https://www.youtube.com/watch?v=NCRzGmM1ywE)
* [Week 4](https://www.youtube.com/watch?v=0IoLKnAg6-s)
* [Week 5](https://www.youtube.com/watch?v=grsO57XMJMk)
* [Week 6](https://www.youtube.com/watch?v=5NMIUZ7_nrg)
* [Week 7](https://www.youtube.com/watch?v=AC4l_MY2Dhc)
* [Week 8a,b](https://www.youtube.com/watch?v=7o9dT6puHHg)
* [Week 8c](https://www.youtube.com/watch?v=X-B3nAN7YRM)
* [Week 9a](https://www.youtube.com/watch?v=0AxgLbQfyjQ)
* [Week 9b](https://www.youtube.com/watch?v=PX11C5Vfo9U)


# Week 1

## Opening/Intro

Goal: to capture rich patterns in data using deep networks in a label-free way.
  - Generative models: recreate raw data distribution
  - Self-supervised models: "puzzle" tasks that require semantic understanding 
  
Geoffrey Hinton: "The brain has about 10^14 synapses and we only live for about 10^9
seconds. So we have a lot more parameters than data.  This motivates the idea that we
must do a lot of unsupervised learning since the perceptual input (including
proprioception) is the only place we can get 10^5 dimension of constraint per second."

Yann LeCun: Considers supervised learning to only be icing on the cake (and reinforcement
learning to be the cherry on top).  Unsupervised learning is the cake.

Applications of UDL:
* Generate novel data
* Data compression
  - e.g., WaveOne for image compression/reconstruction
* Improve downstream tasks
  - e.g., some NLP model us unsupervised pre-training, fed into supervised models...
* Flexible building blocks
  - might be re-useable in other problems

Some UDL approaches:
* Early approach (~2006): deep belief networks.
* Later approach (~2013): variational autoencoders; more computationally efficient than DBNs
* 2014: Generative adversarial networks 

WaveNet used to generate super realistic sounding voices (e.g., to read text off a webpage).

## What is an Autoregressive Model?
Type of generative model.  

Autoregresive models are used in things like machine translation.


### Maximum-Likelihood Estimation
MLE is about determining model parameters.

The idea is that you've got a data set, and you've already guessed at or decided on what the
model looks like -- that is, you've assumed some sort of functional form.  For example, you
might assume the model should be linear.  Problem is, at this point, you have an infinite
number of possible models: you need to somehow select one.  In the linear example, if you have 
D features, this means you have D+1 parameters to define.  

I purposely say "define" and not "learn" because you cannot learn the parameters (train
the model) until you've defined a learning strategy.  

MLE is a learning strategy.

The strategy is this: choose the parameters that maximize the probability of having observed
the training data.  To do this, you maximize the model's likelihood function.  

In practice,
it is easier to work with the log likelihood both theoretically and computationally.  Theoretically,
the likelihood function is a huge product of probabilities (samples are considered to be randomly 
selected and i.i.d.), while the log likelihood is a huge
sum of log probabilities; this makes differentiation easier, which is necessary to find optima. Computationally,
it is necessary because a product of many probabilities result in numbers that are generally too 
small for the computer to properly grapple with.

We said that the likelihood function is a huge product of probabilities, and that we must maximize
the likelihood function -- so why not refer to the method as "maximum probability estimation."  In
fact, you will often see the likelihood function written like:

```
L(q|data) = P(data|q)
```

Though the terms are equal, the can be interpreted very differently:
* L(q|data) is asking about the probability of the parameters given the data
* P(data|q) is asking about the probability of the data given the parameter
* we have ("been given") the data and want to know the parameters that maximize the probability, which
  is in line with the first interpretation
* the word "likelihood" is used to emphasize the first interpretation

As Wolfram Mathworld puts it: "Likelihood is the hypothetical probability that an event that has already 
occurred would yield a specific outcome. The concept differs from that of a probability in that a probability refers 
to the occurrence of future events, while a likelihood refers to past events with known outcomes."

So how does this relate to minimizing a loss function, which is what we usually discuss when
training a model in machine learning?

Basically, in some cases, minimizing your model's loss function is the same thing as maximizing 
model's corresponding likelihood function.  For example, in linear regression, minimizing the
mean squared error (MSE) is equivalent to maximizing linear regression's corresponding 
likelihood function (assuming the errors are normally distributed).  The equivalence
can be found by noting that both rely on the same parameter set to be optimized, and that
the two functions are monotonic transformations of each other.

Similarly, minimizing the cross entropy loss for a logistic regression (e.g., in a typical
binary classification problem) is equivalent to the maximizing the associated likelihood
function.

Note that loss functions need not be related to likelihood functions. Just so happens that, oftentimes, 
this is the case.  But the loss-likelihood connection is not necessarily special or the best choice
available.  Furthermore, note that the likelihood estimator itself is not necessarily special or
the best choice available.  In fact, in some cases (e.g., sample variance) it is an biased estimator,
and so in some settings not a desriable estimator.  Just some things to think about and be aware of.





#### MLE References
* [The real reason you use MSE and cross-entropy loss functions](https://www.expunctis.com/2019/01/27/Loss-functions.html)
* [Minimizing the Negative Log-Likelihood, in English](http://willwolf.io/2017/05/18/minimizing_the_negative_log_likelihood_in_english/)
* [Cross-entropy and Maximum Likelihood Estimation](https://medium.com/konvergen/cross-entropy-and-maximum-likelihood-estimation-58942b52517a)
* Penn State STAT 414/415: [Maximum Likelihood Estimation](https://newonlinecourses.science.psu.edu/stat414/node/191/)


### Likelihood-Based Models
Likelihood-based models: estimate p from sample x1, ..., xn
  - a model which is a joint distribution over data
    * in this class, a distribution is simply a function that takes in a data point (say an image)
      and spits out a number between 0 and 1, which is the probability of that data point given
      the distribution
  - learn a distribution p that allows computing p(x) for arbitrary x
    * this means that we can use this learned distribution for anomaly detection, e.g., if
      we have a data point that corresponds to a probability q < r, some threshold, then we
      have an anomaly
  - also allows sampling x from p(x)
    * generate new data points


Data compression is very related to likelihood-based modeling / generative modeling.  This is
because you are trying to find a lower-dimensional space that encodes the original data, and
so points in that lower-dimensional space should be able to predict the corresponding data...
* they talk about compression as in generating efficient codes, whereas from my signal processing
  background, I think we might call it sparse representations (i.e., the time domain representation
  of a sinusoid is not sparse, while its frequency domain representation is)
  - the idea of both is "how much do you have to write down (or store) to be able to predict
    the full contents of a sentence/something/signal?"

### Learning a Distribution
What do we want out of a learned distribution?
* we want to be able to learn it fast (i.e., it can take exponentially long)
* we want to the representation/model to be quick itself (i.e., for any data point provided,
  we would prefer that it's probability can be computed quickly)
* we want the distribution to be generalized (often called "expressive" in CS), i.e., we want
  the model to be able to handle data points it has never seen before -- we do not want to have to
  provide every possible data point during the learning/training phase

#### The Histogram
Simplest example of learning a distribution is a 1D histogram
* say you have k values, {v[1], ..., v[k]}, which can be represented by their index {1, ..., k}
* and say that you have N data points, {x[1], ..., x[N]}
* then hist(k) = count(x[i]: x[i] == v[k])/N

Would seem like histograms can be computed for anything, but not when you're confronted
with the curse of dimensionality, where counting fails (b/c for extremely large dimensional
spaces, you'll never have enough training data to fill all of the bins).

#### Function Approximation
Next best thing: function approximation. Instead of storing each probability, we can
store a parameterized function, p_{theta}(x).  The learning here is the parameter theta: 
we find the best theta s.t. `p_{theta}(x) ~= p(x)`.

Maximum Likelihood:  given a data set {x[1], ..., x[N]}, find theta by solving optimization 
problem: `arg min_{theta} loss(theta, {x[1],...,x[N]}) = (1/N)SUM(-log p_{theta}(x[i]))`
* this is equivalent to minimizing the KL divergence between the empirical distribution and the model, e.g.,
  when developing a compression model, this tells you how good the compression is

#### Deep Learning the Function Approximation
How does DL tie into all of this?  Well, we will leverage DL models as approximations of 
probability distributions of interest.
* DL models are highly expressive and have efficient computation (can take in any data point and
  compute probability estimates in reasonable time)

For computing a DL that approximates a probability distribution, it must satisfy the
seemingly benign laws of probability:
* SUM{all p(x)} = 1
* p(x) >= 1 for all x

This problem can be solved using Bayes nets: basically, set up a causal-looking DAG and use
DL to estimate the conditional probabilities between the variables. Then we have: 
`log p_{theta}(x[i]) = SUM{i=1,d: log p_{theta}(x[i] | parents(x[i]))}`.  Since any distribution
can be written as a product of conditionals, this is expressive enough... and this is what's
called an **autoregressive model**.  

NOTATIONAL NOTE: here, d stands for dimension (number of variables), while N stands for the number
of data points.  

Autoregressive model: "an expressive Bayes net structure with neural network conditional distributions
yeilds an expressive model for p(x) with tractable maximum likelihood training."


## Examples of Autoregressive Models

### A Toy Autoregressive Model w/ 2 vars
* vars: x1, x2
* model: p(x1,x2) = p(x1)p(x2|x1)
  - p(x1) is a histogram
  - p(x2|x1) is a multilayer perceptron, where the input is x1 and
    the output is a distribution over x2 (logits, followed by a softmax)
    
Does this extend well to high dimensions?
* Well, sort of ... but one function approximator per conditional can get tedious
* Also, if conditional are being trained independently, then we are likely using a sub-optimal method since
  there is no parameter sharing between conditionals
  
By tweaking this approach to include parameter sharing, we are back in business,e.g., 
* RNNs
* masking 
  - Masked MLP - MADE (Masked Autoencoder for Distribution Estimation)
  - masked convolutions, self-attention

There are certain cases where adding more layers will not help!  You will still be missing
certain statistical dependencies...

### MADE: Masked Autoencoder for Distribution Estimation
* this is an interesting way to estimate conditionals while sharing parameters
* basically, you start with an autoencoder, then you "mask" (remove) connections
  between various nodes in a way that produces a joint probability distribution
  across the output nodes
* a pic here would help: [MADE example](https://camo.githubusercontent.com/263f87f23fc1de9563d7083f924bb71540daa8b5/68747470733a2f2f7261772e6769746875622e636f6d2f6b617270617468792f7079746f7263682d6d6164652f6d61737465722f6d6164652e706e67)

### Masked Temporal (1D) Convolution 
* more info:
  - https://medium.com/the-artificial-impostor/notes-understanding-tensorflow-part-3-7f6633fcc7c7
  - https://github.com/philipperemy/keras-tcn
* pro: constant parameter count for variable-length distribution
* con: has limited receptive field (number of sequence points that current prediction is dependent on)

### WaveNet
* improves upon TCNs by using dilated convolutions (w/ exponential dilation)
* results in better expressivity
* more info
  - https://towardsdatascience.com/how-wavenet-works-12e2420ef386
  - https://www.youtube.com/watch?v=GyQnex_DK2k

### PixelCNN
* images can be flattened into 1D vectors, but are fundamentally 2D
  - we can use a masked version of ConvNet to exploit this
  - first, impose an autoregressive ordering on your 2D images (e.g., a raster scan ordering)
  - next, must develop a modeling/masking technique to obey the ordering you chose
  - PixelCNN is one such implementation
* one issue: PixelCNN has a blind spot in its receptive field
  - by construction, an infinitely deep net couldn't rectify this
  
### Gated PixelCNN
* fixes receptive field issues by using two streams of convolutions
  - the horizontal stack: 1D convolution ocurring across a row (captures things before point of interest)
  - the vertical stack: a 2D convolution... (captures things above)
* improved ConvNet architecture:  **Gated ResNet Block**


### PixelCNN++
* Moving away from SoftMax
  - we know pixels near each other are likely to co-occur
  - softmax treats each pixel/bin independently of each other when computing conditional distributions...


