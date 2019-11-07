
Full Stack Deep Learning, March 2019: [Official Website](https://fullstackdeeplearning.com/march2019)

# Lecture 1: Introduction to Deep Learning (Pieter Abbeel)

* 1958:  Rosenblatt:  [The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain](https://www.ling.upenn.edu/courses/cogs501/Rosenblatt1958.pdf)
* 1980:  Fukushima:  [Neocognitron: A Self-organizing Neural Network Model for a Mechanism of Pattern Recognition Unaffected by Shift in Position](https://www.cs.princeton.edu/courses/archive/spr08/cos598B/Readings/Fukushima1980.pdf)
* 1982:  Kohonen:  [Self-Organized Formation of Topologically Correct Feature Maps](http://www.cnbc.cmu.edu/~tai/nc19journalclubs/Kohonen1982_Article_Self-organizedFormationOfTopol.pdf)
* 1982:  Oja:  [A Simplified Neuron Model as a Principal Component Analyzer](http://www.cnbc.cmu.edu/~tai/nc19journalclubs/oja_pca_1982.pdf)
* 1985:  Rumelhart et al:  [Learning Internal Representations by Error Propagation](https://web.stanford.edu/~jlmcc/papers/PDP/Volume%201/Chap8_PDP86.pdf)
* 1988:  Broomhead & Lowe:  [Radial Basis Functions, Multi-Variable Functional Interpolation and Adaptive Networks](https://apps.dtic.mil/dtic/tr/fulltext/u2/a196234.pdf)
* 1989:  Baldi & Hornik:  [Neural Networks and Principal Component Analysis: Learning from Examples Without Local Minima](http://www.vision.jhu.edu/teaching/learning/deeplearning18/assets/Baldi_Hornik-89.pdf)
* 1989:  Lecun et al:  [Backpropagation applied to handwritten zip code recognition](https://www.ics.uci.edu/~welling/teaching/273ASpring09/lecun-89e.pdf)
  - convolutional nets
* 1992:  Neal:  [Connectionist learning of belief networks](http://www.cs.toronto.edu/~bonner/courses/2016s/csc321/readings/Connectionist%20learning%20of%20belief%20networks.pdf)
  - sigmoid belief networks
* 1994:  Field:  [What is the Goal of Sensory Coding?](https://www.researchgate.net/profile/David_Field8/publication/220500193_What_Is_the_Goal_of_Sensory_Coding/links/02e7e538699c231f1d000000.pdf)
* 1996:  Olshausen & Field:  [Emergence of Simple-Cell Receptive Field Properties by Learning Sparse Code for Natural Images](https://www.cns.nyu.edu/~tony/vns/readings/olshausen-field-1996.pdf)
* 1996:  Olshausen & Field:  [Natural image statistics and efficient coding](https://www.cs.unm.edu/~williams/cs591/olshausen96.pdf)
* 2009:  Deng et al:  [ImageNet: A Large-Scale Hierarchical Image Database](https://www.researchgate.net/profile/Li_Jia_Li/publication/221361415_ImageNet_a_Large-Scale_Hierarchical_Image_Database/links/00b495388120dbc339000000/ImageNet-a-Large-Scale-Hierarchical-Image-Database.pdf)
* 2012:  Krizhevsky et al:  [ImageNet Classification with Deep Convolutional Neural Networks](http://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networ)
* 2015:  Antol et al:  [VQA: Visual Question Answering](http://openaccess.thecvf.com/content_iccv_2015/html/Antol_VQA_Visual_Question_ICCV_2015_paper.html)
* 2015:  Donahue et al:  [Long-Term Recurrent Convolutional Networks for Visual Recognition and Description](http://openaccess.thecvf.com/content_cvpr_2015/html/Donahue_Long-Term_Recurrent_Convolutional_2015_CVPR_paper.html)
* 2015:  Karpathy & Fei-Fei:  [Deep Visual-Semantic Alignments for Generating Image Descriptions](https://www.cv-foundation.org/openaccess/content_cvpr_2015/html/Karpathy_Deep_Visual-Semantic_Alignments_2015_CVPR_paper.html)
* 2015:  Xu et al:  [Show, Attend and Tell: Neural Image Caption Generation with Visual Attention](http://proceedings.mlr.press/v37/xuc15.pdf)
* 2017:  Esteva et al: [Dermatologist-level classification of skin cancer with deep neural networks](https://www.nature.com/articles/nature21056?TB_iframe=true&width=914.4&height=921.6)
  - paywall
* 2017:  Rajpurkar et al:  [CheXNet: Radiologist-Level Pneumonia Detection on Chest X-Rays with Deep Learning](https://arxiv.org/abs/1711.05225)



# Lecture 2: Setting Up Machine Learning Projects (Josh Tobin)

The [Labs on GitHub](https://github.com/full-stack-deep-learning/fsdl-text-recognizer-project) (though
none of the labs seem to actually exist).

Lifecyle of ML Project 
* Planning and Project Set Up
  - Decide on project (e.g., pose estimation)
    * Define project goals
  - Determine requirements and goal (e.g., esitmate x,y,z of object center and angles for orientation)
    * Choose metrics
  - Allocate resources (where will the data come from? how much compute will you require?)
    * Set up codebase
* Data Collection and Labeling
  - Develop collection strategy
  - Implement strategy and ingest
  - Label collected data
  - Pose Estimation example
    * Collect training objects (e.g., boxes of cereal)
    * Set up sensors (e.g., cameras)
    * Capture data (e.g., images of the cereal boxes)
    * Annotate w/ ground truth (must decide on what that even means)
  - NOTE: Lifecycle is not a linear flow!  If things seem to complex here, we can go back to the first phase.
    * e.g., maybe it's too hard to take pictures of cereal boxes, so you decide to estimate pose on images
      of alien octopus ghosts
* Training & Debugging
  - Implement baseline model
  - Find state-of-the-art model and reproduce
  - Debug your implementation as much as possible
  - Fine tune and improve the model for specific task 
  - NOTE: this phase includes reading, implementing, debugging, ideation, etc
  - TROUBLESHOOTING
    * Do we need to collect more data?
    * Is the data labeling unreliable?  
    * Do we need to rethink the project scope? 
    * Do we need to rethink our data collection procedures? 
* Deploying & Testing
  - Testing 
    * Ensure model works on mission critical data points
    * Software regression testing (regularly ensure all functions/objects/etc work as intended,
      especially after any software updates)
    * Fine-tuned validation metrics, e.g., things like closer attention to individual class accuracies
  - TROUBLESHOOTING
    * Chosen metric does not affect real world events as planned
    * Real world performance does not match test set performance 
* Maintaing and Updating
  - Maintaining
    * Keep tabs on performance drift
    * Develop online updating / retraining strategy
  - Updating
    * Keep tabs on state-of-the-art in your domain
    * Stay current with similar domains and what's possible
    * Develop an intuition for how to improve and what to try next

### Planning and Project Set Up
How to choose a high-impact project?
How much will a ML project cost? 

```
                 Low         High
             Feasibility  Feasibility
              ______________________
 High Impact |          |     X     |
             |----------|-----------|
 Low  Impact |__________|___________|
 ```
 
 Where can you take advantage of cheap prediction?
 
 Where can you automate complicated manual software pipelines?
 
 Karpathy:  "Gradient descent can write code better than you. I'm sorry."
 * Find points in software pipelines where this might hold true
 
 Cost drivers
 * Data availability
  - How hard is it to get?
  - How expensive is it to label?
  - How much of it will be needed?
 * Accuracy requirement
  - How costly are wrong predictions?
    * Recommender systems often have low cost (e.g., Netflix recommendations)
  - How often does the model need to be right to be useful?
    * Again, recommender systems have low accuracy requirements:  Netflix recommendations
      only have to be great at a fairly low frequency (thus why there are a million recommendation
      bars w/ tons of shows that look uninteresting)
 * Problem difficulty
  - Is there any good, published work on similar problems?
    * If not, this means more risk and technical effort
  - How much compute required for training?
    * Can transfer learning help reduce this?  (e.g., use model pre-trained on ImageNet)
  - How much compute required for deployment?
    * Does it need to run real-time on a smartphone?
    * Or is it running in the cloud?
    * How much latency is ok?
  - What level of automation is necessary?
    * Automation is often thought of at 6 different levels:
      - Level 0: No automation
      - Level 1: Assistance
      - Level 2: Partial Automation
      - Level 3: Conditional Automation
      - Level 4: High Automation
      - Level 5: Full Automation
    * It is a good exercise to write out what these things mean for your particular project

One can optimize cost by trade-offs, e.g., Facebook does not automatically tag you in a photo, but
asks if the person in the photo is you.  This reduces the level of automation, but also reduces
the risk of failure -- it lowers the accuracy requirement.  It's more acceptable if Facebook incorrectly
asks if a person in a photo is you than if it automatically assigns it to be you.
 
 
Choosing Metrics:
There is a discrepancy between how Machine Learning systems train and what the real world is really like:
* The real world is messy and one is often interested in multiple metrics simultaneously
* However, ML systems work best when optimizing against a scalar
This means that you need to choose a formula that intelligently combines the metrics you care about, e.g.,
* a weighted sum
* n-1 thresholds w/ nth primary metric
  - e.g., set recall threshold and optimize precision
* integrations
  - AUROC
  - AUPRC (called mean average precision, or MAP, in the video)

NOTE: Blows my mind that this is state-of-the-art machine learning.  I read so much about improving
neural networks through clever architectural modifications, taking advantage of pre-trained models, etc.  But
I don't think I ever see much about addressing vectorial loss functions or model metrics (for model metrics,
we do see passive use of multiple metrics, or a single-primary-multiple-secondary approach, where the primary is
directly involved in the training process and the secondary metrics passively or indirectly so).  All the big 
wigs, like Hinton,
LeCun, and Bengio seem to generate new ideas by considering how the human perceptual system works, or the 
mechanics of neurons and synapses...but what about the fact that humans are constantly weighing pros and cons, i.e.,
considering multi-component loss/reward functions and/or mental model metrics...?


Creating baselines
* mean model
* single-variable model
* rules-based model
* linear/logistic regression
* neural network w/o bells and whistles (no batch norm, no weight norm, no dropout, etc)

Human baselines: Quality/Ease Trade-Off
* Lowest Quality, Highest Ease:  Random People (e.g.,Amazon Turk)
* Low Quality, High Ease: Ensemble of Random people
* Intermediate:  Domain experts (e.g., doctors)
* High Quality, Low Ease:  the best domain experts (e.g., specialists)
* Highest Quality, Lowest Ease:  Mixture/Ensemble of experts

How many people should be on a ML team?
* He says that he has seen the most success with teams of 8-12 people -- a mix of software and ML engineers


 
### References in Lecture
* 2017:  Karpathy:  [Software 2.0](https://medium.com/@karpathy/software-2-0-a64152b37c35)
* 2017:  Reinhardt:  [Designing Collaborative AI](https://medium.com/@Ben_Reinhardt/designing-collaborative-ai-5c1e8dbc8810)
* 2017:  Xiang et al:  [PoseCNN: A Convolutional Neural Network for 6D Object Pose Estimation in Cluttered Scenes](https://arxiv.org/abs/1711.00199)
* 2018:  Agrawal et el:  [Prediction Machines: The Simple Economics of Artificial Intelligence](https://books.google.com/books?id=wJY4DwAAQBAJ&printsec=frontcover&source=gbs_ge_summary_r&cad=0#v=onepage&q&f=false)




