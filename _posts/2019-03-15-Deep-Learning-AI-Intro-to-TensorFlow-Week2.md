---
layout: post
title: deeplearning.ai's Intro to TensorFlow (Week 2)
tags: deep-learning machine-learning tensorflow python coursera easi
---




# Fashion MNIST


## Fashion MNIST References
* https://github.com/zalandoresearch/fashion-mnist
* https://www.tensorflow.org/tutorials/keras/basic_classification
* https://hanxiao.github.io/2018/09/28/Fashion-MNIST-Year-In-Review/
* https://github.com/hwalsuklee/tensorflow-generative-model-collections 
* https://www.youtube.com/watch?v=RJudqel8DVA


# Identifying Biases in Machine Learning 
This is a very interesting subfield of machine learning.  

The lectures introduced this concept very quickly and little artificially, noting that by encoding
labels as numbers, there is no language bias (true, but I thought we do that b/c TF needs numerical data :-p).

At any rate, the meat of this topic was provided in a link to a Google Developer page called
[Machine Learning Fairness](https://developers.google.com/machine-learning/fairness-overview/), which has an
interesting [video](https://www.youtube.com/watch?v=59bMh59JQDo) and a list of references for further
reading.  This linked to a great Google Developer blog article called [Text Embedding Models Contain Bias](https://developers.googleblog.com/2018/04/text-embedding-models-contain-bias.html), which very simply
details what bias in machine learning models is, and details why and when it matters.  

For example, the authors reference several studies that highlight embarrassing-to-alarming features 
learned (or not) by ML models:
* "face classification models may not perform as well for women of color"
  - this bias seems more "embarrassing" than "alarming" in that the training data set should have been better vetted
  - given that it can be upsetting/distressing, one might call it alarming too though
  - at any rate, this example definitely shows how unconscious bias can leak in to a training set
* "classifiers trained to detect rude, disrespectful, or unreasonable comments may be more likely to flag the sentence 'I am gay' than 'I am straight'"
  - this bias is more "alarming" than embarrassing because the model is unable to contextualize the word "gay" 
  and therefore unable to distinguish between someone self-identifying as gay and someone else using the word "gay" as a slur
  - it is alarming because it will flag someone self-identifying as gas as being rude, disrespectful, or unreasonable
* "speech transcription may have higher error rates for African Americans than White Americans"
  - this has both elements of "embarrassing" (should have used a more robust training set!) and
  possibly "alarming" too (can be upsetting/distressing)

Actually, after trying to decide where these examples fit on a scale from "embarrassing" to "alarming," it
strikes me this isn't the right scale to use:  they all seem to contain both embarrassing and alarming 
elements.  Also, all examples are
"bad for business", period.  For a company like Google, any of these would be bad press.  For a small startup, 
missteps like these might be the difference between gaining traction or going extinct.  

Some of the ML bias concerns are clearly concerning, however these isssues are very context dependent.  Biases
identified in a data set are not necessarily inherently bad and should not be automatically removed.  Really
depends on how you will be using the data.

For example, in the beginning of the article, looking at the  ability of 5 different models in
predicting whether or not a movie review is positive or negative, the authors observe that model C
performs the best -- but with a caveat:

> "Normally, we'd simply choose Model C. But what if we found that while Model C performs the best overall, it's also most likely to assign a more positive sentiment to the sentence "The main character is a man" than to the sentence "The main character is a woman"? Would we reconsider?"

If you want to reflect the reality presented in the data set and make the best prediction, you would
not reconsider.  In a scientific, empirical sense, this bias (preference?) simply
exists -- at least in the minds of the reviewers represented in the training set.  If the context is
that you will be betting money on whether or not a reviewer gave a newly released movie a thumbs up
or thumbs down based only the on the text of their review, you better use the best model (especially
if it's a reviewer that had representation in your training set).  However, even in this case, knowledge
of implicit bias in the training set would be helpful, e.g., if you know all the reviewers in the
training set were 20-something year old males and the bet was to be made on a 40-something year old female's 
review, you might not use Model C or even take that bet.

The authors of the article actually go into this quite a bit, and more eloquently than I just did.  



## ML Implicit Bias References
* [Machine Learning Fairness](https://developers.google.com/machine-learning/fairness-overview/)
* [Text Embedding Models Contain Bias. Here's Why That Matters.](https://developers.googleblog.com/2018/04/text-embedding-models-contain-bias.html)
* [Measuring and Mitigating Unintended Bias in Text Classification](https://github.com/conversationai/unintended-ml-bias-analysis/blob/master/presentations/measuring-mitigating-unintended-bias-paper.pdf)
* Some Google CoLabs
  - [Conversation AI's Pinned AUC Unintended Model Bias Demo](https://colab.research.google.com/github/conversationai/unintended-ml-bias-analysis/blob/master/unintended_ml_bias/pinned_auc_demo.ipynb)
  - [Mitigating Unwanted Biases with Adversarial Learning](https://colab.research.google.com/notebooks/ml_fairness/adversarial_debiasing.ipynb)



# Some Code

```python
from tensorflow import keras 

# RAW DATA
mnist = tf.keras.datasets.fashion_mnist
(training_images, training_labels), (test_images, test_labels) = mnist.load_data()

# NORMALIZE DATA
training_images, test_images = training_images/255.0, test_images/255.0

# MODEL:  xMRNS = y  
model = keras.Sequential([
  keras.layers.Flatten(input_shape=(28,28)),       # 28x28 input to match F-MNIST images, then flattened to 1D
  keras.layers.Dense(128, activation=tf.nn.relu),  # hidden layer
  keras.layers.Dense(10, activation=tf.nn.softmax) # 10 neurons to match 10 clothing classes
])

# OPTIMIZER METHOD & LOSS FCN
model.compile(optimizer = tf.train.AdamOptimizer(),
              loss = 'sparse_categorical_crossentropy',
              metrics=['accuracy'])

# TRAIN MODEL
model.fit(training_images, training_labels, epochs=5)

# EVALUATE MODEL
model.evaluate(test_images, test_labels)

# CLASSIFICATIONS 
#   - vectors representing probability of each FMNIST class for given image
classifications = model.predict(test_images)
```

Updating that code to be more flexible w/ a function:
```python


def get_model(
  dense = [128],
  activation = tf.nn.relu,
  optimizer = tf.train.AdamOptimizer(),
  loss = 'sparse_categorical_crossentropy',
  metrics = ['accuracy'],
):

  # DENSE LIST
  #  -- for single hidden layer designations, like 512
  if type(dense) is not type(list()): dense = [dense]

  # MODEL:  xMRNS = y  
  model = keras.Sequential()
  model.add(tf.keras.layers.Flatten(input_shape=(28,28)))
  for layer_size in dense: model.add(tf.keras.layers.Dense(layer_size, activation = activation))
  model.add(keras.layers.Dense(10, activation=tf.nn.softmax))

  # OPTIMIZER METHOD & LOSS FCN
  model.compile(
    optimizer = optimizer,
    loss = loss,
    metrics = metrics
  )
  
  return model_architecture
  

def train_model(
  model,
  training_images,
  training_labels,
  epochs = 5,
  loss_goal = 0.25,
):
  # Add Error Checks: training_images.shape == (28,28), etc
  
  # Threshold Callback
  class myCallback(tf.keras.callbacks.Callback):
    def on_epoch_end(self, epoch, logs={}):
      if(logs.get('loss') < loss_goal):
        print(f"\n\nReached loss goal so cancelling training!\n\n")
        self.model.stop_training = True

  model.fit(
    training_images, 
    training_labels, 
    epochs = epochs,
    callbacks = [myCallback()]
  )
  
  return model

```

