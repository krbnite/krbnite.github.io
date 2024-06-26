---
title: Notes on Autoencoders (Decoded into English)
layout: post
tag: deep-learning autoencoders machine-learning tensorflow udacity
---

An autoencoder is designed to reconstruct its input. In a sense, a perfect autoencoder
would learn the identity function perfectly.  However, this is actually undesirable
in that it indicates extreme overfitting to the training data set.  That is, though
the autoencoder might learn to represent a faithful identity function on the training set,
it will fail to act like the identity function on new data --- especially if that new
data looks different than the training data.  Thus, autoencoders are not typically
used to learn the identity function perfectly, but to learn useful 
[representations](http://www.deeplearningbook.org/contents/representation.html)
of the input data. In fact, learning the identity function is actively resisted
using some form of regularization or constraint.  This ensures that the learned
representation of the data is useful --- that it has learned the salient features
of the input data and can generalize to new, unforeseen data.

(NOTE: find these notes and more in my [DLND repo](https://github.com/krbnite/deep-learning-nanodegree/tree/master/Notes).)

In Fourier analysis, learning a new representation is akin to mapping a time series into
the frequency domain, where we learn amplitude and phase coefficients on
a set of sinusoids that best represent the time series when summed.  However, the 
autoencoder does not enforce the components of the representation to take on 
any particular form, making its components more like empirical orthogonal functions
(more popularly known as principle components).  In fact, a simple autoencoder can
be used to mimic PCA (that is, to find a set of basis vectors that span the same space
as the orthogonal basis identified in PCA).

In the neural network lingo, an autoencoder consists of two neural networks 
(the encoder and decoder) and a loss function.  The encoder maps the input
into a latent space (a.k.a., hidden state, embedding), while the decoder 
maps the latent space back into the original data space.  The encoder
and decoder are laid out sequentially, giving the autoencoder the appearance
of a multilayer perceptron (MLP), however it differs from the MLP in an
important respect: it is unsupervised in that its output training data is
its input.  

Often, the encoder and decoder share weights, though
[this is not a requirement](https://www.quora.com/Is-weight-sharing-required-for-an-autoencoder).
If one uses weight sharing (sometimes referred to as tied weights), then
the decoder can be thought of as the matrix transpose of the encoder. A benefit
of weight sharing is that it injects some regularization into the model.

In the most general sense, an encoder can map the data vectors to latent (hidden, embedded)
vectors of the same dimension, of smaller dimension, or of higher dimension.  Most often, 
encoder are used to map the input vectors to a lower-dimensional space.  In this case,
the encoder can be thought of as a projection operator (in QM lingo), or compression scheme (in DSP lingo).  
Its job is to project N-dimensional input onto a M-dimensional space, where M < N. 

Example: One can "encode" (compress, project) the 3D representation of the 2-sphere, (x,y,z), to a
2D representation (zenith, azimuth).  Similarly, one can "decode" (reconstruct) the polar
representation into the Cartesian representation.  


## Variational AutoEncoders (VAEs)
Variational autoencoders (VAEs) are 
[the lovechild of Bayesian inference and unsupervised deep learning](http://blog.fastforwardlabs.com/2016/08/12/introducing-variational-autoencoders-in-prose-and.html):
>> "Variational Autoencoders (VAEs) incorporate regularization by explicitly learning the joint distribution over data and a set of latent variables that is most compatible with observed datapoints and some designated prior distribution over latent space. The prior informs the model by shaping the corresponding posterior, conditioned on a given observation, into a regularized distribution over latent space (the coordinate system spanned by the hidden representation)."

AEs / VAEs can be used to 
* generate images, audio, etc ([generative modeling](https://blog.openai.com/generative-models/))
* supplement reinforcement learning methods
* recover the "true, lower-dimensional manifold" that the input data lives on ([manifold learning](http://scikit-learn.org/stable/modules/manifold.html))
* denoise images
* for dimensionality reduction (pre-treatment, feature extraction)

A VAE is a stochastic autoencoder --- that is, a vanilla autoencoder is deterministic, whereas
a VAE introduces an element of stochasticity, treating its inputs, representations, and reconstructions 
as random variables within a [directed graphical model](http://blog.forty.to/2013/08/24/graphical-models-theory/).
The trick is mostly in the latent space: instead of piping a latent representation into the decoder, we use a likely
latent representation.  This is done by sampling from an approximated posterior distribution, q(z|x), where z
is the latent representation and x is the input.  The approximated posterior distribution is found by
learning the distribution's parameters (e.g., mean and stdDev for a Gaussian) at the latent layer, then
sampling from that distribution to obtain the input into the decoder.

This is a really F'n cool trick.  Think about it: in order for this autoencoder to really work well,
the network will really have to learn that posterior probability distribution.  One can then apply
Baye's theorem to get p(x|z) and sample from that distribution to generate realistic-looking synthetic 
data.  But how "realistic" is realistic?  This is where generative adversarial networks would come into
play by pitting a discriminator against this generator, allowing the two adversaries to compete, 
try to learn each other's tricks, and try to outwit each other.  Theoretically, the generator will
eventually learn to generate synthetic data that is so realistic looking that the discriminator can't
tell the difference between real and fake data.  

There are lessons to be learned here, folks: 
1. In long-enough game of cops (discriminators) and robbers (generators), the robbers will ultimately outwit the cops.  
2. Virtual reality (generator) will one day be indistinguishable from regular ol' reality to our senses (discriminator).
3. Regular ol' reality could already be a virtual reality... We all lay in pergatory with a VR headset on, where
a psychologist is evaluating whether we get to go to the loony bin or the VIP tropical resort.

Anyway, this trick makes gradient descent's eyes cross, so another trick is introduced: the reparameterization trick!
Instead of sampling z from q(z|x) ~ N(m,s^2), we define z as m + s\*e, where e ~ N(0,1).

Viewing a VAE as both a probabilistic model and a deep neural network is important:
* as a probabilistic graphical model, we ground the VAE is solid mathematical theory
* as a neural network, we give the VAE all the computational benefits that have been created for deep learning

---------------------------

XKCD: [Bayes Theorem](https://xkcd.com/1236/)

------------------------------------

## Probabilistic Model Perspective
A VAE can be considered a generative process (probability model) that emits observables, x, based on unobservable 
internal changes (i.e., latent variables), z.  (I'm mixing in some QM perspective here.)

The emission (generative process) of an observable, x[i], is stochastically dependent on the internal state, z. This dependence
is called the likelihood, and is written: x[i] ~ p(x|z).
A particular internal state (set of latent variables), z[i], follow a probability distribution, p(z), called the prior: z[i] ~ p(z).  
The model can be written as a joint probabibility distribution over observables (data) and internal states 
(latent variables): p(x,z) = p(x|z)p(z).

We can measure the observables, but not the internal state. We see a rock fall and the moon in the sky,
and we think: "Didn't Newton develop a single 1-inch equation that describes both these things?"  

From a data perspective, we have the input data (observables) and want to know 
how to compress the data (reduce its dimensionality) without losing too much information.  Our assumption
is that the data's dimensionality can be squeezed quite a bit --- that, like the 2-sphere in Cartesian 
coordinates, the current representation includes informational redundancies. 
In practical terms, these redundancies require more data storage and larger bandwidth.

So the goal is to infer the internal state (latent variables) given what we know about the observables 
(input data). That is, we want to calculate the posterior using Baye's Theorem: p(z|x) = p(x|z)p(z)/p(x).  

...

Anyway, the "encoder" is called an "inference network" in this lingo, and it is used to find the best parametrization, L,
of the approximate posterior, q(z|x,L; W), where z is the latent variable, x is the input, L is
the parametrization (of the chosen distribution family, e.g., mean and stdDev for a normal distribution), and W 
represents the inference network parameters (i.e., neural network weights and biases for the encoder).

The "decoder" is called a "generative network."

For more info mapping between probabilistic modeling lingo and neural network lingo,
re-read "[What is a Variational Autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)"


### Kullback-Leibler Divergence
The [KL Divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence)
measures how different one probability distribution is from another expected distribution. 
It is known as [relative entropy](http://mathworld.wolfram.com/RelativeEntropy.html) in 
physics.

Basically, we often approximate stochastic processes as drawing from a familiar probability 
distribution, like a Normal or Log-Normal distribution.  For example, if an empirical
distribution looks normal, then it can be quite informative to discuss two parameters 
(mean and standard deviation) rather than all the specific values and nuances in the data set.
It might also be possible to infer certain results about a normal-looking data set by 
assuming its normality.  However, what do we lose by making these assumptions?

The KL divergence, D(p,q), can help inform us about how much information is discarded by using
a familiar distribution to approximate an empirical one.  

D(p,q) = E{log(p[i]/q[i])} = SUM{ p[i] * (log(p[i]) - log(q[i])) }

It is an error metric.  Interestingly,
it is not a distance metric because it is not symmetric: D(p,q) != D(q,p).

By simultaneously minimizing the KL divergence and number of model parameters, one attempt
to find a faithful, simple model for a data set.  For example, if a 3-parameter model
has just as good a KL divergence as a 6-parameter model, its likely that the 3-parameter
model should be used.  But what if a 2-parameter model has just a slightly bigger KL
divergence?  Careful thought would help here.  If one has enough data, it'd probably be
good to compute on multiple subsets and see what holds up.  ...

#### ELBO: Evidence Lower BOund
The KL divergence is not directly computable.  However, one can bound it by the ELBO.
Then, by optimizing the ELBO, one has optimized the KL divergece...

Again, can't stress how good this article is:  [What is a Variational Autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)

I twould be nice to re-visit it multiple times, and even extend it (e.g., w/ quantum mechanical lingo, or DSP).


## VAEs in TensorFlow
* [Variational Autoencoder in TensorFlow](https://jmetzen.github.io/2015-11-27/vae.html)
* [Categorical Variational Autoencoders using Gumbel-Softmax](http://blog.evjang.com/2016/11/tutorial-categorical-variational.html)

### Simple Autoencoder (Notes from Lecture)
We will create a simple autoencoder for MNIST digits.  Clearly, a convolutional/deconvolutional net
would likely do well here, but we're developing a simple test case of an autoencoder, and will
simply flatten the 28x28 images into 784-element vectors and use fully connected layers.

```python
%matplotlib inline  # if using Jupyter Notebook or Jupyter QTConsole
import tensorflow as tf
import matplotlib.pyplot as plt
# Get Data
from tensorflow.examples.tuturials.mnist import input_data
mnist = input_data.read_data_sets('MNIST_data', validation_size=0)

# Show an example MNIST digit
img = mnist.train.images[2]
plt.imshow(img.reshape((28,28)), cmap='Greys_r')

# Definte code size (hidden layer dimensionality)
encoding_dim = 32
image_size = mnist.training.images.shape[1]

# Construct i/o placeholders
inputs_  = tf.placeholder(tf.float32, shape=[None,image_size], name="inputs")
targets_ = tf.placeholder(tf.float32, shape=[None,image_size], name="targets")

# Create hidden layer output as a fully-connected layer
#  -- default to relu activation
#  -- options: 
#     a. create using raw tf code
#     b. use tf.layers.dense
#     c. use tf.contrib.keras.layers.Dense
#
encoded = tf.layers.dense(inputs_, encoding_dim, activation=tf.nn.relu)

# Create pre-output layer (i.e., the logit layer)
#  -- the logit layer is fully connected w/ no activation
#  -- the logit layer is used by the loss function, 
#     tf.nn.sigmoid_cross_entropy_with_logits, to train the autoencoder 
#  -- after training, to view image reconstructions (the autoencoder output),
#     we have to manually apply the sigmoid activation to the logit layer
logits = tf.layers.dense(encoded, image_size, activate=None)
decoded = tf.nn.sigmoid(logits, name="output")

# Loss function
#  -- use cross_entropy loss, e.g., 
#     tf.nn.sigmoid_cross_entropy_with_logits
loss = tf.nn.sigmoid_cross_entropy_with_logits(labels=targets_, logits=logits)
cost = tf.reduce_mean(loss)
opt = tf.train.AdamOptimizer(0.001).minimize(cost)

# Training
sess = tf.Session()
epochs = 20
batch_size = 200
sess.run(tf.global_variables_initializer())
for e in range(epochs):
    for idx in range(mnist.train.num_examples//batch_size):
        batch = mnist.train.next_batch(batch_size)
        # Autoencoders have same input & output!
        feed = {inputs_: batch[0], targets_: batch[0]}
        batch_cost, _ = sess. run([cost, opt], feed_dict=feed)
        print("Epoch: {}/{}...".format(e+1, epochs),
            "Training Loss: {:.4f}".format(batch_cost))
 
# Figures
fig, axes = plt.subplots(nrows=2, ncols=10, sharex=True, sharey=True, .........)
in_imgs = mnist.test.images[:10]
reconstructed, compressed = sess.run([decoded, encoded], feed_dict={inputs_:.......})
for images, row in zip([in_imgs, reconstructed], axes):
    for img, ax in zip(images, row):
        ax.imshow(img.reshape((28,28), cmap='Greys_r')
        ax.get_xaxis().set_visible(False)
        ax.get_yaxis().set_visible(False)
fig.tight_layout(pad=0.1)

# Close TF Session
sess.close()
```


### Convolutional Autoencoder


```python
%matplotlib inline
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt

# Data
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets('MNIST_data', validation_size=0)

# Example Image
img = mnist.train.images[2]
plt.imshow(img.reshape((28,28)), cmap='Greys_r')

#
learning_rate = 0.001
inputs_ = tf.placeholder(tf.float32, (None,28,28,1), name="inputs")
targets_ = tf.placeholder(tf.float32, (None,28,28,1), name="targets")

### Encoder
# The image begins as 28x28x1:  
conv1 = tf.layers.conv2d(inputs_, filters=16, kernel_size=(3,3), \
    padding='same', activation=tf.nn.relu)
#  -- 28x28x16 after 1st conv layer
maxpool1 = tf.layers.max_pooling2d(conv1, pool_size=(2,2), strides=(2,2), padding='same')
#  -- 14x14x16 after max pooling 
conv2 = tf.layers.conv2d(maxpool1, 8, (3,3), padding='same', activation=tf.nn.relu)
#  -- 14x14x8 after 2nd conv layer
maxpool2 = tf.layers.max_pooling2d(conv2, (2,2), (2,2), padding='same')
#  -- 7x7x8 after max pooling
conv3 = tf.layers.conv2d(maxpool2, 8, (3,3), padding='same', activation=tf.nn.relu)
#  -- still 7x7x8
encoded = tf.layers.max_pooling2d(conv3, (2,2), (2,2), padding='same')
#  -- the final image code is 4x4x8

### Decoder
#  -- the code begins as 4x4x8
upsample1 = tf.image.resize_nearest_neighbor(encoded, (7,7))
#  -- 7x7x8 after upsample
conv4 = tf.layers.conv2d(upsample1, 8, (3,3), padding='same', activation=tf.nn.relu)
#  -- still 7x7x8
upsample2 = tf.image.resize_nearest_neighbor(conv4, (14,14))
#  -- 14x14x8 after upsampling
conv5 = tf.layers.conv2d(upsample2, 8, (3,3), padding='same', activation=tf.nn.relu)
#  -- still 14x14x8
upsample3 = tf.image.resize_nearest_neighbor(conv5, (28,28))
#  -- 28x28x8 after upsampling
conv6 = tf.layers.conv2d(upsample3, 16, (3,3), padding='same', activation=tf.nn.relu)
#  -- 28x28x16 after deconvolution 

### Logits and Decoded Image
logits = tf.layers.conv2d(conv6, 1, (3,3), padding='same', activation=None)
#  -- 28x28x1 after deconvolution
decoded = tf.nn.sigmoid(logits, name="decoded")

### XE Loss, Cost, and Optimization
loss = tf.nn.sigmoid_cross_entropy_with_logits(labels=targets_, logits=logits)
cost = tf.reduce_mean(loss)
opt = tf.train.AdamOptimizer(learning_rate).minimize(cost)

### Train
sess = tf.Session()
epochs = 20
batch_size = 200
sess.run(tf.global_variables_initializer())
for e in range(epochs):
    for idx in range(mnist.train.num_examples // batch_size):
        batch = mnist.train.next_batch(batch_size)
        imgs = batch[0].reshape((-1, 28, 28, 1))
        batch_cost, _ = sess.run([cost, opt], feed_dict = {inputs_: imgs, targets_: imgs})
        print("Epoch: {}/{}...".format(e+1, epochs),
            "Training Loss: {:4f}".format(batch_cost))
 
 ### Figs
 # same as above
 sess.close()
 ```
 
 ### Denoising
 The TF code is pretty dang similar to the convolutional autoencoder above. However, 
 there are important differences.
 
 The most import difference is that in a denoising autoencoder, the input and output are not
 exactly the same during trainig.  This is because we purposely inject noise into the input
 images, then have the network try to learn what must be done in order to make it look like the
 original image, which we use as the target.  
 
 ```python
 %matplotlib inline
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt

# Data
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets('MNIST_data', validation_size=0)

# I/O
inputs_ = tf.placeholder(tf.float32, (None,28,28,1), name="inputs")
targets_ = tf.placeholder(tf.float32, (None,28,28,1), name="targets")

### Encoder
conv1 = tf.layers.conv2d(inputs_, filters=32, kernel_size=(3,3), \
    padding='same', activation=tf.nn.relu)
maxpool1 = tf.layers.max_pooling2d(conv1, pool_size=(2,2), strides=(2,2), padding='same')
conv2 = tf.layers.conv2d(maxpool1, 32, (3,3), padding='same', activation=tf.nn.relu)
maxpool2 = tf.layers.max_pooling2d(conv2, (2,2), (2,2), padding='same')
conv3 = tf.layers.conv2d(maxpool2, 16, (3,3), padding='same', activation=tf.nn.relu)
encoded = tf.layers.max_pooling2d(conv3, (2,2), (2,2), padding='same')

### Decoder
upsample1 = tf.image.resize_nearest_neighbor(encoded, (7,7))
conv4 = tf.layers.conv2d(upsample1, 16, (3,3), padding='same', activation=tf.nn.relu)
upsample2 = tf.image.resize_nearest_neighbor(conv4, (14,14))
conv5 = tf.layers.conv2d(upsample2, 32, (3,3), padding='same', activation=tf.nn.relu)
upsample3 = tf.image.resize_nearest_neighbor(conv5, (28,28))
conv6 = tf.layers.conv2d(upsample3, 32, (3,3), padding='same', activation=tf.nn.relu)

### Logits and Denoised Images
logits = tf.layers.conv2d(conv6, 1, (3,3), padding='same', activation=None)
decoded = tf.nn.sigmoid(logits, name="decoded")

### Loss, Cost, and Opt
loss = tf.nn.sigmoid_cross_entropy_with_logits(labels=targets_, logits=logits)
cost = tf.reduce_mean(loss)
opt = tf.train.AdamOptimizer(0.001).minimize(cost)

### Training
# -- most of the denoising subtleties arise here
epochs = 100   # much more training required thant previous problem
batch_size = 200
noise_factor = 0.5
sess.run(tf.global_variables_initializer())
for e in range(epochs):
    for idx in range(mnist.train.num_examples // batch_size):
        batch = mnist.train.next_batch(batch_size)
        imgs = batch[0].reshape((-1,28,28,1))
        # Add random noise to input images
        noisy_imgs = imgs + noise_factor * np.random.randn(*imgs.shape)
        # Enforce the initial [0,1] clipping range of images
        noisy_imgs = np.clip(noisy_imgs, 0.0, 1.0)
        ### Major difference from regular convAE:  inputs_ ~= targets_ (not ==)
        batch_cost, _ = sess.run([cost,opt], 
                feed_dict = {inputs_: noisy_imgs, targets_: imgs})
        print("Epoch: {}/{}...".format(e+1, epochs), 
            "Training Loss: {:.4f}".format(batch_cost))


### Performance
fig, axes = plt.subplots(nrow=2, ncols=10, sharex=True, sharey=True, ........)
in_imgs = mnist.test.images[:10]
noisy_imgs = in_imgs + noise_factor * np.random.randn(*in_imgs.shape)
noisy_imgs = np.clip(noisy_imgs, 0.0, 1.0)
reconstructed = sess.run(decoded, feed_dict={inputs_: noisy_imgs.reshape((..........)
for images, row in zip([noisy_imgs, reconstructed], axes):
    for img, ax in zip(images, row):
        ax.imshow(img.reshape((28,28)), cmap='Greys_r')
        ax.get_xaxis().set_visible(False)
        ax.get_yaxis().set_visible(False)
fig.tight_layout(pad=0.1)

 ```

## References
* [Deconvolution and Checkerboard Artifacts](http://distill.pub/2016/deconv-checkerboard/)
* 2013: Kingma & Welling: [Auto-Encoding Variational Bayes](https://arxiv.org/abs/1312.6114)
* 2014: Rezende et al: [Stochastic Back Propagation and Approximate Inference in Deep Generative Models](https://arxiv.org/abs/1401.4082)

### Some Links
* [Introducing Variational Autoencoders in Prose](http://blog.fastforwardlabs.com/2016/08/12/introducing-variational-autoencoders-in-prose-and.html)
  - Easy Mode Reading
  - Great read!
* [Under the hood of the Variational Autoencoder](http://blog.fastforwardlabs.com/2016/08/22/under-the-hood-of-the-variational-autoencoder-in.html)
  - Technical follow-up to "Introducing VAEs in Prose"
  - Example implementation of a VAE on MNIST in TensorFlow
  - Read again w/ more note taking...
* [Building Autoencoders in Keras](https://blog.keras.io/building-autoencoders-in-keras.html)
  - I use tf.contrib.keras
  - Still haven't really figured out if this offers any advantage over just using keras
* [What is a Variational Autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)
  - bridges language gap between neural networks and probabilistic models
  - worth reading a couple more times, then building further bridges to quantum mechanical, digital signal processing, and/or manifold lingo
* [Kullback-Leibler Divergence Explained](https://www.countbayesie.com/blog/2017/5/9/kullback-leibler-divergence-explained)
  - discusses KL divergence
* [Tutorial on Variational Autoencoders](https://arxiv.org/abs/1606.05908)
* [Generative Models (OpenAI)](https://blog.openai.com/generative-models/)
  - application of VAEs
* [Graphical Models: Theory (blog.forty.to)](http://blog.forty.to/2013/08/24/graphical-models-theory/)



