---
title: More Notes on Missing Data for Statistical Inference
layout: post
tags: statistics inference missing-data easi
---

As I've mentioned in previous posts, many of the references one will encounter when looking
up methods for dealing with missing values will be oriented towards statistical inference
and obtaining ubiased estimates of population parameters, such as means, variances, and
covariances.  The most mentioned of these techniques is multiple imputation.  I saw value in
digging deeper into this area in general, despite it not being optimized reading for
developing predictive models -- especially those that might run in real time in an app or at a
clinic of some sort.  The reason is that, in tandem with developing a great predictive model,
I generally like to develop corresponding models that focus on interpretability.  This allows me
to learn from both inferential and predictive approaches, and to deploy the predictive model while
using the interpretable model to help explain and understand the predictions.  However, once one
becomes interested in interpretability, one becomes interested in inference -- or, importantly,
unbiased estimates of population parameters, etc.  That is, I'm actually very interested in
unbiased estimates of means, variances, and covariances -- but in parallel to prediction, not
in place of it.

This post is mostly just a lot of notes from various papers I've read on the topic.

One thing I can say for sure: no one recommends on complete case analysis.  It is considered to produce
biased estimates of population parameters and associations, ruling it out for inference and causal modeling. And,
for predictive modeling, it (i) lowers the amount of data, and (ii) is implausible for using the predictive 
model on new, out-of-sample, incoming data records.  

Also: no one recommends things like mean replacement.  Single imputation methods are frowned upon too, 
at least from an inference / causal modeling perspective where multiple imputation methods rule (seriously,
I saw some simulation studies where they removed 50% of the data and the MCMC MI method still produced unbiased
estimates of the mean and variance).  

Notably, in most (if not all) of these statistical inference heavy works, imputation by decision tree-based
methods or by k Nearest Neighbors are not even mentioned.  I do have a collection of papers to get to
that do focus on some of these things... These will be for a different blog post. 

--------------------------------------------------------------
--------------------------------------------------------------


# 2009: Graham: Annual Reviews of Psychology: [Missing Data Analysis: Making It Work in the Real World](https://www.personal.psu.edu/jxb14/M554/articles/Graham2009.pdf)

"My aim here is to encourage researchers to use the missing data
procedures that are already known to be good ones. ... much
of the reluctance to adopt these procedures is related to the myths and misconceptions that
continue to abound about the impact of missing data with and without using these procedures."

"Sometimes the solutions are a bit ad hoc...but the solutions I present are known
to have minimal harmful impact on **statistical inference**..."


### Missing Value Taxonomy
"The major three missingness mechanisms are MCAR, MAR, and MNAR. These three kinds
of missingness should not be thought of as mutually exclusive categories of missingness, 
despite the fact that they are often misperceived as such. In particular, MCAR, pure MAR, and
pure MNAR really never exist because the pure form of any of these requires almost universally
untenable assumptions. The best way to think of all missing data is as a continuum between
MAR and MNAR." Assuming or accepting that all missingness is partially MNAR
"(i.e., not purely MAR), then whether it is MNAR or not should never be the issue.
Rather than focusing on whether the MI/ML assumptions are violated, we should answer the
question of whether the violation is big enough to matter to any practical extent."

* MAR: 
  - "With MAR, the missingness (i.e., whether the data are missing or not) may depend on
    observed data, but not on unobserved data."
  - "If the cases for which the data are missing can be thought of as a random
    sample of all the cases, then the missingness is MCAR. This means that everything one might
    want to know about the data set as a whole can be estimated from any of the missing data 
    patterns, including the pattern in which data exist for all variables, that is, for complete 
    cases."
  - "Another aspect of MCAR that is particularly easy to understand for psychologists is that the
    word 'random' in MCAR means what psychologists generally think of when they use the term."
  - Like MCAR, "MAR missingness (i.e., when the cause of missingness is taken into account) also 
    yields unbiased parameter estimates."
* MCAR: 
  - "MCAR is a special case of MAR in which missingness does not depend on the observed
    data either."
  - "The word 'random' in MAR ... means something rather different from what psychologists 
    typically think of as random. In fact, the randomness in MAR missingness means
    that once one has conditioned on (e.g., controlled for) all the data one has, any remaining
    missingness is completely random (i.e., it does not depend on some unobserved variable)."
  - A "more preciseterm for missing at random would be conditionally missing at random."
  - "The main consequence of MCAR missingness is loss of statistical power. The
    good thing about MCAR is that analyses yield unbiased parameter estimates (i.e., estimates
    that are close to population values)."
* MNAR: 
  - "With MNAR, missingness does depend on unobserved data."
  - "The reason MNAR missingness is considered a problem is that it yields biased parameter estimates."

"Nonignorable (NMAR) methods may be very useful. However, it is not necessarily
true that any particular method will be better than MAR methods (e.g., normal-model MI or
ML) for any particular empirical study. It is well known that methods for handling nonignorable
data require the analyst to make assumptions about the model of missingness. If this model is
incorrect, the MNAR model may perform even less well than standard MAR methods. ... On the 
other hand, MNAR methods such as pattern mixture models may ...be excellent tools for describing 
the missingness in longitudinal fashion, thereby increasing one's confidence in many instances about 
the true nature of the missingness."


#### Excursion:  Missingness in Psychological Data
A part of the reason I like reading papers from all sorts of fields is the bridge building aspect to 
it, as this author does here: "Statisticians talk about missingness mechanisms. But what they mean by 
that term differs from what social and behavioral scientists think
of as mechanisms. When I (trained as an experimental social psychologist) use that word, I
think of causal mechanisms. What is the reason the data are missing? Statisticians, on the other
hand, often are thinking more along the lines of a description of the missingness."


### Old Methods of Dealing with Missingness
* **CCA**: "can be very useful. One concern ... is that it may yield biased parameter estimates," but
given the study design and model that might be used, the bias can be minimal.  "However, there 
will always be some loss of power ... because of the unused partial data ... making this method
an undesirable option. Still, if the loss of cases due to missing data is small (e.g., less than about
5%), biases and loss of power are both likely to be inconsequential."  He goes on to say that 
he would use it if less than 5% of the data records are affected, but would also understand if
he gets called on it in the peer review process.
* **Pairwise Deletion**: Due to all kinds of issues (biased estimates, no guarantee of positive definite correlation matrix,
inability to estimate standard errors), Graham "cannot recommend pairwise deletion as a general solution." However,
he does admit that he finds it to be a good, preliminary exploratory data analysis tool.
* **Mean Substitution**:  "I do not recommend."
* **Missingness Dummy Variable**:  "This approach has been discredited and should not be used (e.g., see Allison 2002)."
* Regression-Based Single Imputation: 
  - "Although the concept is a sound one and is the basis for many
    of the modern procedures, this method is not recommended in general."
  - Basically, this type of procedure imputes data based on the regression equation, i.e., missing data is
    always filled in along the regression curve itself; given enough missing data, this will strongly damper
    the variable's variance, thus giving poor estimates of the population variance.  ("Imputed values from 
    single imputation always lie right on the regression line. But real data always deviate from the 
    regression line by some amount.")
  - The always-on-the-regression-line weakness is why multiple
    imputation was invented: "MI was designed to restore the lost variability found in single imputation, and the MI 
    strategy was designed to yield the correct variability."

### Modern Methods of Dealing with Missingness
* EM Algorithm:
  - Apparently there are many, so the one described here is EM algorithm that takes in data with 
    missing values and spits out a maximum-likelihood variance-covariance matrix and vector of
    means.
    * I'll skip the descripton of the EM algorithm here, as it went over several paragraphs
  - Since EM is a maximum likelihood (ML) method, "the parameter estimates (means,
    variances, and covariances) from the EM algorithm are excellent. However, the EM algorithm does not 
    provide standard errors as an automatic part of the process."  However: "One could obtain an estimate of 
    these standard errors using bootstrap procedures."
  - "the lack of convenient standard errors means that EM is not particularly good for hypothesis testing," 
    however, "several important analyses, often preliminary analyses, don’t use standard errors anyway, so the
    EM estimates are very useful." For example, "it is often desirable to report means, standard deviations, 
    and sometimes a correlation matrix in one's paper. I would argue that the best estimates for these 
    quantities are the ML estimates provided by EM."
  - "The EM covariance matrix is also
an excellent basis for exploratory factor analysis
with missing data."

Not sure if it was stated directly...but I've read elsewhere the the EM method is "single imputation"
similar to regression-based single imputation above.  That said, Graham repeats again and again that the
EM method provides the best estimates for mean, variance, etc, so it clearly does not bias these
estimates like the regression-based imputation is said to do.  However, Graham notes several times that
the EM method will show much smaller standard errors on these parameters then is true in the population.

* Multiple Imputation (MI)
  - "The key to \[MI\] is to restore the error variance lost from regression-based single
    imputation. Imputed values from single imputation always lie right on the regression line.
    But real data always deviate from the regression line by some amount. In order to restore
    this lost variance, the first part of imputation is to add random error variance (random normal
    error in this case). The second part of restoring lost variance relates to the fact that each
    imputed value is based on a single regression equation, because the regression equation, and
    the underlying covariance matrix, is based on a single draw from the population of interest."
  - "In order to adjust the lost error completely, one should obtain multiple random draws from
    the population and impute multiple times, each with a different random draw from the population. Of 
    course, this is almost never possible; researchers have just a single sample. One option might 
    be to simulate random draws from the population by using bootstrap procedures (Efron 1982). Another 
    approach is to simulate random draws from the population using data augmentation (DA; 
    Tanner & Wong 1987)."
      * Graham uses software that implements the data augmentation approach
      * Graham describes DA as a "stochastic (probabilistic) version of EM."
      * Graham recommends N DA steps, where N is number of steps it would take EM to converge

**Concluding thoughts about MI/ML methods**: Multiple imputation and maximum likelihood "are
good procedures that are based on strong statistical traditions. They can certainly be improved
on, but by how much? I would argue that using MI and ML procedures gets us at least 90% of
the way to the hypothetical ideal..."


### Wrap Up
Auxillary variables: one basic theme throughout was use all the data for data imputation of missing
values.  That is, often a statistician will only want to include a few variables of interest in their
final model, however, that does not mean that one should restrict the variable set during the MI/ML
step!  "Probably the single best strategy for reducing bias (and increasing
statistical power lost owing to missing data) is to include good auxiliary variables in the missing 
data model ... these variables need only be good for predicting the missing values;
they need not be related to missingness, per se.

This paper couldn't be any more focused on true understanding of relationships between dependent and
independent variables, of accurately estimating population parameters, such as mean and variance.  The focus
on MI/ML was because these techniques ensure unbiased estimates of these parameters. There were many 
sections about assessing how much MNAR missingness might be in your data... and ultimately, how missingness
might affect your conclusions about correlations, parameter estimates, cause and effect, etc... The idea of
missingness was realizing that there is no perfect MAR or MNAR, and that missing data should be considered on
a continuum between the two, and so the right question is not "is this missing data MAR?" but instead "how much
is this data like MAR and how much like MNAR?", which then allows you to approach the problem similarly each time
and focus on whether or not the missing data introduces a negligible amount of bias o rnot....   The focus
was not on "getting the best predictions" at all... Seems like, independent of that, the MI/ML techniques
involved would still be best for predictive models...but it's not yet clear to me how these things would
work at run time (e.g., in terms of speed, or whatever).

Target/Outcome Var:  Don't think I included the quotes above, but I should. Graham goes on about how 
a lot of people have the wrong impression about including the target var in the MI process -- they're
usually afraid to, or think it's cheating.  He explains that it's actually wrong not to include it: that
the analysis actually is incorrect when it's not included!

------------------------------------------------

# 2002: Scheffer: Research Letters in the Information and Mathematical Sciences: [Dealing with Missing Data](https://mro.massey.ac.nz/bitstream/handle/10179/4355/Dealing_with_Missing_Data.pdf)

"Traditional approaches include case
deletion and mean imputation." During the 90s, interest "centred on Regression
Imputation, and Imputation of values using the EM (Expectation-Maximisation) algorithm, both of
which will perform Single Imputation."  The most recent method of interest this paper looks
at is multiple imputation.  Overall, 8 methods are compared.

### Missing Value Taxonomy
* MCAR: 
  - "The term 'Missing Completely at Random' refers to data where the missingness mechanism does not
    depend on the variable of interest, or any other variable, which is observed in the dataset. MCAR is both
    missing at random, and observed at random (This means the data was collected randomly, and does not
    depend on any other variable in the data set). This very stringent condition is required in order for case
    deletion to be valid, and missing data is very rarely MCAR."
* MAR: 
  - "The term 'Missing at Random' is a misnomer, as the missing data is anything but missing at random. The
    intuitive meaning of this term is better suited to the term MCAR. What MAR means is missing, but
    conditional on some other 'X-variable' observed in the data set, although not on the 'Y-variable' of interest."
* MNAR: 
  - "occurs when the Missingness mechanism depends on the actual value of the missing data. This is the most 
    difficult condition to model for."
* Ignorability: "MCAR and MAR are ignorable, for likelihood-based imputation methods, NMAR is not."


This paper was much less thorough than Graham's above.  It is a fairly simple, short simulation study. But 
it did have some succinct nuggets:
* Under 50% MCAR data, most methods produced means within 1% of the population mean, and standard deviations
  within 5% -- all methods except mean imputation, which had a 30% discrepancy in its standard deviation
  - MORAL:  Mean estimation sucks very bad, even on MCAR data (at least as far as estimating variance is concerned)
* However, remember from Graham's paper that almost no missing data is truly MCAR, and it's better to think of
  missing data as somewhere between pure MAR and pure MNAR, so...
* How do these methods perform on MAR data:
  - mean imputation is again the worst (basically never use it)
  - all methods except listwise deletion and the chosen regression-based imputation are fairly stable up to ~5% MAR data
    * in both MCAR and MAR plots, the regression and listwise (CCA) methods very consistently overestimate the mean (this
      is that introduction of bias that you hear about!)
    * i.e., never use mean imputation, listwise deletion, or regression-based imputation, especially if you
      care about estimates of population parameters
  - hot deck imputation makes it to about ~10% MAR data before it falls apart (but if you look at the plot, it's
    really not doing much better than the group mean method, single imputation EM)
  - both the frequentist (EM) MI and Bayesian (MCMC) algorithms are fine up until ~25% MAR data
    * looking at the plots, though EM MI is doing much better at estimating the mean than the other 6 methods, 
      it's doing fairly worse than MCMC MI; both EM MI and MCMC MI estimate the variance very well though!
  - only the MCMC MI algorithm safely makes it to 50% MAR data
    * not just "makes it", but resoundlingly so!
    * every other method just shits the bed, and there is MCMC MI hanging in there at an awesome estimate of the
      population mean!
* How do these methods perform on MNAR data?
  - tells a similar story to taht of MAR data
  - basically, EM MI is an "ok" second best to MCMC MI, but only up to ~10% missing data if you care about
    variance; EM MI holds out for a slightly biased estimate of the mean until ~20-25% MNAR data (not great,
    but much better than the non-MI methods)
  - anything more than 25% MNAR data, then you MUST use MCMC MI for unbiased estimates of the mean and variance
    * if you care about population estimates of the variance, then the plots would suggest to just care
      about MCMC MI; everything else is some degree of terrible
    * MCMC MI estimates not just unbiased, but nearly perfect: holy hell this method is good!
    
 the author acknolwedges that the particular regresion-based imputation (SPSS MVA REG) was especially bad -- probably
 worse than other implementations....
 
 "What this really shows is that group means perform the worst of anything when it comes to imputing
data. Case Deletion is bad; imputing the mean far worse, for it destroys the variance structure within the
data."

"Deleting the case is also very wrong, in all but MCAR; this confirms what
is in the literature (Little, 1988; Little, Rubin, 1987). Single Imputation methods can work for MAR, but
only when less than 10% of the data is missing, if one is interested in the mean. If the variance structures
in the data are important, then don’t use these methods if the data is more than 5% missing."  Basically,
use MI -- and, if possible, MCMC MI.

Ending Note:  Again, this paper is exclusively focused on statistical inference (population estimates of
the mean and variance) rather than on prediction.

-------------------------------------------------------------------------


# 2012: Little et al: The New England Journal of Medicine: [The Prevention and Treatment of Missing Data in Clinical Trials](https://www.nejm.org/doi/full/10.1056/NEJMsr1203730)

It's crazy: I've read papers from the 90s saying things like, "Techniques for dealing with missing
data have been around since the 70s, yet no one know about them  -- and those that do, are reluctant
to use them because (i) they don't fully understand or trust them, or (ii) they do not use software
packages that provide the right functionality easily or intuitively."  This paper is from 2012, but
it basically sings the same song:  "Missing data have seriously compromised inferences from clinical
trials, yet the topic has received little attention in the clinical-trial community."

Btw, not only is this yet another statistical inference focused paper, it is centered on missing
outcome data: "Missing data are defined as values that are not available and that would be meaningful for analysis if they were observed. For example, measures of quality of life are usually not meaningful for patients who have died and hence would not be considered as missing data under this definition. We focus on missing outcome data here, though analysis methods have also been developed to handle missing covariates and auxiliary data."

When training a model using supervised learning, we care so much about outcome data -- that's the thing
we hope to predict.  It is interesting to consider using feature vectors that have no associated outcome... This
is more aligned with something you were creating a model to fill in / predict... That said, what if you
have a fairly compromised data set -- lots of missing values, including in the outcome/target variable?  Would it
be ok to use something like multiple imputation before training?  I don't know... I'd like to do some simulations
on this to get an idea... In the meanwhile, I'm going to read through this paper with that in mind!

Read another few paragraphs... Seems this paper is really about limiting missing data by improving
study designs and doing follow-up after treatment discontinuation, etc.  This is likely because the paper
is focusing on missing outcomes (i.e., missing endpoints).  

They do mention good points:  "An important and relatively neglected design issue is how to account for the loss of power from missing data in statistical inferences such as hypothesis tests or confidence intervals. The most common approach simply inflates the required sample size in the absence of missing data to achieve the same sample size under the anticipated dropout rate, estimated from similar trials. This approach is generally flawed, since inflating the sample size accounts for a reduction in precision of the study from missing data but does not account for bias that results when the missing data differ in substantive ways from the observed data. In the extreme case in which the amount of bias from missing data is similar to or greater than the anticipated size of the treatment effect, detection of the true treatment effect is unlikely, regardless of the sample size, and the study is noninformative. When performing power calculations, one should consider sample-size computations for an intention-to-treat analysis that uses a hypothesized population treatment effect that is attenuated because of the inability of some study participants to adhere to the treatment. Alternatively, one could develop power analyses for statistical procedures that explicitly account for missing data and its associated uncertainty, as discussed below."

### Missing Value Taxonomy
* MCAR: 
  - "In the first scenario, data are missing completely at random, which implies that the missing data are unrelated 
    to the study variables. In particular, the complete cases are representative of all the original cases as randomized."  
  - In terms of patient dropout: "The assumption that data are missing completely at random presumes that outcomes for 
    those who dropped out would be expected to be similar to outcomes for participants who did not drop out, so the 
    data from dropouts can be ignored without bias."
  - Unrealistic: "We do not recommend using the complete-case-analysis approach to missing data, since it requires 
    the unrealistic assumption that the data are missing completely at random. "
* MAR: 
  - "In the second scenario, data are missing at random, which implies that recorded characteristics can account 
    for differences in the distribution of missing variables for observed and missing cases."
  - In terms of patient dropout: "The less stringent assumption that data are missing at random implies that outcomes 
    for participants who dropped out would be expected to be similar to outcomes for participants who did not drop out 
    with similar baseline characteristics and similar intermediate measures up to that time, so missing outcomes can 
    be modeled on the basis of outcomes of similar participants who did not drop out."
* MNAR: 
  - "In the third scenario, data are missing not at random, which implies that recorded characteristics do not account 
    for differences in the distribution of the missing variables for observed and missing cases."
  - In terms of patient dropout: "Only the assumption that the data are missing not at random allows for the possibility
    that events that were not observed (e.g., severe toxicity or disease progression occurring since the last visit) may
    have influenced the decision to drop out, and thus outcomes are likely to be different from those of similar 
    participants who did not drop out."


### Imputation Methods
* CCA: Not recommended!
  - "We do not recommend using the complete-case-analysis approach to missing data, since it requires the unrealistic 
    assumption that the data are missing completely at random."
* Single imputation methods: Not recommended!
  - "Simple imputation methods such as the last observation carried forward and baseline observation carried forward are 
    commonly applied, in part because they are simple and easy to understand, but they are overused. We do not recommend 
    them, since their validity hinges on assumptions that are often unrealistic. For example, the last-observation-carried-
    forward method makes the assumption that the outcomes of participants do not change after they have dropped out, leading 
    to biased treatment effects when this assumption is not justified. These methods, like many other methods that impute a 
    single value for the missing data, do not propagate imputation uncertainty and thus yield inappropriately low estimates 
    of standard errors and P values. Thus, we recommend that single-imputation methods, such as last observation carried 
    forward and baseline observation carried forward, should not be used as the primary approach to the treatment of missing 
    data “unless the assumptions that underlie such methods are scientifically justified.”
* Two methods recommended, but w/ the addition of sensitivity analysis
  - Weighted estimation equations
  - Multiple imputation
  - Reason for Method: "Weighted estimating equations and multiple-imputation models have an advantage in that they can be used to incorporate auxiliary information about the missing data into the final analysis, and they give standard errors and P values that incorporate missing-data uncertainty."
  - Reason for Sensitivity Analysis: "Analyses that are performed with such methods often assume that missing data are missing at random, and such an assumption often makes sense for the primary analysis. However, the observed data can never verify whether this assumption is correct... assess the robustness of inferences about treatment effects to various missing-data assumptions by conducting a sensitivity analysis that relates inferences to one or more parameters that capture departures from the primary missing-data assumption, such as the pattern-mixture analysis" or selection models. 

The paper doesn't really get technical, but does make recommendations (e.g., if you have to impute, do not
use CCA, mean imputation, last-observation-carried-forward, etc).  That said, it did get me thinking more
about missing "Y" data.  When I think about missing data -- MCAR, MAR, and MNAR -- I am almost exclusively
thinking about "X" data -- predictors, inputs, or as the stat guys like to say, "covariates".  


------------------------------------------------------------

# 2006: Donders et al: Journal of Clinical Epidemiology: [A gentle introduction to imputation of missing values](https://www.jclinepi.com/article/S0895-4356%2806%2900197-1/pdf)

This is another inference/causally focused study interested in achieving unbiased estimates of 
mean, variance, and variable associations in the face of missing data.

### Imputation Motivation
* "Imputation techniques are based on the idea that any subject in a study sample can be replaced by a new 
    randomly chosen subject from the same source population.  In single imputation, only one estimate is used. In 
    multiple imputation, various estimates are used, reflecting the uncertainty in the estimation of this 
    distribution."
* "In the classical (frequentistic) statistical view, conclusions drawn from 
    any study should not depend 
    on the sample that is involved in the study. Should the study be repeated with a different sample, nearly 
    identical results should be obtained. The conclusions do not depend on the given set of subjects in the 
    sample. This implies that every subject in a randomly chosen sample can be replaced by a new subject that 
    is randomly chosen from the same source population as the original subject, without compromising the 
    conclusions. Imputation techniques are also based on this basic principle of replacement."


### Missing Data Taxonomy
* MCAR
  - "If subjects who have missing data are a random subset of the complete sample of subjects, missing 
    data are called missing completely at random (MCAR). ... The reason for missingness is completely 
    random, i.e., the probability that an observation is missing is not related to any other patient 
    characteristics."
  - "Typical examples of MCAR are when a tube containing a blood sample of a study subject is broken by 
    accident (such that the blood parameters can not be measured) or when a questionnaire of a study 
    subject is accidentally lost."
* MNAR
  - "If the probability that an observation is missing depends on information that is not observed, like 
    the value of the observation itself, missing data are called missing not at random (MNAR)."
  - Example:  "when asking a subject for his or her income level, it might well be that missing data 
    are more likely to occur when the income level is relatively high."
* MAR
  - The "probability that an observation is missing ... depends on information for that subject that is 
    present, i.e., reason for missingness is based on other observed patient characteristics."
  - "Mostly, missing data are neither MCAR nor MNAR."
    * As other papers have noted, missing data is more of a mix of all these definitions, e.g., there can
      be a few MCAR data points in a largely MAR populations with a tinge of MNAR.
    * This is why other authors have noted that it's important to test assumptions, e.g., perform sensitivity
      analyses.
   - As most other authors I've read, these folks consider MAR to be a horrible misnomer since the
     data is not "missing at random" as much as it's "conditionally missing at random," i.e., there is
     an order to the missingness that one can find and use to isolate the randomness.
   - Example: "suppose we want to evaluate the predictive value of a particular diagnostic test, and the test 
     results are known for all diseased subjects but unknown for a random sample of nondiseased subjects. In 
     this case the missing data would be MAR: conditional on a patient characteristic that is observed (here 
     the presence or absence of the disease) missing data are random." 
      * Note that missingness may depends on the outcome variable, other predictor variables, or any
        combo thereof.
      * The authors chose outcome-based MAR because it is typical in epidemiological studies.



### Imputation Methods
> "Under the general conditions of so-called missing at random (MAR) and missing completely at random 
> (MCAR), both single and multiple imputations result in unbiased estimates of study associations. But
> single imputation results in too small estimated standard errors, whereas multiple imputation results 
> in correctly estimated standard errors and confidence intervals. "

* Complete Case Analysis
  - "Complete and available case analyses provide inefficient though valid results when missing data are 
    MCAR, but biased results when missing data are MAR, which is the more common form of missingness in 
    epidemiological research."
  - Even if you can assume that the missing data in your data set is MCAR (unlikely), which would mean
    that the CCA is unbiased, "estimating associations using \[CCA\] remains less efficient (i.e., 
    imprecise), because part of the data is not used."
* Missing Indicator Method
  - Terrible for obtaining unbiased population estimates
  * Mean Imputation
  - "Like the indicator method, the overall mean imputation of
missing values should also be discarded because it will lead
to biased associations even when missing data are MCAR."
* Multiple Imputation
  - Great!
  - The "multiple imputation approach leads to unbiased results with correct standard errors, in 
    situations where missing data are MCAR or MAR."
    
---------------------------------------------------------------


# 2010: Knol et al: Journal of Clinical Epidemiology: [Unpredictable bias when using the missing indicator method or complete case analysis for missing confounder values: an empirical example](https://www.sciencedirect.com/science/article/pii/S0895435610000181)

I did not have access to this paper, but it's title has value all its own:  though the missing indicator method
seems to be popular and useful for predictive modeling (e.g., in Kaggle competitions), it is considered highly
improper for those interested in statistical inference and causal modeling.  

**Results**: "When missing values were completely random, MIM gave an overestimation of the odds ratio, whereas CCA and MI gave unbiased results. MIM and CCA gave under- or overestimations when missing values depended on observed values. Magnitude and direction of bias depended on how the missing values were related to exposure and outcome. Bias increased with increasing percentage of missing values."

**Conclusion**: "MIM should not be used in handling missing confounder data because it gives unpredictable bias of the odds ratio even with small percentages of missing values. CCA can be used when missing values are completely random, but it gives loss of statistical power."


------------------------------------------------------------------------


# 2006: van der Heijden et al: Journal of Clinical Epidemiology: [Imputation of missing values is superior to complete case analysis and the missing-indicator method in multivariable diagnostic research: A clinical example](https://www.jclinepi.com/article/S0895-4356(06)00198-3/pdf)

Same deal as last one: did not have access to, but the title and "front matter" say enough.

The difference is that this one is about diagnostic research and identifying "potential predictors 
(test results) that independently contribute to the prediction of disease presence or absence."  In other
words, these guys are leaning into predictive modeling a bit.

However, from the "Results" and "Conclusions" write up, it is clear that they still strongly regard and
desire interpretability, which places it more closely to the inference / causal modeling concerns.  From what
I can tell, their issue with CCA and the missing indicator method (MIM) in this case was that the resulting list
of top predictors for these methods differed from the predictors lists generated by the single and multiple
imputation (MI) methods they tested out.  However, all methods resulted in predictive models with AUC > 0.75, which
is pretty good (unfortunately, the front matter doesn't specify which methods resulted in which AUCs).  

**Results**: "The receiver operating characteristic curve area for all diagnostic models was above 0.75. The predictors in the final models based on the complete case analysis, and after using the missing-indicator method, were very different compared to the other models. The models based on MI did not differ much from the models derived after using single conditional and unconditional mean imputation."

**Conclusion**: "In multivariable diagnostic research CCA and the use of the MIM should be avoided, even when data are MCAR. MI methods are known to be superior to single imputation methods. For our example study, the single imputation methods performed equally well, but this was most likely because of the low overall number of missing values."

I agree with not using CCA in a predictive model, but I'm not sure their conclusion about MIM is strictly true.  Seems
to me there should be a qualifier: "If developing a predictive model where you still care about association
estimates and interpretability, then the use of MIM should be avoided. However, if you are exclusively interested in
best predicting outcomes, then use what works -- Kaggle winners often talk about MIM."

NOTE: Much of the same authors for this paper contributed to another paper 6 years later in 2012, where they go over when 
the MIM is and isn't useful.  I expect this to be a more nuanced look at this, and cover it next.

----------------------------------------------------------------------

# 2012: Groenwold et al: Canadian Medical Association Journal: [Missing covariate data in clinical research: when and when not to use the missing-indicator method for analysis](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3414599/)

In one sentence: the missing indicator method (MIM) obey the intention-to-treat principle and gives unbiased
estimates of baseline covariates in randomized trials, independent of the missingness mechanism; however, this
is not true for nonrandomized studies.

This article really deals with biased/unbiased population estimates and interpretability (i.e., inference
and/or causal modeling as oppposed to predictive modeling).

* CCA
  - "In addition to reducing statistical power, this approach will often result in biased estimates of the 
    associations between covariates and outcomes."
  - Gives unbiased estimates when data are MCAR; however, MCAR data is considered unlikely for most missing 
    data encountered in practice: "it is unlikely that data are MCAR, but rather 
    missingness of data depends (partly) on observed patient characteristics."
    
* Single/Multiple Imputations
  - Imputation "methods can be applied equally for missing outcomes, missing exposures and missing covariates."
  - Single imputation (multivariable regression): "missing value \[is replaced\] with the most likely value, based 
    on all observed patient characteristics, including the outcome."
  - Some thoughts:
    * Replacing all missing values with the "most likely values" means that values are always obtained
      from the regression curve itself, which is very uncharacteristic of the actual data -- and this results
      in distorted estimates (e.g., lower variance and standard error).  
    * Multiple imputation takes into account that randomly sampling from the population is inherently different
      than randomly sampling from the regression curve, and fixes this issue.
    * This goes to show that a "great imputation" is different than a "great prediction" -- both are "missing
      data" problems in a way, but they serve different purposes and operate in different contexts.  For example,
      in inference and causal modeling, you may have a lot of missing target/outcome data, but the goal is not 
      to "predict" that outcome data, it is to impute it properly in order to obtain ubiased, non-distorted estimates
      of population parameters and associations... "Imputation" is more holistic, while "prediction" is
      more "individualistic"...

* MIM (Missing Indicator Method)
  - For "missing covariate data in nonrandomized studies ... the missing-indicator method ... very likely produce\[s\] 
    biased results. The direction and size of the bias depended on the reason or mechanism of missingness."
  - For "missing baseline covariate data in randomized trials ... the missing-indicator method produce\[s\]
    unbiased estimates of the treatment effect."
  - "The missing-indicator method was proposed for missing confounder data in etiologic research," which the authors
    argue it does not work for...
  - Interestingly, they make a distinction between imputation and MIM: "The missing-indicator method does not impute 
    missing values. Instead, missing observations are set to a fixed value (usually zero, but other numbers will give 
    the same results), and an extra indicator or dummy (1/0) variable is added to the analytical (multivariable) model 
    to indicate whether the value for that variable is missing. Consequently, each participant can still be included in 
    the analysis to maintain statistical power."
      * I previously considered any method that dealt with missing data as "imputation", which isn't
        unreasonable: "mean imputation" is when you replace missing values with the mean, which is
        really just "lazy, brute force replacement" compared to regression-based single imputation (which is "lazy,
        but well-intentioned imputation" compared to multiple imputation)...  Here, you are "imputing", but in
        a vectorial way I guess.  Those are things are "scalar imputation", while MIM imputes by expanding
        the dimensionality of offending variable.
  - **When MIM is bad**: 
    * "When using \[MIM\] to adjust for an incomplete covariate, the estimated association 
      between the independent variable under study (e.g., treatment, risk factor or predictor) and outcome is 
      a weighted average of two associations representing
        - (a) the association between the independent variable and outcome, adjusted for all covariates, among 
          the participants for whom all data were observed; and 
        - (b) the association between the independent variable and outcome, adjusted only for complete covariates, 
          among the participants for whom the covariate was not observed."
    * "For nonrandomized studies, the second association will typically be biased because it is only 
      partially adjusted for confounding."
    * "Furthermore, the first association is based on a complete case 
      analysis, so this association is unbiased only if missingness is conditionally independent of the 
      outcome (MAR). But, given the nature of nonrandomized studies, in which covariates are commonly 
      mutually related, \[MIM\] will almost always give biased results."
  - **When MIM is good**: 
    * "In randomized trials, however, randomization implies that baseline covariates are 
      balanced across treatment groups and therefore not related to the treatment under study. Hence, unadjusted 
      treatment effects from randomized trials are unbiased. Because of randomization, the distribution of missing 
      values is likely to be balanced across treatment groups as well. Consequently, both the association between 
      treatment and outcome among the participants for whom all data were observed, and the association between 
      treatment and outcome among the participants for whom not all data were observed, will be unbiased. Hence, 
      both complete case analysis and the missing-indicator method will give unbiased estimates."
    * "Missingness of baseline covariates in a randomized trial is not necessarily the same as \[MCAR\]. In 
      a randomized trial on the effects of a certain treatment for depression, participants who are 
      severely depressed could be more likely to have missing baseline covariates. If the baseline covariate indicates 
      severity of the depression, however, missingness will likely also depend on the value of the baseline variable 
      itself \[MNAR\]. But, even if baseline covariate data are \[MNAR\], randomization implies that missingness 
      is still not related to treatment, so the observed treatment effect will still be unbiased with application 
      of \[MIM\]."

So, basically, MIM isn't bad for estimating causal parameters (treatment effects) in RCTs.  (Right? Yea. Right?
Yea, I think. Yea? I don't know! :-p)

------------------------------------------------------------------------------------------

# 2017: Pedersen et al: Clinical Epidemiology: [Missing data and multiple imputation in clinical epidemiological research](http://discovery.ucl.ac.uk/1549966/1/Petersen_clinical-epidemiolog_031517.pdf)

Winner: 1st paper I read in this list to mention how DAGs may help assess whether missing data are MCAR, MAR, or 
MNAR.  However, they don't really dig into this aspect much.  (Gives me a new Google Scholar search idea...but
for another day!)

Note: this paper seem very good so far...i.e., one of the ones I'd read again if I had to (there was that
psychology one above that was really good too). 

Despite this paper being a decent read, I'm definitely reaching limiting returns... It's just more of the same at
this point: methods like CCA, mean imputation, and MIM are generally not good, especially when the data is
not MCAR, and they produce biased estimates in one parameter or another (mean, variance, standard error, correlation,
odds ratio, what-have-you).  In other words, not the greatest idea to employ these methods in the causal inference
setting, lest thee have great justification.  However, MI -- well, that shit's great!  It works for MAR and MCAR data, and
can even be adapted for MNAR.  W00t!

"Individuals with missing data may differ from those with complete data in terms of
the outcome of interest and prognosis in general. For example, those who are healthier
may be less likely to visit their doctor and hence less likely to have blood pressure
recorded. Studies on self-reported data show that individuals who have missing data on
one variable are often likely also to have missing data on other variables. "

* MCAR
  - "When individuals with missing data are a random subset of the study population, the probability of 
    being missing is the same for all cases ... under MCAR, missing data do not depend on either 
    observed data or unobserved data."
  - Example 1: "when a glass slide with biopsy material from a patient is accidentally broken such that 
    pathology and histology tests cannot be performed"
  - Example 2: "when individuals had no blood pressure measured as the equipment was broken"
* MAR
  - "when the missingness depends on information we have already observed"
  - Example 1: "data in a depression survey can be said to be MAR if, given gender, men are less likely 
    than women to fill out the survey"
  - Example 2: "in a study of weight, data on weight are less likely to be recorded for younger individuals, 
  because they do not attend health care facilities as often as older individuals"
* MNAR
  - "When the probability that data are missing depends on the unobserved data, such as the value of the 
  observation itself"
  - Example 1: "overweight or underweight individuals may be more likely to have their weight measured than 
  individuals with normal weight, even after age is accounted for"
  - "when individuals with severe depression, or adverse effects from antidepressant medication, are more or 
    less likely to complete a survey on depression"
  - Example 3: "when data on income are missing, and the probability of missingness is related to the level 
    of income, eg, those with very low or high income refuse to report their income."
    
 **Word of Warning**: "Observed data can give us some indication of whether missing data are MCAR, but we are not
 able, from these data alone or simple test, to evaluate whether missing data are MAR or MNAR.
 
 
 * CCA
  - "the results of such analyses may yield biased estimates of associations, because complete cases are 
    assumed to be a random sample of the whole population, ie, data are MCAR."
  - " Another issue with complete-case analysis is that a large proportion of valuable research data are 
    discarded, which affects the statistical power and precision of the estimates."
  - CCA may be reasonable "when working with large datasets with few missing observations, because the risk of 
    bias is minimal and the precision is still good."
* MIM
  - "The method is popular because it retains the full dataset where no observations are excluded."
  - "Even under the MCAR assumption and with very few missing observations, this method is still subject to bias."
  - "If the method is used for missing data on potential confounder variables, the estimates will be biased due
    to residual confounding."
* Single imputation methods (mean imputation, last-observation-carried-forward, regression-based)
  - "In general, single imputation methods do not account
for the uncertainty of missing data, and as a result, standard
errors of the estimates are likely to be too small (thereby
overestimating the precision of the results). This can potentially lead to Type 1 error (ie, identifying an association when
none exists)."
  - "Mean imputation also does not preserve the relationships between variables; it only preserves the mean
    of the observed data \[but not SE of the mean\]... Since most of the research studies are interested 
    in the relationship between variables and not just the mean, mean imputation should be avoided in general."
* Worst/Best Case Sensitivity Analyses
  - "This method involves the replacement of missing values with the worst or best value in the 
    observed data," then comparing the results of the two associate regression analyses.
  - "When both analyses produce similar estimates of an association, it is rather straightforward to draw 
    conclusions about the effect of missing data. However, analyses yielding opposing results can be difficult 
    to interpret."
  - Has been shown to yield biased estimates...
* Multiple imputation
  - It's good for inference, so use it.
  - Cons: if you're stupid, you might do something stupid.  Don't be stupid.



| Methods | Brief description | Assumption to achieve unbiased estimates | Advantages | Limitation(s) |
|---------|-------------------|------------------------------------------|------------|---------------|
| Complete-case analysis | Include only individuals with complete information on all variables in the dataset | MCAR | (i) Simplicity; (ii) Comparability across analyses | (i) Data may not be representative. (ii) Reduction of sample size and thereby of statistical power. (iii) Too large standard error (lack of precision of the results).  (iv) Discarding valuable data. |
| Missing indicator method | For categorical variables, missing values are grouped into a “missing” category. For continuous variables, missing values are set to a fixed value (usually zero), and an extra indicator or dummy (1/0) variable is added to the main analytic model to indicate whether the value for that variable is missing. | None | (i) Uses all available information about missing observation and retains the full dataset. | (i) The magnitude and direction of bias difficult to predict.  (ii) Too small standard error.  (iii) The results may be meaningless since method is not theoretically driven.  (iv) Bias due to residual confounding. |
| Single value imputation | Replace missing values by a single value (e.g., mean score of the observed values or the most recently observed value for a given variable if data are measured longitudinally) | MCAR, only when estimating mean | (i) Run analyses as if data are complete.  (ii) Retains full dataset. | (i) Too small standard error (overestimation of precision of theresults).  (ii) Potentially biased results.  (iii) Weakens covariance and correlation estimates in the data (ignores relationship between variables). |
| Sensitivity analyses with worst- and best-case scenarios | Missing data values are replaced with the highest or lowest value observed in the dataset. | MCAR | (i) Simplicity.  (ii) Retains full dataset.  |  (i) Too small standard error and thereby overestimation of precision of the results.  (ii) Analyses yielding opposite results may be difficult to interpret. |
| Multiple imputation | Missing data values are imputed based on the distribution of other variables in the dataset | MAR (but can handle both MCAR and MNAR) | (i) Variability more accurate for each missing value since it considers variability due to sampling and due to imputation (standard error close to that of having full dataset with true values). | (i) Room for error when specifying models. | 



---------------------------------------------------------------------------------

# 2001: Bennett: Australian and New Zealand Journal of Public Health: [How can I deal with missing data in my study?](https://onlinelibrary.wiley.com/doi/pdf/10.1111/j.1467-842X.2001.tb00294.x)

This paper is much more thorough than most of the papers I've read so far.  It certainly covers many
more methods that I've not seen (or have seen only mentioned in passing): hot deck imputation, cold deck imputation,
available case analysis (which I think is the same as pairwase deletion, but not sure), Markov-chain imputation,
raw maximum likelihood methods, and pattern mixture models.

As usual, the focus of this paper is on bias and inference.

### Methods

* CCA
  - Assumes data are MCAR, but "in practice the data are not MCAR and participants with complete data 
    may be a biased sub-sample of all participants. As a consequence, a complete case analysis would 
    usually produce biased results. In addition, there is a loss of power due to analysing a smaller 
    dataset."
* Available Case Analysis
  - Description: "This method uses the largest set of available cases for estimating the parameter of interest 
    (e.g. treatment effect)."
  - Verdict:  "This approach is reasonable if we have cross-sectional data. However, if the data are longitudinal, 
    with many time points, different participants will contribute to the data at different time points depending 
    on the pattern of ‘missingness’. If the data are not MCAR then there is a lack of comparability over time points, 
    which would lead to highly biased results."
* Last Value Carried Forward (LVCF)
  - Description: "  This method is used when we have longitudinal data and data are found to be missing at 
    a particular ‘visit’ or point in time for some participants. The researcher would then carry the last
    available value forward (from the last visit or time point) and impute this value for the missing values."
  - Verdict: "The main problem with this method is that the researcher is assuming that there will be no 
    change from one visit to the next. If the data are MCAR then this is reasonable. However, if this 
    is not the case then the results can be seriously biased."
* Mean Imputation
  - Verdict: "This approach assumes the data is MCAR but is not recommended as it can lead to under-estimates 
    of the variance."
* Regression Methods
  - Description: "This approach involves developing a regression equation based on the complete subject data 
    for a given variable, treating it as an ‘outcome’ and using all other relevant variables as predictors. For 
    participants where the ‘outcome’ is missing, the predicted values from the regression equation are used as 
    replacements."
  - Verdict: "This method has similar problems to the mean substitution method but these can be overcome 
    by adding uncertainty, usually by weighting, to the imputation of ‘outcome’ so that the mean value is 
    not always imputed. This method assumes that the data are MAR. However, the weights and standard errors 
    can become extremely complex if the data and incomplete data patterns are not simple."
* Hot Deck Imputation
  - Description: "This method involves replacing missing values with values taken from respondents with 
    matching covariates. The process identifies participants that are similar in terms of data observed."
  - Verdict: "Hotdeck is useful as it is relatively simple and maintains the proper measurement levels of 
    the recorded covariates. It is usually less biased than mean imputation or complete case approaches 
    and assumes that the missing data are MAR. A disadvantage is that complex matching algorithms sometimes 
    need to be employed in order to match respondents."
* Cold Deck Imputation
  - Description: "This method is very similar to hot-deck imputation in its approach except that the strategy 
    for assessing subject similarity is based on external information or prior knowledge rather than the 
    information available in the current dataset."
  - Verdict: A disadvantage is that "this method is dependent on the quality of the available external information."
* Single Imputation Methods in General
  - Includes LVCF, Mean Substitution, Regression Methods, Hot Decking, Cold Decking
  - Verdict: "The disadvantages of all the single imputation methods described above are that they impute the 
    same missing value every time. Hence, a statistical analysis, which treats imputed values just the same 
    as observed values, will systematically underestimate the variance, even assuming that the precise reasons 
    for non-response are known. Single imputation cannot represent any additional variation that arises when the 
    reasons for non-response are not known."
* Other techniques covered (that I'll maybe come back and fill in):
  - Multiple Imputation
  - Markov-Chain Imputation
  - Expectation-Maximization (EM) Approach
  - Raw Maximum Likelihood Methods 
  - Missing Indicator Method
  - Pattern-Mixture Models
  

| Method | Bias | Handling of variability | Availability |
|---------|-------|-----------------------|--------------|
|Complete case (CC) | Unbiased if MCAR otherwise highly biased | Under-estimates variability in the data set | SAS, SPSS, BMDP |
| Available case (AC) | Unbiased if MCAR otherwise highly biased | Under-estimates variability in the data set | SAS,SPSS, BMDP |
| Mean imputation (MnImp) | Unbiased if MCAR otherwise highly biased | Under-estimation of the variance but less than CC and AC | SAS,SPSS, BMDP |
| Last value carried forward (LVCF) | Unbiased if MCAR otherwise highly biased | Under-estimation of the variance but less than MnImp | User-defined |
| Regression methods (RM) | Unbiased if MCAR or MAR otherwise highly biased | Under-estimation of variance but can be overcome by using weights | SOLAS or user defined |
| Hot-deck imputation | Unbiased if MCAR or MAR otherwise highly biased | Under-estimation of variance but less than MnImp, LVCF, and RM | SOLAS or user defined |
| Cold-deck imputation | Unbiased if MCAR or MAR otherwise highly biased | Under-estimation of variance but depends on quality of external information | User defined
| Multiple imputation (MI) | Unbiased if MCAR or MAR otherwise highly biased | Produces good estimates of the variability in the dataset| SOLAS, SAS macros available |
| Markov-Chain imputation | Unbiased under MCAR, MAR, and MNAR conditions | Produces good estimates of the variability in the data set | BUGS |
| E-M Algorithm | Unbiased if MCAR or MAR otherwise highly biased | Produces good estimates of the variability in the dataset | SAS, SPSS, BMDP |
| Raw Maximum Likelihood | Unbiased if MCAR or MAR otherwise highly biased. Depends on model fitted being  specified correctly. | Produces accurate estimates of the variability in the data set | Some SAS procedures, AMOS, LISREL |
| Indicator method imputation | Unbiased if MCAR or MAR otherwise highly biased. Bias may vary depending on whether the missing data occurs for a covariate or a confounder | Does under-estimate the variability in the dataset. Under-estimation of similar  magnitude to CC approach. | User defined |
| Pattern mixture models | Unbiased under MCAR,MAR and NMAR  | Produces good estimates of the variability in the dataset | User defined |


--------------------------------------------------------

# 2004: Raghunathan: Annual Reviews of Public Health: [What Do We Do With Missing Data? (Some Options for Analysis of Incomplete Data)](https://www.annualreviews.org/doi/pdf/10.1146/annurev.publhealth.25.102802.124410)


This article denounces CCA and ACA, then goes on to explore 3 more general methods of increasing statistical
sophistication:  weighting, multiple imputation, and maximum likelihood methods.  It notes that there have
been papers that have shown CCA to work with MAR data, but stresses that these cases are highly specific, unlikely,
and that it is best to just consider CCA acceptable for MCAR data only -- and even then, to understand that
it is still not the best choice because it undermines the statistical power inherent within the data (i.e., it
throws out good data just because of some associated missing data).  The author notes that the weighting technique
helps correct the biases that are produced for non-MCAR data under CCA, but does not resolve the issue of
statistical efficiency (removal of any row with missing data means that one must collect more data in order
for this technique to have the same statistical power as one like multiple imputation that does not throw
out data).  The author notes that the maximum likelihood method is the gold standard, but it is not generally
available in software packages, and that the multiple imputation method is nearly as good and much more
practical.

Great read, but again -- the primary focus is on statistical inference, not on blackbox predictions (the aim
is to eliminate bias while maintaining efficiency, not to produce the most accurate predictions possible).

Given how many papers I read before this, I only skimmed much of it, which is unfortuante because from what I
can tell, it's a much more thorough resource than many of the papers I've read.  Basically, the conclusions is
like most:  use the multiple imputation approach when your focus is on unbiased estimates of population parameters
(inference) and you want to maintain as much statistical efficiency as possible.  However, it does seem that
the author would recommend a maximum likelihood approach such as the EM algorithm if the user is savvy enough
to produce the software for it, etc.


" In a cross-sectional study relying on a survey, subjects may
refuse to participate entirely or may not answer all the questions in the questionnaire. The former type of missing data is called unit nonresponse, and the latter,
item nonresponse. In a longitudinal study, subjects may drop out, be unable, or
refuse to participate in subsequent waves of data collection. The missing data in
this context may be viewed as unit or item nonresponse. For instance, in a crosssectional analysis of data from a particular wave, drop-outs may be viewed as unit
nonrespondents, whereas in a longitudinal analysis involving data from all waves,
missing data due to drop-outs may be viewed as item nonresponse."

"Though most methods rely on a MAR assumption, its lack of applicability is related to the lack of
variables that can be used to predict the missing values. Because the missing data
are inevitable, a prudent step, from the design perspective, is to investigate potential
predictors of variables with missing data and include them in the data-collection
process. Such auxiliary variables can include administrative data, neighborhoodlevel observations, and interviewer observations. These additional variables can be
used in the multiple imputation process. It is important that missing data be considered not solely a data analysis problem, but also a design and analysis problem."


Multiple Imputation:  "the missing set of values is replaced by more than one plausible set of
values. Each plausible set of values in conjunction with the observed data results
in a completed data set. Each completed data set is analyzed separately using the
standard complete data software, and the resulting point estimates and standard
errors are combined using a simple formula described later."  Note that "the filled-in
values for any one subject with missing values should not be considered as microdata for that subject but rather as values that are statistically plausible given other
information on that subject. These filled-in or completed data sets are plausible
samples from the population under certain assumptions. Thus, the completed data
sets should result in inferences (point estimates and confidence intervals, for example) that are within the realm of statistical plausibility of inferences that would
have been obtained had there been no missing data ... The
inferential validity based on the multiple imputed data sets is the goal, and any
imputation procedure should not viewed as a method for recovering the missing
values for any given individual."

---------------------------------------------------------------------------
---------------------------------------------------------------------------

I've run out of time, so here are some papers I wanted to more fully read but didn't get a chance to 
do much more than skim:
* 2009: Sterne: [Multiple imputation for missing data in epidemiological and clinical research: potential and pitfalls
](https://www.bmj.com/content/338/bmj.b2393)
* 2007: Horton & Kleinman: [Much ado about nothing: A comparison of missing data methods and software to fit incomplete data regression models](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC1839993/)
* 2003: Allison: [Missing Data Techniques for Structural Equation Modeling](https://www.researchgate.net/profile/Paul_Allison/publication/8959854_Missing_Data_Techniques_for_Structural_Equation_Modeling/links/02bfe50e5ddd518228000000/Missing-Data-Techniques-for-Structural-Equation-Modeling.pdf)

To come back to...
* https://bookdown.org/max/FES/imputation-methods.html
  - This actually references the different needs between inferential and predictive models
  - In prediction, you do not necessarily care about population estimates at all, but in imputing
    the most likely value and gaining the best predictions.
  - That said, kNN can be computationally expensive, and the linear models still give me pause... Yes,
    multiple imputation cannot be easily "saved and run" at runtime, but wouldn't it be better to 
    build the linear imputers of a MI'd data set...?
* https://medium.com/ibm-data-science-experience/missing-data-conundrum-exploration-and-imputation-techniques-9f40abe0fd87
  - I want to come back to this
  - It is one of the only source I've read that covers visually investigating missing data patterns (via a nullity
    matrix, a bar chart, and a heat map of correlations)
  - It uses a Python package called `missingno`, which I can use for the data sets I'm currently working on
Some online e-Books I found along the way that are worth getting back to:
* Stef van Buuren: [Flexible Imputation of Missing Data](https://stefvanbuuren.name/fimd/)
* Kuhn and Johnson: [Feature Engineering and Selection: A Practical Approach for Predictive Models](https://bookdown.org/max/FES/)
* 2009: Garcia-Laencina et al: [Pattern Classification with Missing Data: a Review](https://sci2s.ugr.es/keel/pdf/specific/articulo/pattern-classification-with-missin-data-a-review-2009.pdf)
  - definitely need to start a new post/investigation with this article and others like it, which
    focus on prediction/classification
  - this paper covers the general methods used with inference and estimate bias in mind, while keeping
    an eye towards how those (and other methods) are repurposed for when classification accuracy
    is most important

