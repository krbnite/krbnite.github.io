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

For example, in [Text Embedding Models Contain Bias](https://developers.googleblog.com/2018/04/text-embedding-models-contain-bias.html), the authors references
several studies that picked out embarrassing-to-alarming features learned by ML models:
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

After trying to decide where some of these examples fit on the scale from "embarrassing" to "alarming," I've
realized that they all basically contain both embarrassing and alarming elements.  Also, all examples are
"bad for business", period.  For a company like Google, any of these would be bad press.  For a small startup, 
missteps like these might be the difference between gaining traction or going extinct.

Some of the ML bias concerns are clearly concerning, however these isssues are very context dependent.  Biases
identified in a data set are not inherently bad and should not be automatically removed.  

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




* [Machine Learning Fairness](https://developers.google.com/machine-learning/fairness-overview/)
* 

