


# 2009: Graham: Annual Reviews of Psychology: [Missing Data Analysis: Making It Work in the Real World](https://www.personal.psu.edu/jxb14/M554/articles/Graham2009.pdf)

"My aim here is to encourage researchers to use the missing data
procedures that are already known to be good ones. ... much
of the reluctance to adopt these procedures is related to the myths and misconceptions that
continue to abound about the impact of missing data with and without using these procedures."

"Sometimes the solutions are a bit ad hoc...but the solutions I present are known
to have minimal harmful impact on **statistical inference**..."


## Missingness
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


### Excursion:  Missingness in Psychological Data
A part of the reason I like reading papers from all sorts of fields is the bridge building aspect to 
it, as this author does here: "Statisticians talk about missingness mechanisms. But what they mean by 
that term differs from what social and behavioral scientists think
of as mechanisms. When I (trained as an experimental social psychologist) use that word, I
think of causal mechanisms. What is the reason the data are missing? Statisticians, on the other
hand, often are thinking more along the lines of a description of the missingness."


## Old Methods of Dealing with Missingness
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

## Modern Methods of Dealing with Missingness
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


## Wrap Up
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

Missingness Definitions
* MCAR: "The term 'Missing Completely at Random' refers to data where the missingness mechanism does not
depend on the variable of interest, or any other variable, which is observed in the dataset. MCAR is both
missing at random, and observed at random (This means the data was collected randomly, and does not
depend on any other variable in the data set). This very stringent condition is required in order for case
deletion to be valid, and missing data is very rarely MCAR."
* MAR: "The term 'Missing at Random' is a misnomer, as the missing data is anything but missing at random. The
intuitive meaning of this term is better suited to the term MCAR. What MAR means is missing, but
conditional on some other 'X-variable' observed in the data set, although not on the 'Y-variable' of interest."
* MNAR: "occurs when the Missingness
mechanism depends on the actual value of the missing data. This is the most difficult condition to model
for."
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


The paper doesn't really get technical, but does make recommendations (e.g., if you have to impute, do not
use CCA, mean imputation, last-observation-carried-forward, etc).  That said, it did get me thinking more
about missing "Y" data.  When I think about missing data -- MCAR, MAR, and MNAR -- I am almost exclusively
thinking about "X" data -- predictors, inputs, or as the stat guys like to say, "covariates".  







