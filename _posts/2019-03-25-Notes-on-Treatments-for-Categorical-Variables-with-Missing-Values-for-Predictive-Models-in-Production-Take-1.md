---
title: Notes on Treatments for Categorical Variables with Missing Values for Predictive Models in Production (Take 1)
layout: post
tags: predictive-modeling statistical-modeling easi
---

If you begin to search the web for info imputing categorical variables, you will find a lot of
great information -- but most of it is not in the context of deploying a real-time prediction model<sup>&dagger;</sup>.  

Case in point: complete case analysis (CCA).  You'll see this mentioned everywhere -- usually just before
some reason is given for why it could hurt your analysis.  There are many reasons why CCA can be a bad
idea, but one that really stands out when putting a model into production is that missing data is real:
if your model is expected to provide the best prediction possible on the fly, given the available data, 
then you cannot just conveniently ignore the request (or crash!) because some feature values are 
missing. 

After CCA is mentioned (by name or otherwise), you're likely to see something about imputation along the
lines of "replace missing values with the mean or median for continuous variables, and the mode 
for categoricals."  Many will will mention multiple imputation as a more sophisticated method.  For
categorical variables, another option usually mentioned is to keep it -- just encode "missing" as
its own category.  

"Keeping it" has been my default assumption.  Imputation might be great, but no matter how sophisticated the 
technique, an imputation still throws out information: the fact that "missingness" might hold predictive value
all its own.  

Ever the contrarian, I decided to not trust my assumption.  At the least, I wanted to find a reference
that justified it... 

The hits that came up in my initial Google searches were a bunch of fluff.  I was just as likely 
to stumble upon a blog post or mini-tutorial that advocates
"keeping it" as I was to find someone who says "keeping it" is a terrible method -- either argument being 
made without further detail and nuance.  Most blogs and tutorials I came across did not really pay much 
attention to the missing data problem.  The ones that did, did not attempt to paint a complete picture -- "toss
out any row with missing data, or impute it, or whatever -- then MACHINE LEARN THAT SHIT!"  (Cue guitar solo
and flashing lights!)

Granted, most of these hits were "light" machine learning reading...but the lack of depth and repetitive
advice was still unsettling:  just delete any row with missing data, or impute it, and/or keeping "missing" as 
its own category; rinse and repeat!  End of story.

But what are the implications of each choice?  Does a certain technique pair better with a certain 
model?  And maybe the most unaddressed question: why not just create a model for each combination
of input variables?  Or, at the least, why not use imputation and missing indicator techniques on variables
with occasionally missing data, while creating a set of dropped-variable models for more frequent
offenders?  I did not see these kinds of ideas widely discussed without digging much deeper... Instead,
the focus is more often on "one model to rule them all" (where "them all" is "all the data").

What makes this dearth of discussion more surprising to me is that most of
the hits that came up in my initial Google searches discuss predictive models, but only by name -- with 
almost attention to using the model to make predictions in a realistic setting...  To my mind, if your goal 
is prediction, then you probably plan on predicting something on yet-to-be measured
data.  For example, if you are in digital marketing, this might be data collected on a new potential 
customer.  Or if you're working with clinical data, this might be a social or demographic variable.  Point is, 
if the data set you have in hand came with missing data, then you will likely be making predictions on
data that occasionally has missing data too.  So it's hard to take an article about predictive modeling seriously 
when it recommends dropping any rows with missing data, then splitting the remaining data records into a training 
and test set...  You'll get a model, but who cares?  It's
gained you about as artificial an "intelligence" as possible -- one that will not work when deployed in the real
world!

By not paying attention to how a predictive model will be used in practice (even if just as a mental 
exercise), other issues creep in to various predictive models I've seen, which renders them useless beyond
an academic exercise.  For example, you will often see a 
predictive model created where a variable being used wouldn't be available for making predictions, e.g.,
a variable that is created in hindsight or a variable that is defined up to the moment of 
the event being predicted. For example, picture having historical data on students with variables like `freshman_gpa`,
`sophomore_gpa`, `junior_gpa`, `senior_gpa`, `total_days_in_high_school`, and `dropout`.  I've seen models
akin to predicting `dropout` using all the other variables.  That's crazy useless, right?!  Using all the variables
means that you are strictly taking a hindsight perspective, and not a predictive one.  To be predictive, one
would have to instead imagine when the model would be useful, say at the end of freshman year, at which
point a model running in real time would only have access to `freshman_gpa` (of the variables listed above).  You 
might be tempted to argue that they would have attendance records too, and so might be tempted to use 
the `total_days_in_high_school` variable too -- but this would be wrong since this variable in the data spans
4 years of high school.  In this scenario, a useful technology having access to only the data variables listed
would be to score dropout risk after freshman year (X = `freshman_gpa`), after sophomore year (X = `freshman_gpa`,
`sophomore_gpa`), and after junior year (X = `freshman_gpa`, `sophomore_gpa`, `junior_gpa`).  The
`senior_gpa` and `total_days_in_high_school` variables are not useful.  This is not to say that more
granular versions of these variables would not be useful, like `senior_gpa_q1` or 
`total_days_absent_freshman_year`, but we don't have them in our data set.


and are simply trying to predict
whether or not a student was a dropout or not, then this variable just gives you the answer:  only 
attended high school 100 days 

Ok, ok -- all this said, one hit on my Google search really stood out.  It was a response to this Quora post, [How can I deal with missing values in a predictive model?](https://www.quora.com/How-can-I-deal-with-missing-values-in-a-predictive-model).  As elsewhere, I found most of the discussion to have a indiscriminately-copy-and-paste vibe (e.g., try CCA or multiple imputation).  However, one response was actually very thoughtful and, IMO, answered the question being
asked.  

...drum roll, please...

I want to give an enthusiastic 
shout out to Claudia Perlich, whose answer really gets at the core of working with with missing values 
for a predictive model that will be used in production.  Her answer should really be highlighted in yellow at
the top of the page.  

Great passage about even the fanciest of imputation methods:  "What about imputation? Simply pretending that something that was missing was recorded as some value (no matter how fancy your method of coming up with it) is almost surely suboptimal. On a philosophical level, you just lost a piece of information. It was missing, but you pretend otherwise (guessing at best). To the model, the fact that it was missing is lost. The truth is, missingness is almost always predictive and you just worked very hard to obscure that fact ..."  

Wow -- Great stuff... And it gets better.  Perlich  describes how to maintain this "missingness" information for not
only nominal variables, but how to generalize to ordinal and numeric variables as well.  

--------------------------------------------------------------------------------------

In what remains of this post, I include some notes that outline methods used to treat missing values... Ultimately,
my goal is better understanding this in terms of nominal and ordinal data, especially
when considering using that model in the "real world".  But I include notes about missing data in general,
and for now I do not go deeply into anything in particular.  (I plan on having some follow-up posts that
dive deeper.)  

# Context, Context, Context
The first question you should ask is: do I even have to worry about imputing this data?

Are you even building a model, or are you attempting a sophisticated statistical analysis?

Given that you are building a model -- well, what's the point of your model?

If you are making a predictive model, it is likely you plan on using it -- not only on the test set,
but in a real setting (e.g., on a mobile device, or a desktop application used by a clinician, etc).  What
does deployment look like?  Will the data generating / collection processes remain the same, or will
your model demand certain standard be adhered to?

If the data collection process remains the same, then there is every reason to expect that you will
have a similar distribution of NULL values in your data features going forward.  This means you have to build
a model that deals with this -- some approach to filling missing values in real time so that a result
can be provided. 

On the other hand, if your model will guide the data collection policy going forward -- e.g., if the enduser 
must input the necessary data for a result -- well, then you might not really have to deal with NULLs.  If you have
enough historical data, you might choose to throw out any row with a NULL, assuming that downstream model
users will necessarily have to input all required data.  

The are a few general approaches to dealing with missing data:
* Delete cases/records/rows with missing data
  - examples include complete case analysis (CCA) and inverse probability weighting (IPW)
  - not particularly useful if your model will have to deal with missing data anyway
* Keep cases/records/rows and impute missing values
  - Globally: Sometimes imputation is coarsely done for a feature, e.g., with median/mode impute,
    which might be computationally efficient, but can also be harmful 
  - Locally:  Sometime imputation considers more localized information (e.g., with nearest neighbor, random forest, etc),
    which might be a better guess than using median/mode, but can also contribute to overfitting the training set
  - Imputation 
* Keep cases/records/rows and do not impute values
  - In other words, "missing" might serve well as its own class
  - example: questionnaire asks "Are you feeling suicidal?" and the subject can choose "Yes", "No", or skip the question
  - you might get a sense here that "skipping the question" is correlated with choosing "Yes", and in fact there have been
  studies that found this to be the case
  - in this case, a NULL is meaningful, so leave it as its own class


# Delete It
As I point out in the intro, deleting data records is not really a choice if one plans on running a model
in production.  That is, the model must be able to navigate any scenario it runs into, so there is no benefit
in training a model on an unrealistic scenario.  Furthermore, if data is missing, then missing data is probably an important
part of the data generating process.  Let's not ignore that!

But let's take a look at these approaches anyway. 

## Complete Case Analysis
CCA also goes by the term "listwise deletion."  The most naive approach to this technique is to blindly throw out
any row that has a missing value, disregarding the fact that this might alter the population and bias your 
results.

Clearly, CCA is not applicable for running a predictive model in production.  For example, when 
deploying a model that must make or guide
decisions, CCA completely misses the point: throwing out an incoming request is likely unacceptable.  Instead, the 
user understandably might expect that the deployed prediction model will provide some output 
whether or not there is data missing -- an estimate, some form of guidance, or at minimum a 404-esque message that
reads, "Not enough data. This models sucks!"


You might ask: "But if CCA is so bad, then why is it always mentioned?"  

I might answer:  In short, throwing out rows of data might be a valid approach 
when exploring data or estimating various population statistics.

There are some obvious and not-so-obvious pitfalls.

An obvious drawback:  throwing out any row that has at least one missing feature
can trim down a data set pretty quickly if there is a column that is mostly always NULL.  

An oft-sighted solution:  Sometimes you will see a modeler first throw out any
columns that are frequently NULL, say 70% of the time or more.  This always gives me pause: what if any 
time the column had a value, it provided near 100% certainty in the outcome?  

Let's assume a data set does not have too many features with missing values, and for those features
with missing values, not too many missing values in general.  Then one might ask: What conditions must be 
met to ensure that the CCA subpopulation is a fair representation of the original population?

From [Rubin 1976](https://academic.oup.com/biomet/article-abstract/63/3/581/270932):
> "Often, the analysis of data ... proceeds with an assumption, either implicit or explicit, 
that the process that caused the missing data can be ignored. ... The question
to be answered here is: when is this the proper procedure?"

In these [lecture notes](https://www.jhsph.edu/research/centers-and-institutes/johns-hopkins-center-for-mind-body-research/resources/Brendan_Klick_20Mar07.pdf), the context in framed in categories of missing data:
* MCAR (missing completely at random)
* MAR (missing at random)
* MNAR (not missing at random)

[Figure 1 in this paper](https://academic.oup.com/ije/advance-article/doi/10.1093/ije/dyz032/5382162) shows
a bunch of great dMCAR, MAR, and MNAR causal diagrams.  Definitely check it out to help build intution for
these terms.

Even if you are building a model that will go into production, these things are important to understand.  For
example, if you are imputing by plucking values at random from a feature's estimated probability distribution:
how do you estimate that distribution?  Do you do it with all available data for that feature, or do you
estimate the distribution using only the complete cases?  What if there is a difference?!  The more you know,
the better -- right?


## Inverse Probability Weighting (IPW)
From the model-in-production perspective, this is just a fancier CCA.  The idea is to basically
do CCA, but then tweak things so that it's kind of like you didn't do CCA.  

Or, as [these guys](https://academic.oup.com/ije/advance-article/doi/10.1093/ije/dyz032/5382162) put it:
> IPW is a "weighted analysis, in which the complete cases are weighted by the inverse of the probability of being a 
> complete case. The weights are used to try to make the complete cases representative of all cases."

They also give an insightful reason why you might choose IPW over imputation:
> "Inverse probability weighting is an alternative to MI that can be advantageous when misspecification of 
> the imputation model is likely."

When restricting to only complete cases/records/rows, the data may become biased.  IPW is commonly used
to correct this bias.  However, the data collection process might itself not reflect the true population; in 
this case, IPW can also be used to adjust/tune the sampling frequencies of certain groups so that the model
will reflect the actual population.

# Impute It
In between "delete it" and "keep it", there exist imputation techniques.  These take into account available information,
such as that found in the other features and neighboring data points in the N-dimensional feature space.

Disallowing data deletion, the  go-to approach in many tutorials and papers is to guess at 
the value of missing values.  With no information other than
population-scale information, this would be the mean, median, or mode -- the best guess you have knowing nothing
else about the current instance.  You can also acknolwedge that you do have other information about the 
current instance, e.g., other features that put the current instance into a known cluster, the median, mean, or
mode of which might be a better fill for the missing value.  

## Multiple Imputation
Done all the time for statistical modeling... Might not have quick response time in production.  


## Random Forests


# Keep It
Missing data may represent a loss of information, but imputing the value may
induce further loss: it deletes the fact that the data is missing.


* Keep cases/records/rows and not impute ("missing" is good as its own class)
  - example: questionnaire asks "Are you feeling suicidal?" and the subject can choose "Yes", "No", or skip the question
  - you might get a sense here that "skipping the question" is correlated with choosing "Yes", and in fact there have been
  studies that found this to be the case


## Multi Model
**Multi-Model**:  Though the model cannot ignore the request (i.e., cannot throw out the "current row of 
data"), it might be ok ignore the features hosting those missing values (e.g., by rerouting to another model 
trained on less features). This might become unruly if one is dealing with 100's or 1000's of input features, but
conceptually it is a great solution.  Why try to bandage up just one model, when you can simply deploy
the right model?  One thing that struck out to me is the absolute dearth of blogs/tutorials/articles recommending this 
approach.  Maybe it feels ugly?  Maybe it doesn't strike that "one ring to rule them all", Lord-of-the-Rings 
vibe.  I don't know... 

But after some digging around, I found a paper that suggests this approach is way 
better than imputation:
* 2007: Saar-Tsechansky & Provost: [Handling Missing Values when Applying Classification Models](http://jmlr.csail.mit.edu/papers/volume8/saar-tsechansky07a/saar-tsechansky07a.pdf)). 

In this paper, they
refer to the smaller-feature-set models as reduced models.  

Note to Reader: You might do yourself a favor and read this paper!

Note to Self:  Definitely want to dig deeper into these authors' papers in general.


## As Is, As Is
**As Is**: And lastly (for this section at least), the model can 
choose to find meaning in the missingness itself (i.e., leave it as its own value).  This approach is
often listed for nominal categorical variables, but does not get the same amount of attention ("page space")
as imputing via clustering or random forests might get.  Probably because it's simpler and easier to say!  But
this approach might be best.  Data is missing: how likely is that the missingness is not meaningful in some way?  Imputataion
might best predict the missing value, but does it best predict the outcome variable that we're actually interested
in?  What we do know is that imputation throws away the information that this value is missing, which might
be important!  Leaving a missing value "as is" for a nominal variable effectively adds one more level to that
variable: a binary variable becomes tertiary, a tertiary become quaternary, and so on!  I like this option because
it feels like a compromise between "imputation" and "multi-model".  




# References

### Some Generic Stuff
* [How to Handle Missing Data](https://towardsdatascience.com/how-to-handle-missing-data-8646b18db0d4)
* [How can I deal with missing values in a predictive model?](https://www.quora.com/How-can-I-deal-with-missing-values-in-a-predictive-model)
  - See Claudia Perlich's response


### CCA Stuff
* StackExchange: [Is Listwise Deletion / Complete Case Analysis biased if data is not MCAR?](https://stats.stackexchange.com/questions/43168/is-listwise-deletion-complete-case-analysis-biased-if-data-are-not-missing-com)
* 1976: Rubin: [Inference and Missing Data](http://people.csail.mit.edu/jrennie/trg/papers/rubin-missing-76.pdf)
* 2007: Klick:  [Missing Data in Health Research](https://www.jhsph.edu/research/centers-and-institutes/johns-hopkins-center-for-mind-body-research/resources/Brendan_Klick_20Mar07.pdf)
* 2013: Jonathan Bartlett: [When is complete case analysis unbiased?](http://thestatsgeek.com/2013/07/06/when-is-complete-case-analysis-unbiased/)
 - Short, insightful blog article on when CCA can be used
 - However, the article is not from a deployment perspective: you might develop a great statistical model, achieve
  heavenly insights, then still not be able to use it live in production
* 2014: Jonathan Bartlett et al: [Improving upon the efficiency of complete case analysis when covariates are MNAR](https://academic.oup.com/biostatistics/article/15/4/719/267454)
 - An insightful publication about the underappreciated circumstances when CCA is valid, and when it might be more
   plausible than the assumptions built into multiple imputation (MI) or inverse probability weighting (IPW) methods
* 2016: Mukaka et al: [Is using multiple imputation better than complete case analysis for estimating a prevalence (risk) difference in randomized controlled trials when binary outcome observations are missing?](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4957845/)
* 2019: Rachael Hughes et al: [Accounting for missing data in statistical analyses: multiple imputation is not always the answer](https://academic.oup.com/ije/advance-article/doi/10.1093/ije/dyz032/5382162)


### IPW
* [Review of inverse probability weighting for dealing with missing data](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.822.7759&rep=rep1&type=pdf)

### MI
* [Multiple imputation for missing data in epidemiological and clinical research: potential and pitfalls](https://www.bmj.com/content/338/bmj.b2393.extract)

### RFs
* [Overcoming Missing Values In A Random Forest Classifier](https://medium.com/airbnb-engineering/overcoming-missing-values-in-a-random-forest-classifier-7b1fc1fc03ba)
* [Imputing Missing Data and Random Forest Variable Importance Scores](http://rnowling.github.io/machine/learning/2015/12/15/imputing-missing-data.html)
* [Large Scale Decision Forests: Lessons Learned](https://engineering.sift.com/large-scale-decision-forests-lessons-learned/)


--------------------

Looks like Claudia Perlich turned her Quora answer into a LinkedIn post (or vice versa):
https://www.linkedin.com/pulse/dealing-missing-values-predictive-models-claudia-perlich/
