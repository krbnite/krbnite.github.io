
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
  - Determine requirements and goal (e.g., esitmate x,y,z of object center and angles for orientation)
  - Allocate resources (where will the data come from? how much compute will you require?)
* Data Collection and Labeling
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
  
 
### References in Lecture
* 2017:  Xiang et al:  [PoseCNN: A Convolutional Neural Network for 6D Object Pose Estimation in Cluttered Scenes](https://arxiv.org/abs/1711.00199)





