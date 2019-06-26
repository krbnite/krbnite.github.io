

* time-based leakage
  - a predictor that would not be available at the time of deployment
  - e.g., predicting if someone will dropout of a treatment program using the
    amount of time they remained in treatment before completing or dropping out

* validation set leakage (ok, if you also have an additional test set for later)
  - happens when one uses validation metrics to guide further model design choices,
    such as what type of model, what variables should be used, hyperparameters, etc
  - this is why it can be wise to use CV on the training set to tune hyperparameters
    of a particular model type, select best model, retrain on full training set, and
    get one number back from the validation set (then after all is said and done, 
    re-evaluating all finalized models on an additional test set)
    
    
* leakage from improper scaling, normalization, and/or transformation
  - using Box-Cox? only use training set to define parameters, then apply learned
    transformation to validation set
  - z-scoring?  same thing
  - PCA? same thing: learn the rotational transformation on training set


* unavailability
  - feature leakages are often defined in a time-based way
  - it's a little more than just time-based though, and specifically is about
    what is available at time/point of prediction
  - for example, you might have a data set with feature x1,x2,x3, and y, and
    you might know that x3 occurs after y, so you don't use it;  what you
    might not realize is that though x2 occurs before y in time, we do not
    have access to that data until after y -- that is, it is unavailable at
    the point of prediction

* group leakage
  - this one is sometimes controversial and seems to depend on a few things
  - basically, if you have 1000 records of 100 patients, then there is an uneasy feeling
    that having the same patient in both the training and validation sets is a source
    of leakage
  - the idea is that the model is supposed to generalize to other patients, so the model
    metric (e.g., accuracy) will be overly optimistic since it was only tested on patients
    that had some representation in the training data
  - for example, think of 2 people who have multiple records detailing STD test outcome, where
    one of them is just paranoid, so goes often and comes up clean, and the other is quite
    frisky and undiscerning and often comes up dirty... the model learns that the variable set
    associated with person 1 (e.g., gender=g1, height=h1, education=e1, etc) is highly 
    likely to come up clean, while the variable set w/ person 2 (g2,h2,e2,etc) is likely
    to come up dirty...  some of those variables can be completely coincidental, with
    no true association, but the validation accuracy won't reflect that...
  - this is a legitimate concern, but my gut says that on very large data sets, this becomes
    less of an issue -- that is, you might have the same patient represented in training
    and validation, but there will many other "look alikes" in each set w/ minor tweaks and
    different outcomes...
  - this is sometimes also called things like "partition leakage"

* oversampling leakage
  - say you have fairly imbalanced target classes, so you use a technique like SMOTE or just plain ol' oversampling
    the minority class
  - if you do this before splitting out the training set, then the training set will have distributional information
    (or straight-up duplicates) of data in the validation and/or test sets....









--------------------------------------

# Some References & Further Reading
* [Leakage in Data Mining: Formulation, Detection, and Avoidance](https://www.researchgate.net/profile/Claudia_Perlich/publication/221653692_Leakage_in_Data_Mining_Formulation_Detection_and_Avoidance/links/54418bb80cf2a6a049a5a0ca/Leakage-in-Data-Mining-Formulation-Detection-and-Avoidance.pdf)
* [Seven Types of Target Leakage in Machine Learning and an Exercise](https://www.researchgate.net/publication/327477467_Chapter_24_Seven_Types_of_Target_Leakage_in_Machine_Learning_and_an_Exercise)
* YouTube: [Target Leakage in Machine Learning](https://www.youtube.com/watch?v=UOxf2P9WnK8&feature=youtu.be)
  

* [Stand Up for Best Practices](https://towardsdatascience.com/stand-up-for-best-practices-8a8433d3e0e8)
  - just an interesting read.....

