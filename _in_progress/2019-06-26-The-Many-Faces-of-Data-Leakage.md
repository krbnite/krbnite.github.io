

* time-based leakage
  - a predictor that would not be available at the time of deployment
  - e.g., predicting if someone will dropout of a treatment program using the
    amount of time they remained in treatment before completing or dropping out
  - Salesorce folk call this "hindsight bias"

* validation set leakage (ok, if you also have an additional test set for later)
  - aka tuning leakage
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
  - more concrete example:  in many healthcare data systems, data is manually uploaded
    by someone; in a retrospective study, it might appear that data is model ready
    after a doctor's visit, but in reality it might be uploaded at the end of every week;
    even it is generally uploaded at the end of everyday, this means that the deployment
    predictions can only be made the day after each patient's visit, etc
  - from the example, you can tell that this affects the missginess distributions of
    data from training to deployment

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


* external / disallowed leakage
  - this is similar "unavailability leakage"
  - it's when a modeler uses data that should not be included in the model, usually defined by set rules
    (e.g., in a Kaggle competition) 
  - example I've seen given:  Titanic competition on Kaggle has top of leaderboard at 100%, where 
    these folks were able to look up data on who survived/orNot and include it in their model (this is
    very similar to "unavailability leakage", right?
  - Kaufmann/Perlich paper talks a lot about winners of competitions doing this; not sure how it applies
    to industry as a unique form of leakage (i.e., if it is not classified as any of the other types of
    leakage, then why not use it if it meets all the parameters necessary for deploying the model, etc?)
  - this can essentially be generalized from Kaggle competitions to the real world by realizing it is
    really "disallowed leakage":  in the real world, we must comply to various data privacy standards,
    such as HIPAA and GDPR, as well as fairness/unbiasedness standards (e.g., cannot develop model that
    somehow favors recommending better treatments based only on gender or race)
 
* foresight leakage
  - the Larsen paper gives an interesting example of when surveying patients, where they
    already know (or have predetermined) their target status; the example given is about
    predicting whether or not someone will go to the gym tomorrow based on survey questions, 
    where asking whether or not the recipient will go to the gym tomorrow is intended to
    be info on "how the recipient feels about doing it today", but in reality includes info
    already in the recipient's head, such as "no, they don't go to the gym on Tuesdays" or
    "yes, it's the only day of the week they get to go," or "no, they are leaving on vacation
    tomorrow", etc ---
  - as you can see, this type of leakage is from an illegitimate feature, but the illegitimacy
    was not obvious to the modelers at first
  - the predeterminedness of the answers of many of the recipients is similar to including the
    target variable in the prediction model, so is very likely to give an optimistic accuracy...
  - that said, one thing I don't fully understand is, if the model was going to be deployed in
    the way it was designed, wouldn't the idea be that it can only run when asking a gym member
    if they will come tomorrow?  What I mean is, it's not overly optimistic given that it must
    be deployed in this way...  Gnomesayn'?
    

* Boosting Leakage
  - basically, boosted models suffer from leakage
  - packages like CatBoost address this

* Stacking Leakage
  - if you train N models on training set T, then using T to train stack(M1,...,MN) will
    introduce leakage
  - instead train 1st level models on say 4/5 of T, then use remaining 1/5 of T to train stack(M1,...,MN)
  - this can actually be done in a proper k-fold CV way:  each time, the 1st level models are trained
    on the training fold (which itself can be folded for some purpose, like hyperparameter tuning)
    and the stacked model is trained on the validation fold 

* augmentation leakage
  - this is similar to oversampling leakage
  - if you use image augmentation (tweaks in coloration, brightness, rotation, scaling) to generate
    a larger data set, then split the augmented data set into training and test, you have literally injected
    training images into test (that is, a slightly darker or discolored version, or slightly scaled, etc)
  - basically, same solution as usual: do it on the training set only




--------------------------------------

# Some References & Further Reading
* [Leakage in Data Mining: Formulation, Detection, and Avoidance](https://www.researchgate.net/profile/Claudia_Perlich/publication/221653692_Leakage_in_Data_Mining_Formulation_Detection_and_Avoidance/links/54418bb80cf2a6a049a5a0ca/Leakage-in-Data-Mining-Formulation-Detection-and-Avoidance.pdf)
* [Seven Types of Target Leakage in Machine Learning and an Exercise](https://www.researchgate.net/publication/327477467_Chapter_24_Seven_Types_of_Target_Leakage_in_Machine_Learning_and_an_Exercise)
* YouTube: [Target Leakage in Machine Learning](https://www.youtube.com/watch?v=UOxf2P9WnK8&feature=youtu.be)
  - [https://github.com/YuriyGuts/odsc-target-leakage-workshop](https://github.com/YuriyGuts/odsc-target-leakage-workshop)
* [Data Leakage in Healthcare Macine Leanring](https://healthcare.ai/data-leakage-in-healthcare-machine-learning/)
* Till Bergmann: Video Lecture: [Hindsight Bias: How to Deal with Label Leakage at Scale](https://www.datacouncil.ai/talks/hindsight-bias-how-to-deal-with-label-leakage-at-scale)
  - Simlar article (also by Einstein Salesforce): [Back to the Future: Demystifying Hindsight Bias](https://www.infoq.com/articles/data-leakage-hindsight-bias-machine-learning/)

--------------------------------------

* [Stand Up for Best Practices](https://towardsdatascience.com/stand-up-for-best-practices-8a8433d3e0e8)
  - just an interesting read.....
  - follow-up blog: [Deep Learning, Nature and Data Leakage, Reproducibility and Academic Engineering](https://flavioclesio.com/2019/06/25/deep-learning-nature-and-data-leakage-reproducibility-and-academic-engineering/)

