

https://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm


So you've used a decision tree, and a few more -- eventually, you put 'em all
to work at the same time and hand yourself a random forest.

```python
from sklearn.ensemble import RandomForestClassifier
```

```r
library(randomForest)
```

Great.  Here's more info about those things!

https://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm



----------------------

Updated from 2019-Sep-11:

RFs are good for both classification and prob estimation: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3250568/
* “Trees should not be grown to purity. In fact, if nodes are pure, the probability estimate is either 0 or 1 in a terminal node.  As a result, a larger number of trees might be required to obtain consistent probability estimates. If trees are too small, probability estimates might be imprecise. Therefore, as a default value, the terminal node size is generally 10% of the total sample size. Alternatively, the optimal terminal node size may be tuned.”

RFs are low bias, low variance:  https://hal.inria.fr/hal-01590513/document

RFs should be Grid/RandomSearched w/ terminal node size as a search parameter (originally believed that growing trees to terminal purity was always optimal; basically, this requires more trees in the forest to have a consistent estimator):  http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.75.2319&rep=rep1&type=pdf

Are RFs consistent estimators:  http://www.jmlr.org/papers/volume9/biau08a/biau08a.pdf

----------------------------------------------


# Consistency of Random Forests

Gist: many versions of RFs are consistent, some are not.  

Ruling: I'll get back to this stuff at a later date (not super HP to know all details).


2004: Breiman: [Consistency for a Simple Model of Random Forests](https://www.stat.berkeley.edu/~breiman/RandomForests/consistencyRFA.pdf)

2008: Biau et al: [Consistency of Random Forests and Other Averaging Classifiers](http://www.jmlr.org/papers/volume9/biau08a/biau08a.pdf)

* What is "consistency"?
  - https://en.wikipedia.org/wiki/Consistency_(statistics)
* This paper looks further at "universal consistency"


2010: Ishwaran & Kogalur: [Consistency of Random Survival Forests](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2889677/)

2013: Denil et al: [Consistency of Online Random Forests](http://proceedings.mlr.press/v28/denil13.pdf)

2015: Scornet et al: [Consistency of random forests](https://arxiv.org/abs/1405.2881)

2018: Tang et al: [When do random forests fail?](https://papers.nips.cc/paper/7562-when-do-random-forests-fail.pdf)

---------------------------

# Variable Importance


2007: Ishwaran: [Variable importance in binary regression trees and forests](https://projecteuclid.org/download/pdfview_1/euclid.ejs/1195157166)

* "Averaging over trees [in a random forest], in combination with the randomization used in growing a tree, 
  enables random forests to approximate a rich class of functions while maintaining low generalization error. This 
  enables random forests to adapt to the data, automatically fitting higher order interactions and non-linear 
  effects, while at the same time keeping overfitting in check."
* PImp:  Historically, in "regression trees, node impurity is measured by mean squared error, whereas in classification 
  problems, the Gini index is used. The most popular VIMP method to date, however, adopts a prediction error 
  approach involving 'noising-up' a variable. In random forests, for example, VIMP for a variable x\[v\] is the 
  difference between prediction error when x\[v\] is noised up by permuting its value randomly, compared to prediction 
  error under the original predictor."
* Basically, these guys do not use PImp b/c its too hard to tract theoretically.  They instead use something they
  think is similar enough to PImp that their theoretical results are insightful.  The VImp they use goes like
  this: for a given variable x[v], travel down the tree branches until you reach the first split on x[v] or
  a terminal node; if a split on x[v] is reached, then use random left-right assignment on that split and for
  every split further down the branch.  Apparently, a reviewer criticised this since other inputs were being
  modified and could possibly cause a tree to become more informative while also not directly measuring the 
  importance of x[v], however the authors argued that while true for a single tree, this type of effect should
  wash out in the forest itself -- and, also, that they're theoretical results are still meaningful given
  one cares about this VImp score at all.  They said a potential remedy would be to only do the left-right
  randomization at x[v] splits in the tree, but for some reason this makes theoretical analysis much more
  intractable.  
* Paper gets pretty heavy with theorems and proofs going forward.  Shows that for VImp to have certain
  important properties, certain conditions have to be met (e.g., trees should be rich enough to 
  approximate f(X) and trees should be orthogonal), and that these are same conditions for RFs to
  achieve low generalization errors...
  
  


2009: Grömping: [Variable Importance Assessment in Regression: Linear Regression versus Random Forest](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C31&q=Variable+Importance+Assessment+in+Regression%3A+Linear+Regression+versus+Random+Forest&btnG=)

* "Variable importance in regression is an important topic in applied statistics that keeps coming up in spite of critics who basically claim that the question should not have been asked in the
first place."
* "Linear regression is a classical parametric method which requires explicit modeling of nonlinearities and 
  interactions, if necessary. It is known to be reasonably robust, if the number of observations n is distinctly 
  larger than the number of variables p (n>p). With more variables than observations (p>n or even p>>n), linear regression 
  breaks down, unless shrinkage methods are used like ridge regression, the lasso, or the elastic net as a 
  combination of both."
* "Random forests, on the other hand, are nonparametric and allow nonlinearities and interactions to be learned 
  from the data without any need to explicitly model them. Also, they have been reported to work well not only 
  for the n>>p setting but also for data mining inthe p>>n setting."
* "The splitting approach in CART trees has been known for a long time to be unfair in the presence of regressor 
  variables of different types, categorical variables with different numbers of categories, or differing numbers 
  of missing values  (cf., e.g.,Breiman 1984; 
  [Shih and Tsai 2004](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.435.2346&rep=rep1&type=pdf)). To 
  avoid this variable selection bias, 
  [Hothorn, Hornik, and Zeileis (2006b)](https://www.tandfonline.com/doi/abs/10.1198/106186006X133933) proposed 
  to use multiplicity-adjusted conditional tests rather than maximum impurity reduction as the 
  splitting criterion."
  - Wow, this quote would have been helpful a few months ago :-p
  - Also, note that I don't think their (interesting) p-value procedure at each node is implemented
    in sklearn (if anywhere (?))
  - In most places (sklearn included), a variation on what this paper refers to as RF-CART is implemented (true
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
* "A random forest is random in two ways: (i) each tree is based on a random subset of the observations, and 
  (ii) each split within each tree is created based on a random subset of mtry candidate variables. Trees are 
  quite unstable, so that this randomness creates differences in individual trees’ predictions. The overall 
  prediction of the forest is the average of predictions from the individual trees—because individual trees 
  produce multidimensional step functions, their average is again a multidimensional step function that can 
  nevertheless predict smooth functions because it aggregates a large number of different trees."
* An important difference between CART-like RFs and CI-RFs is that the first builds new n-sample data sets
  for each tree by sampling with replacement, while the CI-RFs provide each tree with about 63% of the 
  n-sample data set by sampling without replacement.  CI-RFs typically grow smaller trees by default, though
  this is a tweakable parameter (that is, in practice, just like we use CART-like RFs, we customize variants on
  the original/default CI-RFs). 
  - NOTE: in a 2008 follow-up paper 
    ("[Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307)"), 
    the Strobl group decide that their CI-RFs do not solve all the issues
    with variable importance, so they also argue that one must
    use conditional permuation importance as the measure of variable importance; this is also
    possible to apply to CART-like RFs.
  - GIST: Know that CI-RFs exist, but do not worry about 'em too much.
* "Variable importance is not very well defined as a concept. Even in the well-developed linear model and 
  the standard n>>p situation, there is no theoretically defined variable importance metric in the sense 
  of a parametric quantity that a variable importance estimator should try to estimate."
* "In the absence of a clearly agreed true value, ad hoc proposals for empirical assessment of variable 
  importance have been made, and desirability criteria for these have been formulated, for example, 'decomposition' 
  of R2 into 'nonnegative contributions attributable to each regressor' has been postulated. Popular approaches 
  for empirically assessing a variable’s importance include squared correlations (a completely marginal approach) 
  and squared standardized coefficients (an approach conditional on all other variables in the model)."
* Given no clear definition of variable importance, and the plethora of choices available to define such a thing,
  one must be prudent.  "Depending on the research question at hand, the focus of the variable importance
  assessment in regression can be explanatory or predictive importance or a mixture of both."  What definition
  best suits your goals?  (Heck, you can use more than one, as long as you clearly know what each measure
  means and what are its pros and cons.)
* For an intuition concerning what constitutes an explanatory measure of importance versus a predictive 
  measure of importance, the authors provide two causal chains: (i) X2 -> X1 -> Y;  (ii) X2 <- X1 -> Y.  Assuming
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
* This leads us into variable importances associated with random forests.  For example, the conditional permutation
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
  
  
  
  
  
  
  
  
-----------------------------------------------------------

# Tree Depth

Breiman originally pitched growing trees in a RF to terminal purity (the original CART-RF).  This is sometimes 
referred to growing trees to maximal depth, growing the largest trees, or just as growing large trees.  The idea and
motivation behind this is seemingly sound, and at first it empirically rang true.  

I found this cool paper that kind of shows a pivot point in the history of random forests from the
original CART-RF to more general CART-like RFs.  Basically, all the data in the UCI ML repository at the time
just so happened to be amenable to CART-RFs.  Namely, the data sets were hard to overfit by the CART-RF method,
and so there was over-optimism about CART-RF's ability to reduce model bias while leaving model variance relatively
untouched.  They found that some of the optimism could be restored by including another parameter or two, e.g.,
"max depth" or "max terminal node size".  

2004: Segal: [Machine Learning Benchmarks and Random Forest Regression](https://escholarship.org/uc/item/35x3v9t4)
* The individual trees in Breiman's original CART-RF "are all grown to maximal depth. While this helps with regard 
  bias, there is the familiar tradeoff with variance. However, these variability concerns were potentially obscured 
  because of an interesting feature of those benchmarking datasets extracted from the UCI machine learning repository 
  for testing: all these datasets are hard to overfit using tree-structured methods. This raises issues about the scope 
  of the repository."
* The authors found that "gains can be realized by additional tuning to regulate tree size via limiting the number 
  of splits and/or the size of nodes for which splitting is allowed."
* First, there were ensembles of bagged trees.  These bootstrap the data for each tree, then aggregate
  by voting or averaging, etc, but they do not randomly subsample the features at each split.
* From the ensembles of bagged trees, it was found that performance could be improved by better 
  de-correlating the individual trees.  This observation motivated the original random forests (CART-RFs),
  which added the random feature subsampling at each split.
* The paper focuses on regression RFs instead of classification RFs.  Basically, from what I can tell, the
  loss function in the regression forests is not a huge contributor to overfitting.  However, in 
  classification forests, overfitting is very sensitive to the choice of loss function.  Furthermore, the
  authors also noted that classification forests tended to perform poorly on imbalanced class problems 
  without additional machinery.  Regression forests were the easiest way to show their point independent
  of other effects.
* Prediction Error Inequality:  `PE(RF) <= r * PE(Tree)`, where r is the avg correlation between
  tree residuals in the forest.  This inequality shows that we can improve the RF in two ways: either
  by improving each individual tree and/or by reducing the correlation between each tree's residuals. For 
  the original CART-RF, this was accomplished as follows:
  1. To keep individual tree errors low, each tree is grown to maximal depth.
  2. To keep tree residual correlation low, randomize:
    - grow each tree on a bootstrap
    - choose mtry < p, then at every node of every tree, randomly choose mtry of the 
      p predictors to guide split decision 
* The paper's intent is to show that (1) controls the bias, but can generally inflate the variance and, thus,
  the prediction error.  They argue this was not initially observed because the standard data sets
  used for testing in the ML community from the UCI ML data repository all happened to work well with the
  RF method by happenchance.
* Their main argument is to limit the number of splits per tree, nsplit.  In scikit-learn, there are many
  other ways to do this as well.
  
-------------------------------------------------------------


2015: [Understanding Random Forests: from theory to practice](https://arxiv.org/pdf/1407.7502.pdf)



* 2005: [Predicting Good Probabilities With Supervised Learning](http://datascienceassn.org/sites/default/files/Predicting%20good%20probabilities%20with%20supervised%20learning.pdf)
  - "Experiments with eight classification problems suggest that random forests, neural nets
    and bagged decision trees are the best learning methods for
    predicting well-calibrated probabilities prior to calibration,
but after calibration the best methods are boosted trees, random forests and SVMs."


RFs can be updated (recalibrated) to new patient populations (either using the Elkan rule, or the Dankowski rule).
