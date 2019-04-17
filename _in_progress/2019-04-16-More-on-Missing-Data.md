


2009: Graham: Annual Reviews of Psychology: [Missing Data Analysis: Making It Work in the Real World](https://www.personal.psu.edu/jxb14/M554/articles/Graham2009.pdf)

"My aim here is to encourage researchers to use the missing data
procedures that are already known to be good ones. ... much
of the reluctance to adopt these procedures is related to the myths and misconceptions that
continue to abound about the impact of missing data with and without using these procedures."

Multiple imputation and maximum likelihood "are
good procedures that are based on strong statistical traditions. They can certainly be improved
on, but by how much? I would argue that using MI and ML procedures gets us at least 90% of
the way to the hypothetical ideal..."

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
    the variable's variance, thus giving poor estimates of the population variance.  This is why multiple
    imputation was invented: "MI was designed to restore the lost variability found in single imputation, and the MI 
    strategy was designed to yield the correct variability."

## Modern Methods of Dealing with Missingness
EM Algorithm:
* Apparently there are many, so the one described here is EM algorithm that takes in data with 
  missing values and spits out a maximum-likelihood variance-covariance matrix and vector of
  means.


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






