# The Scenario

**The Data**:  Let's say you are working with a data set about depressed subjects.  Specifically, you gain some
data at intake about each subject: demographic/socioeconomic (e.g., gender, age, race, income, employment), 
criminal (e.g., arrests), drug background (e.g., primary substance of abuse, frequency of abuse, etc), and
maybe even a questionnaire or two is filled out (e.g., PHQ-9, HAM-D).  Sometimes all this information
is available.  Other times, it's not: the patient may skip items on the questionnaire, or it might never
make it to the database.  Maybe they opt to not fill out a bunch of demographic data, etc.

Let's say we have about 5 years of this type of data.

**The Goal**:  We want to estimate at intake whether this person will complete treatment, or dropout.  That is,
we want to deploy a prediction model that takes in the relevant data at intake and provides the clinic with some
estimates (e.g., classify as a dropout or completer, estimate the number of treatments taken before 
completion or dropout, etc).  Ideally, uncertainties will be included in these estimates so that the information
can be used intelligently.  Ultimately, the model is guiding the clinic how to prioritize resources (time,
personnel hours, etc), treatment approaches, and so on.

**The Setback**:  Yes, we can create a predictive model given the historical data.  Simple, right?  Just
split the data up in training, validation, and test sets, establish a meaningful model metric, and make a model
that wows in validation, and doesn't disappoint on test. 

Except that we go beyond the test set in deployment.  See, on the test set, we just made a prediction, then
checked if we were right.  In deployment, we will also make a prediction, but we will NEVER get to check if we 
were right.  This is because in deployment, the intention is to intervene on these predictions -- if someone is
likely to dropout after 2-3 treatments, but needs at least 10, we are likely going to expend more resources
on that subject, hoping to convert a dropout into a completer.  After such an intervention, it is
no longer clear whether your model is bad or your intervention/treatment is really good!  

This is where things like A/B testing crop up in marketing: did our email campaign influence more purchases,
and if so can we quantify that?  This, in general, is where causal inference comes into play.  

But causal inference is hard, and I'm just trying to write about whether or not we should delete, impute, 
or preserve missing values in nominal categorical variables.  

So, for sanity's sake, let's assume
no interventions take place for now: we just want to nerd out with the data in hand.  If the model is good on 
test, we'll be happy to deploy it.  And in deployment, we will make predictions, but do nothing:  we'll 
simply wait to see whether the model is right or wrong.  Kind of like a weather forecast.  

Ok, let's focus on missing data.

# The Missing Data
## The Predictor Variables
Also, there is a marriage variable
with the levels: never married, now married, separated, divorced, MISSING/UNKNOWN/NULL.  

Should we remove any row with missing data?  Should we somehow impute these categorical variables?  If
so, then how?  

It should be no surprise that this is all context-dependent.  Let's dive in...




## The Target Variable
The first discrepancy arises: not everyone fits neatly into our universe of completers and dropouts.  Turns out
that some patients exit treatment for other reasons.  For example, death, incarceration, or relocation. Some
folks are even more mysterious: they reason for exit is MISSING/NULL.

The last reason is classic missing data.   But what of death, incarceration, or relocation?

Comleted case analysis (CCA) would have us remove the missing data.  But what should we do about the
available, but not very straightforward data?
