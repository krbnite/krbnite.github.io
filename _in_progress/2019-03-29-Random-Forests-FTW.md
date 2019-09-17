

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


RFs are low bias, low variance:  https://hal.inria.fr/hal-01590513/document

RFs should be Grid/RandomSearched w/ terminal node size as a search parameter (originally believed that growing trees to terminal purity was always optimal; basically, this requires more trees in the forest to have a consistent estimator):  http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.75.2319&rep=rep1&type=pdf

Are RFs consistent estimators:  http://www.jmlr.org/papers/volume9/biau08a/biau08a.pdf

----------------------------------------------


# Probability Estimation

2005: Niculescu-Mizil & Caruana: [Predicting Good Probabilities With Supervised Learning](http://datascienceassn.org/sites/default/files/Predicting%20good%20probabilities%20with%20supervised%20learning.pdf)
"Experiments with eight classification problems suggest that random forests, neural nets and bagged decision trees are the best learning methods for predicting well-calibrated probabilities prior to calibration, but after calibration the best methods are boosted trees, random forests and SVMs."


2011: Malley et al: [Probability Machines: Consistent Probability Estimation Using Nonparametric Learning Machines](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3250568/)
& RFs are good for both classification and prob estimation
* “Trees should not be grown to purity. In fact, if nodes are pure, the probability estimate is either 0 or 1 in a terminal node.  As a result, a larger number of trees might be required to obtain consistent probability estimates. If trees are too small, probability estimates might be imprecise. Therefore, as a default value, the terminal node size is generally 10% of the total sample size. Alternatively, the optimal terminal node size may be tuned.”


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

2019: Josse et al: [On the consistency of supervised learning with missing values
](https://arxiv.org/abs/1902.06931)
  - paper that addresses that missing data is often studied in the inferential setting, but much less is known about 
    the predictive setting; for example, it finds that mean imputation isn't so bad in predictive setting as it 
    is considered in inferential setting
  


---------------------------

# Model Interpretability

## Variable Importance


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
  
  
2007: Strobl et al: [Bias in random forest variable importance measures: Illustrations, sources and a solution](https://link.springer.com/article/10.1186%2F1471-2105-8-25)

2008: Strobl et al: [Conditional variable importance for random forests](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2491635/)

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
  
  
2011: Nicodemus: [Letter to the Editor: On the stability and ranking of predictors from random forest variable importance measures](https://academic.oup.com/bib/article/12/4/369/241163)

  
2013: Janitza, Strobl, & Boulesteix : [An AUC-based permutation variable importance measure for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-119)

2017: Gregorutti et al: [Correlation and variable importance in random forests](https://arxiv.org/pdf/1310.5726.pdf)
  
2017: Epifanio: Intervention in prediction measure: a new approach to assessing variable importance for random forests
  
2018: Nembrini et al: [The revival of the Gini importance?](https://academic.oup.com/bioinformatics/article/34/21/3711/4994791)
2018: Parr et al: [Beware Default Random Forest Importances](https://explained.ai/rf-importance/)


[Interpretable ML Book](https://christophm.github.io/interpretable-ml-book)


## LIME
2016: Ribeiro et al: [“Why Should I Trust You?” Explaining the Predictions of Any Classifier](https://arxiv.org/pdf/1602.04938.pdf)

Undated: [Ch 5.7 of Intepretable Machine Learning: Local Surrogate (LIME)](https://christophm.github.io/interpretable-ml-book/lime.html)

Undated: Wright: [Justifying a random forest’s predictions](https://roywright.me/2018/02/09/justifying-random-forest/)


----------------------------------------------------------------

# Interactions

* 2014: Boulesteix et al: [Letter to the Editor: On the term ‘interaction’ and related phrases in the literature on Random Forests](https://academic.oup.com/bib/article/16/2/338/246566)
  
------------------------------------------------------

# Imbalanced Data
* 2007: Khoshgoftaar et al: [An Empirical Study of Learning from Imbalanced Data Using Random Forest](https://ieeexplore.ieee.org/abstract/document/4410397)


  
-----------------------------------------------------------

# Model Bias & Model Variance


Tree Depth / Number of Slits

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
  
  
2012: Genuer: Variance reduction in purely random forests (Link: https://hal.inria.fr/hal-01590513/document)

-------------------------------------------------------------





* 2005: [Predicting Good Probabilities With Supervised Learning](http://datascienceassn.org/sites/default/files/Predicting%20good%20probabilities%20with%20supervised%20learning.pdf)
  - "Experiments with eight classification problems suggest that random forests, neural nets
    and bagged decision trees are the best learning methods for
    predicting well-calibrated probabilities prior to calibration,
but after calibration the best methods are boosted trees, random forests and SVMs."


RFs can be updated (recalibrated) to new patient populations (either using the Elkan rule, or the Dankowski rule).

--------------------------------------


2018: Janitza: [On the overestimation of random forest’s out-of-bag error](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0201904)


------------------------------------------

# Missing Values

Undated: Becker: Kaggle: Handling Missing Values (Link: https://www.kaggle.com/dansbecker/handling-missing-values)
  - Basically, shows the importance of imputation + indication for predictive power
1998: Sarle: [Prediction with Missing Inputs](https://pdfs.semanticscholar.org/f1f5/d604e31c615d357a7729c1e7d506f3c3528d.pdf)
2007: Saar-Tsechansky & Provost: [Handling Missing Values when Applying Classification Models](http://jmlr.csail.mit.edu/papers/volume8/saar-tsechansky07a/saar-tsechansky07a.pdf)
2009: Janssen et al: [Dealing with Missing Predictor Values When Applying Clinical Prediction Models](https://pdfs.semanticscholar.org/0c67/97f88f12edef205a314188f936ffd5cc3e88.pdf)
  - Not specific to RFs, but important for this kind of work in general
2009: Garcia-Laencina et al: [Pattern classification with missing data: a review](https://sci2s.ugr.es/keel/pdf/specific/articulo/pattern-classification-with-missin-data-a-review-2009.pdf)
2010: Ding & Simonoff: [An Investigation of Missing Data Methods for Classification Trees Applied to Binary Response Data](http://www.jmlr.org/papers/volume11/ding10a/ding10a.pdf)
2010: Rieger et al: [Random Forests with Missing Values in the Covariates](https://epub.ub.uni-muenchen.de/11481/1/techreport.pdf)
2010: Vergouwe et al: [Development and validation of a prediction model with missing predictor data: a practical approach](https://www.sciencedirect.com/science/article/abs/pii/S0895435609001188)
2015: Wood et al: [The estimation and use of predictions for the assessment of model performance using large samples with multiply imputed data](https://onlinelibrary.wiley.com/doi/pdf/10.1002/bimj.201400004)
  - Not specific to RFs, but important for this kind of work in general
2018: Mercaldo & Bloom: [Missing data and prediction: the pattern submodel](https://academic.oup.com/biostatistics/advance-article/doi/10.1093/biostatistics/kxy040/5092384)
  - Not about RFs
  - Instead, this about a multi-tiered modeling approach where a model is developed for every missing data pattern
  - RFs or any other model can technically be used for each sub-model

2019: Josse et al: [On the consistency of supervised learning with missing values](https://arxiv.org/abs/1902.06931)
  - paper that addresses that missing data is often studied in the inferential setting, but much less is known about 
    the predictive setting; for example, it finds that mean imputation isn't so bad in predictive setting as it 
    is considered in inferential setting


-----------------------------------------------


# Deployment
Most of these articles are not necessarily specific to RFs, but are important in general for 
this type of work.

## Misc
2008: Guha: [Flexible Web Service Infrastructure for the Development and Deployment of Predictive Models](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.453.7552&rep=rep1&type=pdf)
2008: Bellazzi & Zupan: [Predictive data mining in clinical medicine: Current issues and guidelines](http://eprints.fri.uni-lj.si/994/1/2008-IJMI-BellazziZupan.pdf)
2009: Janssen et al: [A simple method to adjust clinical prediction models to local circumstances](https://link.springer.com/article/10.1007/s12630-009-9041-x)
2011: Vickers et al: [Everything you always wanted to know about evaluating prediction models (but were too afraid to ask)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2997853/)
2013: Paxton et al: [Developing Predictive Models Using Electronic Medical Records: Challenges and Pitfalls](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3900132/)
2013: Lin & Ryaboy: [Scaling Big Data Mining Infrastructure: The Twitter Experience](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.728.2&rep=rep1&type=pdf)
2013: Hendriksen et al: [Diagnostic and prognostic prediction models](https://onlinelibrary.wiley.com/doi/full/10.1111/jth.12262)
2014: Steyerberg & Vergouwe: [Towards better clinical prediction models: seven steps for development and an ABCD for validation](https://academic.oup.com/eurheartj/article/35/29/1925/2293109)
  - Not specifically about RFs, but important in general for this work
2015: Sculley et al: [Hidden Technical Debt in Machine Learning Systems](http://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)
2015: Khalilia et al: [Clinical Predictive Modeling Development and Deployment through FHIR Web Services](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4765683/)
2016: Breck et al: [What’s your ML Test Score? A rubric for ML production systems](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/45742.pdf)
2017: Polyzotis et al: [Data Management Challenges in Production Machine Learning](https://dl.acm.org/citation.cfm?id=3054782)
2018: Google: [Is My Data Any Good? A Pre-ML Checklist (v2)](https://services.google.com/fh/files/blogs/data-prep-checklist-ml-bd-wp-v2.pdf)
2018: Murphree et al: [Deploying Predictive Models In A Healthcare Environment - An Open Source Approach](https://ieeexplore.ieee.org/abstract/document/8513689)
2019: Tonekaboni et al: [What Clinicians Want: Contextualizing Explainable Machine Learning for Clinical End Use](https://arxiv.org/pdf/1905.05134.pdf)
  - Not specifically about RFs, but important in general for this work

## Data Leakage / Illegitimacy

2011: Kaufman et al: [Leakage in Data Mining: Formulation, Detection, and Avoidance](https://www.researchgate.net/publication/221653692_Leakage_in_Data_Mining_Formulation_Detection_and_Avoidance)

Undated: Larsren: [Data leakage in healthcare machine learning](https://healthcare.ai/data-leakage-in-healthcare-machine-learning/)

2018: Bergmann (SalesForce): [Hindsight Bias: How to Deal with Label Leakage at Scale](https://www.datacouncil.ai/talks/hindsight-bias-how-to-deal-with-label-leakage-at-scale)

2018: SalesForce: [Back to the Future: Demystifying Hindsight Bias](https://www.infoq.com/articles/data-leakage-hindsight-bias-machine-learning/)


-------------------------------------------------

# Peculiarities of Predictive Models (comparison w/ explanatory/inferential objectives)

2001: Breiman: [Statistical modeling: The two cultures (with comments and a rejoinder by the author)](https://projecteuclid.org/download/pdf_1/euclid.ss/1009213726)
2010: Schmeli: [To Explain or to Predict?](https://www.stat.berkeley.edu/~aldous/157/Papers/shmueli.pdf)
2011: Shmueli & Koppius: [Predictive Analytics in Information Systems Research](https://core.ac.uk/download/pdf/18520155.pdf)
2015: Lo et al: [Why significant variables aren’t automatically good predictors](https://www.pnas.org/content/112/45/13892.long)
2014: Allison: [Prediction vs. Causation in Regression Analysis](https://statisticalhorizons.com/prediction-vs-causation-in-regression-analysis)
2017: Yarkoni & Westfall: [Choosing Prediction Over Explanation in Psychology: Lessons From Machine Learning](https://journals.sagepub.com/doi/abs/10.1177/1745691617693393)

2017: Mullainathan & Spiess: [Machine Learning: An Applied Econometric Approach](https://pubs.aeaweb.org/doi/pdf/10.1257/jep.31.2.87)


----------------------------------------------

# RF Optimization (Scikit-Learn)

https://towardsdatascience.com/end-to-end-python-framework-for-predictive-modeling-b8052bb96a78

---------------------------------------------

# Reviews

2015: Biau & Scornet: [A Random Forest Guided Tour](https://arxiv.org/pdf/1511.05741.pdf)

2015: Biau and Scornet: [A Random Forest Guided Tour](https://arxiv.org/pdf/1511.05741.pdf)

*  Howard and Bowles (2012):  "Ensembles of decision trees (often known as 'random forests') have been the most 
  successful general-purpose algorithm in modern times."
*  Lin and Jeon (2006):  highlighted an interesting connection between random forests and a particular 
  class of nearest neighbor predictors
*  Scornet et al. (2015): show that Breiman’s (2001) forests are consistent in an additive regression framework.

2015: [Understanding Random Forests: from theory to practice](https://arxiv.org/pdf/1407.7502.pdf)


---------------------------------------------

# Time Series

2017: Tyralis & Papacharalampous: [Variable Selection in Time Series Forecasting Using Random Forests](https://www.mdpi.com/1999-4893/10/4/114)


--------------------------------------------

# Applications

### Bio / Healthcare
* 2011: Goldstein et al: [Random Forests for Genetic Association Studies](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3154091/)
* 2011: Dittman: [Random forest: A reliable tool for patient response prediction](https://ieeexplore.ieee.org/abstract/document/6112389)
* 2012: Boulesteix et al: [Overview of Random Forest Methodology and Practical Guidance with Emphasis on Computational Biology and Bioinformatics](https://epub.ub.uni-muenchen.de/13766/1/TR.pdf)
* 2012: Chen & Ishwaran: [Random forests for genomic data analysis](https://www.sciencedirect.com/science/article/pii/S0888754312000626)
* 2012: Qi: [Random Forest for Bioinformatics](http://www.montefiore.ulg.ac.be/~chaichoompu/CK/userfiles/downloads/2016/GBIO0002/HW2/3Random_Forests_for_Bioinformatics.pdf)

### Physics / Remote Sensing
* 2010: Carliles et al: [Random Forests for Photometric Redshifts](http://cis.jhu.edu/~parky/CEP-Publications/CBHSP-AJ2010.pdf)
* 2012: Rodriguez-Galiano et al: [An assessment of the effectiveness of a random forest classifier for land-cover classification](https://s3.amazonaws.com/academia.edu.documents/29631954/Rodriguez_Galiano__etal_2012_Random_forest_land_cover_ISPRS.pdf?response-content-disposition=inline%3B%20filename%3DAn_assessment_of_the_effectiveness_of_a.pdf&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWOWYYGZ2Y53UL3A%2F20190917%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190917T010957Z&X-Amz-Expires=3600&X-Amz-SignedHeaders=host&X-Amz-Signature=60e22062133bdddd59c3b2a5abfd85a26b3ed29c5d38d4750dadac5aadf5b319)
* 2016: Belgiu: [Random forest in remote sensing: A review of applications and future directions](https://daneshyari.com/article/preview/555862.pdf)
* 2016: Pelletier et al: [Assessing the robustness of Random Forests to map land cover with high resolution satellite image time series over large areas](http://recherche.ign.fr/labos/matis/pdf/articles_revues/2016/PVICD16.pdf)


### Ecology
* 2007: Cutler et al: [Random Forests for Classification in Ecology](https://digitalcommons.usu.edu/cgi/viewcontent.cgi?article=1805&context=wild_facpub)

---------------------------------

# Related Stuff

[Reinforcement Learning Trees](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4760114/)
