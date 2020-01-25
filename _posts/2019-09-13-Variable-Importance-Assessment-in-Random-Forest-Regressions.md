---
title: Variable Importance Assessment in Random Forest Regressions
layout: post
tags: machine-learning random-forest easi
---

Today, I'm reading through 2009's 
[Variable Importance Assessment in Regression: Linear Regression versus Random Forest](https://www.tandfonline.com/doi/abs/10.1198/tast.2009.08199)
(at the time of this writing, academia.edu had a [pdf of this paper](https://s3.amazonaws.com/academia.edu.documents/30498691/tast_2e2009_2e08199.pdf?response-content-disposition=inline%3B%20filename%3DVariable_importance_assessment_in_regres.pdf&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWOWYYGZ2Y53UL3A%2F20200125%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200125T125949Z&X-Amz-Expires=3600&X-Amz-SignedHeaders=host&X-Amz-Signature=31dcd9cbaa3339895ec00ca18e177b502b01347f7f8ba0c61504ef14a8a26ea6))
  
In what follows are quotes and notes.


"Variable importance in regression is an important topic in applied statistics that keeps coming up in 
spite of critics who basically claim that the question should not have been asked in the first place."


"Linear regression is a classical parametric method which requires explicit modeling of nonlinearities and 
interactions, if necessary. It is known to be reasonably robust, if the number of observations n is distinctly 
larger than the number of variables p (n>p). With more variables than observations (p>n or even p>>n), linear regression 
breaks down, unless shrinkage methods are used like ridge regression, the lasso, or the elastic net as a 
combination of both."

"Random forests, on the other hand, are nonparametric and allow nonlinearities and interactions to be learned 
from the data without any need to explicitly model them. Also, they have been reported to work well not only 
for the n>>p setting but also for data mining inthe p>>n setting."
 
"The splitting approach in CART trees has been known for a long time to be unfair in the presence of regressor 
variables of different types, categorical variables with different numbers of categories, or differing numbers 
of missing values  (cf., e.g.,Breiman 1984; 
[Shih and Tsai 2004](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.435.2346&rep=rep1&type=pdf)). To 
avoid this variable selection bias, 
[Hothorn, Hornik, and Zeileis (2006b)](https://www.tandfonline.com/doi/abs/10.1198/106186006X133933) proposed 
to use multiplicity-adjusted conditional tests rather than maximum impurity reduction as the 
splitting criterion."
* Wow, this quote would have been helpful a few months ago :-p
* Also, note that I don't think their (interesting) p-value procedure at each node is implemented
in sklearn (if anywhere (?))
* In most places (sklearn included), a variation on what this paper refers to as RF-CART is implemented (true
RF-CART grows individual trees to node purity, whereas usually an API allows you to make a few choices about
how the trees are grown).  The paper refers to the p-value-split trees as conditional inference (CI) trees, and
random forests built up of CI-trees as RF-CI.  RF-CI is the type of RF proposed in 
[Strobl 2007a](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-8-25) ("Bias in random 
forest variable importance measures: Illustrations, sources and a solution"), which I've read
and commented a little on previously. (See also 
[Strobl 2007b: Unbiased split selection for classification trees based on the Gini 
 Index](https://www.ibe.med.uni-muenchen.de/organisation/mitarbeiter/020_professuren/boulesteix/pdf/gini.pdf), which
says, "variable selection based on standard impurity measures as the Gini Index is biased. The bias is such 
that, e.g., splitting variables with a high amount of missing values —- even if missing completely at random 
(MCAR) —- are artificially preferred.")

"A random forest is random in two ways: (i) each tree is based on a random subset of the observations, and 
(ii) each split within each tree is created based on a random subset of mtry candidate variables. Trees are 
quite unstable, so that this randomness creates differences in individual trees’ predictions. The overall 
prediction of the forest is the average of predictions from the individual trees—because individual trees 
produce multidimensional step functions, their average is again a multidimensional step function that can 
nevertheless predict smooth functions because it aggregates a large number of different trees."

An important difference between CART-like RFs and CI-RFs is that the first builds new n-sample data sets
for each tree by sampling with replacement, while the CI-RFs provide each tree with about 63% of the 
n-sample data set by sampling without replacement.  CI-RFs typically grow smaller trees by default, though
this is a tweakable parameter (that is, in practice, just like we use CART-like RFs, we customize variants on
the original/default CI-RFs). 
* NOTE: in a 2008 follow-up paper 
("[Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307)"), 
the Strobl group decide that their CI-RFs do not solve all the issues
with variable importance, so they also argue that one must
use conditional permuation importance as the measure of variable importance; this is also
possible to apply to CART-like RFs.
* GIST: Know that CI-RFs exist, but do not worry about 'em too much.

"Variable importance is not very well defined as a concept. Even in the well-developed linear model and 
the standard n>>p situation, there is no theoretically defined variable importance metric in the sense 
of a parametric quantity that a variable importance estimator should try to estimate."

"In the absence of a clearly agreed true value, ad hoc proposals for empirical assessment of variable 
importance have been made, and desirability criteria for these have been formulated, for example, 'decomposition' 
of R2 into 'nonnegative contributions attributable to each regressor' has been postulated. Popular approaches 
for empirically assessing a variable’s importance include squared correlations (a completely marginal approach) 
and squared standardized coefficients (an approach conditional on all other variables in the model)."

Given no clear definition of variable importance, and the plethora of choices available to define such a thing,
one must be prudent.  "Depending on the research question at hand, the focus of the variable importance
assessment in regression can be explanatory or predictive importance or a mixture of both."  What definition
best suits your goals?  (Heck, you can use more than one, as long as you clearly know what each measure
means and what are its pros and cons.)

For an intuition concerning what constitutes an explanatory measure of importance versus a predictive 
measure of importance, the authors provide two chains: (i) X2 -> X1 -> Y;  (ii) X2 <- X1 -> Y.  Assuming
all relationships are linear, the authors discuss that two variable importances often used with linear models
(e.g., squares of standardized coefficients) would zero out the importance of X2 for both causal chains since,
conditional on X1, X2 does not contribute any additional information to the prediction.  Given this, do you
consider this type of importance explanatory or predictive?  It is neither, at least not purely.  It makes
sense for predictive purposes, but doesn't tell the whole story (e.g., if one removes X1, then a prediction is
still possible as X2 will take X1's place).  For explanatory purposes, it makes sense for causal chain (ii), since
we see in that chain that X2 is only correlated with Y since both are driven by X1; thus, X2 should not be considered
an important explanatory/causal variable for Y.  However, for causal chain (i), this measure of variable importance
is not explanatory: X2 very clearly drives Y, thus should not be considered to have zero explanatory importance!  In
practice, a linear model does not actually differentiate which of the two causal chains are at play.  So the variable 
importance might be accidentally explanatory in the second causal chain, but not generally explanatory.  It more
generally a measure of predictive power, though as mentioned, just one story of predictive power among many.

This leads us into variable importances associated with random forests.  For example, the conditional permutation
importance advocated by Strobl2008: Is it really "better" than regular permutation importance?  Before reading
this paper, I thought the answer was probably "yes," but now I'm not so sure.  Basically, CPImp acts like
the conditional importance described above, associated with a linear model:  there is an assumption about the
type of causal chain implicit in the method.  We may reduce how many variables we look at and look only
at direct relationships, but we have lost the true causal story of chain (i).  We are telling a partial 
explanatory story...  With regular PImp, the correlated variables would likely be assigned similar
importances.  Here, we wouldn't know anything about a direct relationship, yet we wouldn't be throwing out
important variables in the story.  The authors bring up LASSO and ElasticNet for comparison.  Basically,
LASSO chooses one representative from a group of correlated predictors and zeros the rest of them out.  This
is fine if one's goal is a parsimonious predictive model, but not fine if one wants to understand the explanatory
power of each predictor.  ElasticNet was invented to remedy this: it basically forces groups of correlated
predictors to share coefficient values (e.g., in group G1, predictors with smaller coefficients are made stronger,
while the predictors with larger coefficients are made weaker); now, if one uses an importance measure like
standardized, squared coefficients (SSC), all correlated predictors in the same group should share similar 
importances (similar to  PImp). In this method, no important variables are thrown out, but the measures of 
importance can be considered "biased".


