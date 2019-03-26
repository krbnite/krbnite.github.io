---
title: Linear Regression in Tensorflow
layout: post
tags: deep-learning machine-learning tensorflow python udacity wwe
---

Linear regression is a go-to example of supervised machine learning.  Interestingly, as with
many other types of well-known data analysis techniques, a linear regression model can be 
represented as a neural network:  using known input and output data, the goal is to find the
weights and bias that best represent the outputs/response as a linear function of the 
inputs/predictors. The neural network representation seamlessly integrates simple (one predictor), 
multiple (more than one predictor), and multivariate (more than one response variable) 
regression into one visual:

(input) --> [W,b] --> (output)

You can quickly throw together a linear regression. However, we will trade off some quickness
for generality and treat the linear regression as
we would an arbitrary neural network, using a more typical neural network workflow. This way,
taking the next step to more complex neural networks is trivial.  By adopting such a work flow,
one can get a sense that neural nets may serve as a generalized framework for data analysis in general, 
encompassing techniques such as linear and logistic regression, principle component analysis (PCA),
and deep neural nets like convolutional neural networks (CNNs) and recursive neural networks (RNNs).



```python
#----------------------------------------
# Load your tools
#----------------------------------------
import numpy as np
import tensorflow as tf

#----------------------------------------
# Data Import
#----------------------------------------
#  -- or generation, as we do here
w_data = 3
b_data = 2
x_data = np.array([np.random.rand(10000)]).transpose().astype(np.float32)
y_data = x_data * w_data + b_data
# Add some noise
y_data = np.vectorize(lambda y: y + np.random.normal(loc=0.0, scale=0.1))(y_data)
```

#### Note on variable types
Be careful, the last op converted y_data to np.float64, which tensorflow will
have a fit over!

In TensorFlow, you cannot be lazy with variable types.  That is, TF will not
do any assuming for you.  An example will help here:

```python
a = tf.constant(2)
b = tf.constant(2, dtype=tf.float32)
pi = tf.constant(3.1415)
```

`tf.add(a,pi)` will produce an error because `a` is an integer (tf.int32) and `pi`
is a float (tf.float32). tf.add(b,pi) will not produce this error.

```python
y_data.dtype  # not np.float32 as we need it to be!
y_data = y_data.astype(np.float32)

#----------------------------------------
# Data Shape
#----------------------------------------
n_data     = x_data.shape[0]
n_features = 1
n_outputs  = 1
```

Training, Validation, and Test Split
=======================================================

There are various ways to do this.  We'll do a 50-25-25 split.

```python
from sklearn.model_selection import train_test_split
x_train, x_vt, y_train, y_vt = train_test_split(x_data, y_data, test_size=0.5, random_state=42)
x_val, x_test, y_val, y_test = train_test_split(x_vt, y_vt, test_size=0.5, random_state=31)
```

Feature Normalization
========================================================

In this particular example, normalization is not essential becase we have 
only one input feature.  However, it is necessary in almost any other case!
So might as well get into the habit! In a regular statistical setting,
normalization might literally mean to put the data into a normal, Gaussian
form, e.g., for a log-normal distribution, this is done by taking the 
logarithm of the data.  To compare data sets, one might then standardize
the data by z-scoring the normalized data.  In the linear algebra setting,
normalization refers to discarding a vector's magnitude and maintaining
only its directionality -- that is, to keep only the information that situates
the vector on the n-sphere. Or, to get my head out of my ass, it means
to divide a vector by its length to get a unit vector.  Anyway, the point is,
in reference to neural networks, "normalization" in general is more akin
to the linear algebraic definition.  That is, unitizing one's variables is essential for NN 
algorithms to perform properly and quickly.  Z-scoring one's data is one way to do this... Note, 
however, that z-scoring no longer refers to standardizing a normal distribution, but only 
to the transformation (Y-mean(Y))/stdDev(Y).   There are actually many types of variable normalization 
that are used in the wild, which are better suited for this or that.  For now, z-scoring is good 
enough.

**Important note**: Normalization in general refers to a relocation and rescaling of each feature 
variable, but as estiamted on the training set only.  To learn more about these types of things, 
see these [excellent lecture notes](http://cs231n.github.io/neural-networks-2/).  




Note on TensorFlow Tensors
============================================================
To use TensorFlow, you need to figure out how to declare your tensors.  
What's a tensor you ask?  Well, [in physics](https://en.wikipedia.org/wiki/Tensor) a
tensor is a geometrical object that follows a set of covariant and/or 
contravariant transformation laws, which can be written as a multi-dimensional
array upon choosing a fixed frame of reference (i.e., set of basis vectors),
but which has a meaningful existence independent of the chosen coordinate
representation.

Don't worry though, in TensorFlow it's really just a homogeneous, N-dimensional array.
No pressing need to first understand all the intracacies of
[Riemannian geometry](https://en.wikipedia.org/wiki/Riemannian_geometry).

Something as simple as a single number, like 4, is a type
of tensor.  In array parlance, one calls it a 0-dimensional array.  In tensor parlance,
it's a 0-dimensional tensor, or a scalar.  A 1-dimensional tensor (aka, vector) is just a 
homogeneous list of 0-dimensional arrays, e.g., a set of integers, or a set of strings, but not
a mixed bag of both strings and integers.  Similarly, a 2-dimensional tensor is 
a homogeneous list of homogeneous lists: a set of integer sets, or a set of string sets, but not a 
set of both integer sets and string sets.  This homogeneity is what
separates a 2D tensor object in TensorFlow from something like a pandas DataFrame, which 
is less restrictively a list of homogeneous lists.

In TensorFlow, when specifying a tensor, TensorFlow wants to know: will this tensor
remain constant, be repeatedly updated during training (like weights and biases), or serve as a container
for various sets of unvarying data throughout the training session (e.g., a placeholder for data 
batches of varying size).  In these cases, it is necessary to declare your tensor using tf.constant(), tf.Variable(), and 
tf.placeholder(), respectively.  

Basically, you use tf.Variable instead of tf.constant for any tensor whose value is going to 
be updated throughout the model training session and you know enough about the quantity to 
initialize it.  For example, when training a neural network, your goal is to
train the weights and biases, which can be randomly intialized at the start of the session. 
Thus, weights and biases represent the typical use case of a tf.Variable.  
However, the training data itself static: though your network might look at different subsets 
(batches) at different points in time, at no point is the training data itself being updated.  
It is not initialized at some known or random starting value.  It is merely something you feed 
into your net.  For this, you choose a placeholder, and the specifics (e.g., batch values and 
size) are then computed at run time.  

```python
#----------------------------------------
# Data Placeholders
#----------------------------------------
# By specifying None in the first slot (the "batch slot"), we are telling
#   TensorFlow that we reserve the right to make this value any size
x_input = tf.placeholder(shape=(None,1), dtype=tf.float32)
y_input = tf.placeholder(shape=(None,1), dtype=tf.float32)

#----------------------------------------
# Initialization of Model Parameters
#----------------------------------------
#  In a simple case like simple linear regression, we might 
#  scatter plot the data and eyeball an estimate of the slope and y intercept.
#  However, this technique doesn't really generalize to higher dimensional
#  data sets with many more predictor and response variables.
#  
#  Importantly, weights must be randomly distributed.  They should also 
#  be small...but how small?  For ReLu activations, and mostly in general,
#  like a random number generated by Gaussian distribution with mean=0, 
#  stddev=sqrt(2.0/n_features) is recommended. Also, to avoid generating
#  numbers that are too large, it is good practice to take from a 
#  truncated normal distribution.
w = tf.Variable(
    tf.truncated_normal(
        (n_features,n_outputs), 
        mean=0,
        stddev=np.sqrt(2.0/n_features),
        dtype=tf.float32,
        seed=2),
    name="weight")
b = tf.Variable(
    tf.zeros(
        n_outputs, 
        dtype=tf.float32),
    name="bias")

#----------------------------------------
# CONSTRUCT MODEL / Make a Prediction Function
#----------------------------------------
#  -- Obviously, in this case a linear function: y = wx+b
y_pred = tf.add(tf.multiply(x_train,w), b)

#---------------------------------------------------------
# Choose a Learning Method: Loss Function and Optimizer
#---------------------------------------------------------
# In simple linear regression, we almost always mean "least squares linear 
#   regression," which refers to minimizing the sum of squared errors (SSE) or, 
#   equivalently, the mean-squared error (MSE).  So that's what we'll do.
#   But the modularity of this process really allows us to use any loss function
#   we want, and we can just as readily turn this into another type of 
#   regression altogether!
# What's nice about the neural network representation of linear regression in 
#   conjunction with the typical NN workflow is the modularity.  At this point,
#   we can choose Gradient Descent, which is a pretty standard way of minimizing
#   a loss function.  However, we can easily swap in any other optimizer that
#   TensorFlow has to offer.  For small data sets and simple networks, this isn't a
#   big deal, but it's worth experimenting with early on anyway -- because it does
#   become a bigger deal as your data and networks grow in size and complexity.
learning_rate = 0.1
loss = tf.reduce_mean(tf.square(y_pred - y_input))
optimizer = tf.train.GradientDescentOptimizer(learning_rate)
train = optimizer.minimize(loss)


#---------------------------------------------------------
# Implement Your Model:  Run a TF Session
#---------------------------------------------------------
param_updates = []
init = tf.global_variables_initializer()
sess = tf.Session()
sess.run(init)
for i in range(1000):
  evals = sess.run([train,w,b], feed_dict={y_input: y_train})[1:]
  if i % 25 == 0: 
    print(i, evals)
    param_updates.append(evals)
sess.close()
```



### References
These notes follow 
[Deep Learning with TensorFlow](https://www.youtube.com/playlist?list=PL-XeOa5hMEYxNzHM7YLRjIwE1k3VQpqEh) 
by Big Data University, as well as numerous StackExchange threads, what I've covered in Udacity's Deep
Learning Nanodegree Foundation, and readings of the TF documentation.

