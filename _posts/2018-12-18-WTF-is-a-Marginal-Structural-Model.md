---
title: WTF is a Marginal Structural Model
layout: post
tags: statistics causal-inference
---

Machine learning models are great, right?!   

Obviously, that's a set up: the answer is "it depends."  Sure, ML techniques are great if your focus is on  
attaining highly accurate classification and prediction models.  But that's not always the goal, or at the least -- not
always the only goal.

Prediction is useful, for example, when wanting 
to know whether it will rain tomorrow.  If rain is predicted, you'll bring an umbrella to work.  Importantly, whether you
bring an umbrella to work will have no effect on whether or not it actually rains.  

However, maybe you're not interested
in the weather forecast as an endpoint, but as an input variable: you really want to know the chance that your flower 
garden shrivels and dies over the next week.  The forecast calls for all hot, dry, sunny days -- and your model says chances
are high!  With this information, you make sure the sprinklers are working so the flowers get watered throughout
the week, in which case your flowers likely will not shrivel and die... Here, your goal is still to have a model 
that makes great predictions, and again those predictions are used to suggest an action or intervention -- but the outcome
is changed!  Your intervention increases the likelihood that the flowers will live, despite the model's prediction.

How does one measure success for a model that intends to undo its own predictions?  That is, metrics used 
when training and validating the model will no longer be meaningful once interventions occur.  At least if those 
interventions are not properly accounted for!

Whether or not your model is working isn't your only problem here: you don't even really know if your 
intervention is doing anything.  Those flowers might have lived if the sprinkler was broken.  

Anyway, hopefully I motivated it enough...

Welcome to the world of marginal structural models!

# Marginal structural what?
Actually, it would have been more accurate to say, "Welcome to the world of causal inference!", but I  
didn't know that until I began exploring MSMs -- and what comes below are some introductory notes I took while doing so.

Let's proceed!

A marginal structural model is a type of statistical model used for causal inference.  The term mostly crops
up in epidemiology -- you know, the "study (scientific, systematic, and data-driven) of the distribution (frequency, pattern) and determinants (causes, risk factors) of health-related states and events (not just diseases) in specified populations (neighborhood, school, city, state, country, global)." (Source: [Principle of Epidemiology](https://www.cdc.gov/ophss/csels/dsepd/ss1978/index.html))

Said another way:
> "Marginal structural models are a multi-step estimation procedure designed to control for the effect of 
> confounding variables that change over time, and are affected by previous treatment." - [Williamson & Ravani [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980).

Said a better way (modified to respect the flower example above):
> “Ideally, the true causal effect of [WATERING THE FLOWER GARDEN] would be estimated by comparing what actually happened to  [THE FLOWER GARDEN] given [IT WAS WATERED] with what would have happened to the same [FLOWER GARDEN] if (contrary to fact) [IT HAD NOT BEEN WATERED]. Since we can rarely observe the same [FLOWER GARDEN] under both conditions (even in a crossover randomized controlled trial carry-over effects induce systematic differences), the only way to determine causal effects is to compare two [FLOWER GARDENS] that are identical in every way except that one [FLOWER GARDEN] was [WATERED] and the other was not. Marginal structural models (as other causal models) attempt to fully adjust for measured confounders to enhance group comparability and estimate causal effects in a similar way. While these methods have been found to replicate results of randomized controlled trials when all their assumptions are met, only randomization can ensure comparability of treatment groups by balancing both measured and unmeasured confounding factors.” - Same people: [Williamson & Ravani [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980).



# What is a structural model?   
If you want to know what a "marginal structural model" is, then maybe you want to first know
what a "structural model" is.  Basically, "structural" is synonymous with "causal" -- these are models that
aim to identify causal relationships.

Marginal structural models are causal in that "they model the probabilities of counterfactual variables" ([Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)). They are called "marginal
structural models" instead of "marginal causal models" because -- well, because that's what we call 'em:
* "in the econometric and social science literature, 
models for counterfactual variables are often referred to as structural." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)
* "models exploring causal relationships are referred to as structural in the econometric and social sciences 
literature.” - [Williamson et al [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980)


## Counterfactural Excursion 
You might be asking: WTF is a counterfactual variable?  

Good question!

A counterfactual variable is a variable that exists in a parallel universe: something that is counter to fact, i.e.,
contrary to observation.  That is, if you took ibuprofen for your headache an hour ago, you did not also "not take
ibuprofen for your headache an hour ago."  One thing actually happened, and the other thing is counterfactual: a huge
part of causal inference is asking "what would have happened if...?"

Interestingly, this "counterfactual excursion" was actually an "actual excursion."  Nice!  (Or, counterfactually: not nice!)


# What is a marginal model?  
Ok, so we know that a structural model attempts to infer cause-and-effect relationships from the data.  But ultimately,
we want to know what marginal structural models do!  What does "marginal" mean? 

MSMs are called marginal models, because: 
* "they model the marginal distribution of the counterfactual random variables Y[a=1] and Y[a=0] rather than the joint distribution." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)
* "they use the marginal distribution of the treatment (or exposure) variable at any time point to weight its relationship with outcome. Marginal in statistics means ‘unconditional’ —- i.e. not conditional on other variables, like the probabilities of one variable on the margins of a contingency table of two discrete variables." - [Williamson et al [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980)



# What is a saturated model?
MSMs are also considered saturated... What does that mean?

Marginal structural models are considered saturated, because: 
* "each has two unknown parameters and thus each model places no restriction on the possible values of the two unknown probabilities” pr(Y[a=0] = 1) and pr(Y[a=1] =1)." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)



# References & Further Reading
* Advanced reading list: [Introduction to Marginal Structural Models](https://epiresearch.org/serlibrary/serplaylists/introduction-to-marginal-structural-models/)
  - Robins et al [2000]: [Marginal Structural Models and Causal Inference in
Epidemiology](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)
    * Note to Self: I want read this one again after having read and watched other sources
  - Sato & Matsuyam [2003]: [Marginal Structural Models as a Tool for Standardization](https://epiresearch.org/wp-content/uploads/2014/07/Sato_EPI_2003_14_680.pdf)
    * Haven't read this one yet...
*	Helpful Dissertation:  Ewings [2012]: [Practical and theoretical considerations of the
application of marginal structural models to
estimate causal e§ects of treatment in HIV
infection](http://discovery.ucl.ac.uk/1346448/1/1346448.pdf)
  - Note to Self: left off on pg 21 (KEEP READING)
*	Very nice intro article:  Williamson & Ravani [2017]: [Marginal structural models in clinical research: when and how to use them?](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980)

# Courses & Tutorials
* Coursera: [Crash Course in Causality](https://www.coursera.org/learn/crash-course-in-causality)
  - [Lecture on MSMs](https://www.coursera.org/lecture/crash-course-in-causality/marginal-structural-models-EUpei)
  - Plan: Take a step back from MSMs and learn more about causal inference in general
* YouTube Playlist on Causal Inference:  https://www.youtube.com/watch?v=RtBY1zRaE_4&list=PL2yD6frXhFoYSO4MF3utWG_UUv2bhNZj0
  - only a few mins into 1st video (KEEP WATCHING)
