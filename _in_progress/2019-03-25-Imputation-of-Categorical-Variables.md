



Let's say you are working with a data set about depressed subjects.  Maybe you want to estimate
at intake whether this person will complete treatment, or dropout.  Given the 
historical data, this might be possible -- that is, until you begin intervening on these predictions, at which point
it might not be clear whether your model is bad or your intervention/treatment is really good!  So, let's assume
no interventions take place for now: we just want to nerd out with the data in hand, split it into training, validation,
and test sets, and ultimately predict whether or not a subject in the test set is a completer or a dropout.  (We'll
save worrying about the real world for some other time! :-p)


Ok, so we look at the data -- and it's complex.  For example, turns out not everyone fits neatly
into "completer" or "dropout", but may exit treatment due to death, incarceration, relocation, or "other".  And
maybe this variable is sometimes not filled out: MISSING/UNKNOWN/NULL.  Also, there is a marriage variable
with the levels: never married, now married, separated, divorced, MISSING/UNKNOWN/NULL.  

Should we remove any row with missing data?  Should we somehow impute these categorical variables?  If
so, then how?  

It should be no surprise that this is all context-dependent.  Let's dive in...


# The Target
We are interested in dropout/retention as an outcome.  Let's say the data set includes a variable,
DISCHARGE_REASON, which has the following levels and percentages:
* CT - completed treatment (48%)
* DO - dropped out prior to completing treatment (29%)
* IN - incarcerated prior to completing treatment (2%)
* DI - died prior to completing treatment (1%)
* RT - relocated/transferred to another facility/location prior to completing treatment (17%)
* UK - UNKNOWN/MISSING/NULL (3%)

What we do has everything to do with our goal: is this patient likely to dropout or complete treatment.  

There are a few approaches we can take: 
* make this a binary classification problem (CT/DO) and throw out other levels of
  DISCHARGE_REASON before splitting data set into training/validation/test
* make this a multiclass classification problem, e.g., DO/CT/OTHER
* rescope our problem as a completion vs non-completion problem (CT/OTHER)


For example, 
we are not trying to predict death.  Since
death does not neatly fit into completion or dropout, I think we can remove them.  Think about the
patient intake process: our goal is to estimate a patient's likelihood of completing treatment.
