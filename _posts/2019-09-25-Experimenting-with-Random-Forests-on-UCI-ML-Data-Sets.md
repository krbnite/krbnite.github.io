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

## Health- and/or Bio-Related Data Sets

### Parkinson's
Directly relevant to work stuff.

* [Parkinsons](https://archive.ics.uci.edu/ml/datasets/Parkinsons)
* [Parkinson Speech Dataset with Multiple Types of Sound Recordings](https://archive.ics.uci.edu/ml/datasets/Parkinson+Speech+Dataset+with++Multiple+Types+of+Sound+Recordings)
* [Parkinson Disease Spiral Drawings Using Digitized Graphics Tablet](https://archive.ics.uci.edu/ml/datasets/Parkinson+Disease+Spiral+Drawings+Using+Digitized+Graphics+Tablet)
* [Parkinson's Disease Classification](https://archive.ics.uci.edu/ml/datasets/Parkinson%27s+Disease+Classification)
* [Parkinson Dataset with replicated acoustic features](https://archive.ics.uci.edu/ml/datasets/Parkinson+Dataset+with+replicated+acoustic+features+)


### Autism
Directly relevant to work stuff.

* [Autistic Spectrum Disorder Screening Data for Children](https://archive.ics.uci.edu/ml/datasets/Autistic+Spectrum+Disorder+Screening+Data+for+Children++)
* [Autistic Spectrum Disorder Screening Data for Adolescent Data Set](https://archive.ics.uci.edu/ml/datasets/Autistic+Spectrum+Disorder+Screening+Data+for+Adolescent+++)
* [Autism Screening Adult](https://archive.ics.uci.edu/ml/datasets/Autism+Screening+Adult)




### Heart
Somewhat relevant to work stuff.

* [Statlog (Heart)](https://archive.ics.uci.edu/ml/datasets/Statlog+%28Heart%29)
* [Cardiotocography (Fetal Heart Rate, Uterine Contraction)](https://archive.ics.uci.edu/ml/datasets/Cardiotocography)
  - 23 real-valued features
  - 10- or 3-class outcome (depending on what you choose to predict)
  - Missing Values: No
  - 2126 records


### Diabetes
Not a disease we focus on much at work. 

* [Diabetic Retinopathy Debrecen](https://archive.ics.uci.edu/ml/datasets/Diabetic+Retinopathy+Debrecen+Data+Set)
* [Diabetes 130-US hospitals for years 1999-2008](https://archive.ics.uci.edu/ml/datasets/Diabetes+130-US+hospitals+for+years+1999-2008)

### Cancer
Somewhat related to work stuff (e.g., brain cancer, ability to mine through genetics data, etc).

* [Cervical cancer (Risk Factors)](https://archive.ics.uci.edu/ml/datasets/Cervical+cancer+%28Risk+Factors%29)
* [Arcene (Cancer)](https://archive.ics.uci.edu/ml/datasets/Arcene)
* [Breast Tissue](https://archive.ics.uci.edu/ml/datasets/Breast+Tissue)
  - 10 real-valued features
  - 6- or 4-class outcome (depending on how you set up problem)
  - 106 records
  - Missing Values: No
* [p53 Mutants (Cancer Yes/No)](https://archive.ics.uci.edu/ml/datasets/p53+Mutants)
  - 5408 real-valued features
  - 1 binary outcome
  - 16772 records
  - Missing Values: Yes
  
  
### Misc
* [Immunotherapy](https://archive.ics.uci.edu/ml/datasets/Immunotherapy+Dataset)
* [Cryotherapy](https://archive.ics.uci.edu/ml/datasets/Cryotherapy+Dataset+)
* [Gastrointestinal Lesions in Regular Colonoscopy](https://archive.ics.uci.edu/ml/datasets/Gastrointestinal+Lesions+in+Regular+Colonoscopy)
* [Chronic Kidney Disease](https://archive.ics.uci.edu/ml/datasets/Chronic_Kidney_Disease)
* [Quality Assessment of Digital Colposcopies](https://archive.ics.uci.edu/ml/datasets/Quality+Assessment+of+Digital+Colposcopies)
* [Dorothea (Drug Discovery)](https://archive.ics.uci.edu/ml/datasets/Dorothea)
* [LSVT Voice Rehabilitation](https://archive.ics.uci.edu/ml/datasets/LSVT+Voice+Rehabilitation)
* [Thoracic Surgery](https://archive.ics.uci.edu/ml/datasets/Thoracic+Surgery+Data)
* [Yeast](https://archive.ics.uci.edu/ml/datasets/Yeast)
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
* [Blood Transfusion Service](https://archive.ics.uci.edu/ml/datasets/Blood+Transfusion+Service+Center)
  - 4 real-valued features
  - 1 binary outcome
* [Acute Inflammations](https://archive.ics.uci.edu/ml/datasets/Acute+Inflammations)
  - 6 features (categorical, integer)
  - 2 binary outcomes (e.g., predict on one, the other, or both, or maybe even
      set up a problem where we use on outcome to help predict another)



### Wearable / Activity Recognition / Home Sensor Data Sets
This whole section is SUPER relevant to work stuff.

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
* [Weight Lifting Exercises monitored with Inertial Measurement Units](https://archive.ics.uci.edu/ml/datasets/Weight+Lifting+Exercises+monitored+with+Inertial+Measurement+Units)
  - Description: "Six young health subjects were asked to perform 5 variations of the biceps curl weight lifting exercise. One of the variations is the one predicted by the health professional."
* [EMG dataset in Lower Limb](https://archive.ics.uci.edu/ml/datasets/EMG+dataset+in+Lower+Limb)
  - Description: "3 different exercises: sitting, standing and walking in the muscles: biceps femoris, vastus medialis, rectus femoris and semitendinosus addition to goniometry in the exercises."
* [Dataset for ADL Recognition with Wrist-worn Accelerometer](https://archive.ics.uci.edu/ml/datasets/Dataset+for+ADL+Recognition+with+Wrist-worn+Accelerometer)
  - Description: "Recordings of 16 volunteers performing 14 Activities of Daily Living (ADL) while carrying a single wrist-worn tri-axial accelerometer."
* [User Identification From Walking Activity](https://archive.ics.uci.edu/ml/datasets/User+Identification+From+Walking+Activity)
  - Description: "The dataset collects data from an Android smartphone positioned in the chest pocket from 22 participants walking in the wild over a predefined path."
* [Activity Recognition from Single Chest-Mounted Accelerometer](https://archive.ics.uci.edu/ml/datasets/Activity+Recognition+from+Single+Chest-Mounted+Accelerometer)
  - Description: "The dataset collects data from a wearable accelerometer mounted on the chest. The dataset is intended for Activity Recognition research purposes."
* [Gesture Phase Segmentation](https://archive.ics.uci.edu/ml/datasets/Gesture+Phase+Segmentation)
  - Description: "The dataset is composed by features extracted from 7 videos with people gesticulating, aiming at studying Gesture Phase Segmentation. It contains 50 attributes divided into two files for each video."
* [REALDISP Activity Recognition](https://archive.ics.uci.edu/ml/datasets/REALDISP+Activity+Recognition+Dataset)
  - Description: "The REALDISP dataset is devised to evaluate techniques dealing with the effects of sensor displacement in wearable activity recognition as well as to benchmark general activity recognition algorithms."
* [sEMG for Basic Hand movements](https://archive.ics.uci.edu/ml/datasets/sEMG+for+Basic+Hand+movements)
  - Description: "The 'sEMG for Basic Hand movements' includes 2 databases of surface electromyographic signals of 6 hand movements using Delsys' EMG System. Healthy subjects conducted six daily life grasps."
* [MHEALTH Dataset](https://archive.ics.uci.edu/ml/datasets/MHEALTH+Dataset)
  - Description: "The MHEALTH (Mobile Health) dataset is devised to benchmark techniques dealing with human behavior analysis based on multimodal body sensing."
* [Gas sensors for home activity monitoring](https://archive.ics.uci.edu/ml/datasets/Gas+sensors+for+home+activity+monitoring)
  - Description: "100 recordings of a sensor array under different conditions in a home setting: background, wine and banana presentations. The array includes 8 MOX gas sensors, and humidity and temperature sensors."
* [Smartphone Dataset for Human Activity Recognition (HAR) in Ambient Assisted Living (AAL)](https://archive.ics.uci.edu/ml/datasets/Smartphone+Dataset+for+Human+Activity+Recognition+%28HAR%29+in+Ambient+Assisted+Living+%28AAL%29)
  - Description: "This data is an addition to an existing dataset on UCI. We collected more data to improve the accuracy of our human activity recognition algorithms applied in the domain of Ambient Assisted Living."
* [Activity Recognition system based on Multisensor data fusion (AReM)](https://archive.ics.uci.edu/ml/datasets/Activity+Recognition+system+based+on+Multisensor+data+fusion+%28AReM%29)
  - Description: "This dataset contains temporal data from a Wireless Sensor Network worn by an actor performing the activities: bending, cycling, lying down, sitting, standing, walking."
* [MoCap Hand Postures](https://archive.ics.uci.edu/ml/datasets/MoCap+Hand+Postures)
  - Description: "5 types of hand postures from 12 users were recorded using unlabeled markers attached to fingers of a glove in a motion capture environment. Due to resolution and occlusion, missing values are common."
* [Motion Capture Hand Postures](https://archive.ics.uci.edu/ml/datasets/Motion+Capture+Hand+Postures)
  - Description: "5 types of hand postures from 12 users were recorded using unlabeled markers on fingers of a glove in a motion capture environment. Due to resolution and occlusion, missing values are common."
* [Wireless Indoor Localization](https://archive.ics.uci.edu/ml/datasets/Wireless+Indoor+Localization)
  - Description: "Collected in indoor space by observing signal strengths of seven WiFi signals visible on a smartphone. The decision variable is one of the four rooms."
* [Activity recognition with healthy older people using a batteryless wearable sensor](https://archive.ics.uci.edu/ml/datasets/Activity+recognition+with+healthy+older+people+using+a+batteryless+wearable+sensor)
  - Description: "Sequential motion data from 14 healthy older people aged 66 to 86 years old using a batteryless, wearable sensor on top of their clothing for the recognition of activities in clinical environments."
* [BLE RSSI Dataset for Indoor localization and Navigation](https://archive.ics.uci.edu/ml/datasets/BLE+RSSI+Dataset+for+Indoor+localization+and+Navigation)
* [EEG Steady-State Visual Evoked Potential Signals](https://archive.ics.uci.edu/ml/datasets/EEG+Steady-State+Visual+Evoked+Potential+Signals)
  - Description: "This database consists on 30 subjects performing Brain Computer Interface for Steady State Visual Evoked Potentials (BCI-SSVEP)."
* [WESAD (Wearable Stress and Affect Detection)](https://archive.ics.uci.edu/ml/datasets/WESAD+%28Wearable+Stress+and+Affect+Detection%29)
  - Description: "WESAD (Wearable Stress and Affect Detection) contains data of 15 subjects during a stress-affect lab study, while wearing physiological and motion sensors."
* [BAUM-1](https://archive.ics.uci.edu/ml/datasets/BAUM-1)
  - Description: "BAUM-1 dataset contains 1184 multimodal facial video clips collected from 31 subjects. The 1184 video clips contain spontaneous facial expressions and speech of 13 emotional and mental states."
* [BAUM-2](https://archive.ics.uci.edu/ml/datasets/BAUM-2)
  - Description: "A multilingual audio-visual affective face database consisting of 1047 video clips of 286 subjects."
* [EMG data for gestures](https://archive.ics.uci.edu/ml/datasets/EMG+data+for+gestures)
  - Description: "These are files of raw EMG data recorded by MYO Thalmic bracelet."


### Miscellaneous Data Sets
None of this is really directly related to work stuff, but most of these data sets tend to be easier
to play with and experiment on.  Many of them are fairly well recognized from appearing in many papers,
online courses, and blogs, etc.

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
* [Leaf Data Set](https://archive.ics.uci.edu/ml/datasets/Leaf)
  
  
