---
title: Experimenting with Random Forests on UCI ML Data Sets
layout: post
tags: machine-learning random-forests easi
---

For certain types of data, random forests work the best.  Maybe more accurately, I should say a
tree-based modeling approach works best -- because sometimes the RF is beat out by gradient boosted
trees, and so on.  That said, I've been kind of obsessively vetting random forests as of late, so
this is in that vein.

There are certain properties of RFs that I'm currently investigating, and -- who knows! -- might 
even publish on.  To get a fair picture of such properties, I need to investigate them in a variety
of data sets -- and perhaps even note differences between classification and regression problems.  Having said
that, going through the entirety of the UCI ML repository can be daunting.  There is also the fact
that these interests are related to goings-on at work, and so my priority should be to look at
wearables- or healthcare-related data sets.  But even with this restriction, there is so much to choose
from!  Also, there are non-wearable, non-health data sets that are very regularly used and 
referenced, which hold an important familiarity when it comes to comparisons, benchmarking, and better
understanding what might be happening.

In this post, I want to simply list data sets I'm interested in looking at.  It will help me better
organize my thoughts and serve as a nice reference as I work on this stuff over the next several months. I 
try to list some characteristics of the data sets too, since these can sometimes help direct whether or
not the data set is interesting for a particular use case.  For example, I'm very interested in comparing
various measures of feature importance, and how feature importance depends on things like mTry or nTree; in
this case, data sets with 4 features are less interesting than those with 40 or 400 features.  Similarly,
it is not necessarily interesting to look at a data set with only a few hundred records -- but sometimes it is!



# Classification Data Sets
The first data set we will play with is the UCI Mushroom data set.  It's simple and super easy to predict on.

We will also look at various health-related and miscellaneous data sets from the UCI repository.

### Health- and/or Bio-Related Data Sets

* [Blood Transfusion Service](https://archive.ics.uci.edu/ml/datasets/Blood+Transfusion+Service+Center)
  - 4 real-valued features
  - 1 binary outcome
* [Acute Inflammations](https://archive.ics.uci.edu/ml/datasets/Acute+Inflammations)
  - 6 features (categorical, integer)
  - 2 binary outcomes (e.g., predict on one, the other, or both, or maybe even
      set up a problem where we use on outcome to help predict another)
* [p53 Mutants (Cancer Yes/No)](https://archive.ics.uci.edu/ml/datasets/p53+Mutants)
  - 5408 real-valued features
  - 1 binary outcome
  - 16772 records
  - Missing Values: Yes
* [Breast Tissue](https://archive.ics.uci.edu/ml/datasets/Breast+Tissue)
  - 10 real-valued features
  - 6- or 4-class outcome (depending on how you set up problem)
  - 106 records
  - Missing Values: No
* [Cardiotocography (Fetal Heart Rate, Uterine Contraction)](https://archive.ics.uci.edu/ml/datasets/Cardiotocography)
  - 23 real-valued features
  - 10- or 3-class outcome (depending on what you choose to predict)
  - Missing Values: No
  - 2126 records
* [PubChem Bioassay Data](https://archive.ics.uci.edu/ml/datasets/PubChem+Bioassay+Data)
  - Multiple bioassay data sets (seems like they are not necessarily intended to be mixed together since
    they are bioassays to identify different things)
  - Features (number depends on which bioassay data set you work with)
  - Records (number depends on which bioassay data set you work with)
  - Missing Values (depends?)
* [Vertebral Column](https://archive.ics.uci.edu/ml/datasets/Vertebral+Column)
  - 6 real-valued features
  - 3- or 2-class outcomes (or (3,2)-multivariate outcome if you want)
  - 310 records
  - Missing Values: No
* [Indian Liver Patient Dataset](https://archive.ics.uci.edu/ml/datasets/ILPD+%28Indian+Liver+Patient+Dataset%29)
  - 10 features (real-valued, integer)
  - 583 records
  - 1 binary outcome (liver patiet or not?)
* [Skin Segmentation](https://archive.ics.uci.edu/ml/datasets/Skin+Segmentation)
  - 4 real-valued features
  - 245,057 records
  - Binary or Multi outcome (depends on how you set up the problem: there is a designated 
    decision variable, but one can also choose to try to classify something like race or age)

* [Yeast](https://archive.ics.uci.edu/ml/datasets/Yeast)
* [Statlog (Heart)](https://archive.ics.uci.edu/ml/datasets/Statlog+%28Heart%29)
* [Arcene (Cancer)](https://archive.ics.uci.edu/ml/datasets/Arcene)
* [Dorothea (Drug Discovery)](https://archive.ics.uci.edu/ml/datasets/Dorothea)
* [Parkinsons](https://archive.ics.uci.edu/ml/datasets/Parkinsons)


### Wearable / Activity Recognition Data Sets
* [EMG Physical Action Data Set](https://archive.ics.uci.edu/ml/datasets/EMG+Physical+Action+Data+Set)
  - 8 real-valued features
  - 20- or 2-class outcome (i.e., predict specific action, or just whether action was aggressive)
  - 10k records
  - Missing Values: No
* [Vicon (3D Tracker) Physical Action Data Set](https://archive.ics.uci.edu/ml/datasets/Vicon+Physical+Action+Data+Set)
  - 27 real-valued features
  - 20- or 2-class outcome (i.e., predict specific action, or just whether action was aggressive)
  - 3000 records
* [OPPORTUNITY Activity Recognition](https://archive.ics.uci.edu/ml/datasets/OPPORTUNITY+Activity+Recognition)
  - 242 real-valued features
  - 2551 records
  - Multi-class outcomes (dependent on what you choose to classify)
  - Missing Values: Yes
* [PAMAP2 Physical Activity Monitoring](https://archive.ics.uci.edu/ml/datasets/PAMAP2+Physical+Activity+Monitoring)
  - 52 real-valued features (or one can use the sensory time series data to construct more features, or alternatives)
  - 3,850,505 records
  - Multi-class outcome
* [Human Activity Recognition Using Smartphones](https://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones)
  - Description: "Human Activity Recognition database built from the recordings of 30 subjects performing activities of daily living (ADL) while carrying a waist-mounted smartphone with embedded inertial sensors."
  - 561 features
  - 10,299 records
  - Multi-class outcomes (e.g., predict the activity; maybe even try to predict the patient)
* [Wearable Computing: Classification of Body Postures and Movements (PUC-Rio)](https://archive.ics.uci.edu/ml/datasets/Wearable+Computing%3A+Classification+of+Body+Postures+and+Movements+%28PUC-Rio%29)
  - Description: "A dataset with 5 classes (sitting-down, standing-up, standing, walking, and sitting) collected on 8 hours of activities of 4 healthy subjects. We also established a baseline performance index."
  - 18 features (real, integer)
  - 165,632 records
  - Note: seems like sequential/timeSeries data (need to create own features, or use as-is (?))
* [Daily and Sports Activities](https://archive.ics.uci.edu/ml/datasets/Daily+and+Sports+Activities)
  - Description: "The dataset comprises motion sensor data of 19 daily and sports activities each performed by 8 subjects in their own style for 5 minutes. Five Xsens MTx units are used on the torso, arms, and legs."
  - 5,625 features
  - 9,120 records 
  - 19 classes
* [EEG Eye State](https://archive.ics.uci.edu/ml/datasets/EEG+Eye+State)
  - Description: "The data set consists of 14 EEG values and a value indicating the eye state."
  - 15 features
  - 14,980 records
* [Activities of Daily Living (ADLs) Recognition Using Binary Sensors](https://archive.ics.uci.edu/ml/datasets/Activities+of+Daily+Living+%28ADLs%29+Recognition+Using+Binary+Sensors)
  - Description: "This dataset comprises information regarding the ADLs performed by two users on a daily basis in their own homes."
  - 2747 records
  - Type of outcome and number of features dependent on design choices
  - More Info: "This dataset comprises information regarding the ADLs performed by two users on a daily basis in their
    own homes. This dataset is composed by two instances of data, each one corresponding to a different
    user and summing up to 35 days of fully labelled data. Each instance of the dataset is described by
    three text files, namely: description, sensors events (features), activities of the daily living (labels).
    Sensor events were recorded using a wireless sensor network and data were labelled manually."





### Miscellaneous Data Sets
* [Teacher Assistant Evaluation](https://archive.ics.uci.edu/ml/datasets/Teaching+Assistant+Evaluation)
* [Tic Tac Toe Endgame](https://archive.ics.uci.edu/ml/datasets/Tic-Tac-Toe+Endgame)
* [University](https://archive.ics.uci.edu/ml/datasets/University)
* [Wine](https://archive.ics.uci.edu/ml/datasets/Wine)
* [Wine Quality](https://archive.ics.uci.edu/ml/datasets/Wine+Quality)
* [Zoo](https://archive.ics.uci.edu/ml/datasets/Zoo)
* [CMU Face Images](https://archive.ics.uci.edu/ml/datasets/CMU+Face+Images)
* [Poker Hand](https://archive.ics.uci.edu/ml/datasets/Poker+Hand)
* [Dexter (Bag of Words)](https://archive.ics.uci.edu/ml/datasets/Dexter)
* [Gisette (Hand Written Digits)](https://archive.ics.uci.edu/ml/datasets/Gisette)
* [Ozone Level Detection](https://archive.ics.uci.edu/ml/datasets/Ozone+Level+Detection)
* [Plants](https://archive.ics.uci.edu/ml/datasets/Plants)
  - 70 categorical features
  - Missing values
* [Demospongiae (Sea Sponge)](https://archive.ics.uci.edu/ml/datasets/Demospongiae)
  - Severeal categorical features (# depends on what you consider your predictors)
  - 1 multiple-valued outcome (traditional set up: predict order from family, genus, and/or specie)
  - 503 records
  - Missing Values: Yes
* [Seeds](https://archive.ics.uci.edu/ml/datasets/seeds)
  - Description: "Measurements of geometrical properties of kernels belonging to three different varieties of wheat. A soft X-ray technique and GRAINS package were used to construct all seven, real-valued attributes."
  - 7 real-valued features
  - 210 records
  - Multi-class outcome (predict type of wheat)
* [One-Hundred Plant Species Leaves](https://archive.ics.uci.edu/ml/datasets/One-hundred+plant+species+leaves+data+set)
  - Description: "Sixteen samples of leaf each of one-hundred plant species. For each sample, a shape descriptor, fine scale margin and texture histogram are given."
  - 64 real-valued features
  - 1600 records
  - Multi-class outcome (e.g., predict the species)
* [Fertility (Semen Samples)](https://archive.ics.uci.edu/ml/datasets/Fertility)
  - 10 real-value features
  - 100 records
  - Binary outcome

  
  
