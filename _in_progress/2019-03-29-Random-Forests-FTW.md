

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


# Prediction Intervals / Confidence Intervals
* 2016:  Mentch & Hooker:  [Quantifying Uncertainty in Random Forests via Confidence Intervals and Hypothesis Tests](http://www.jmlr.org/papers/volume17/14-168/14-168.pdf)
* 2018: Ishwaran & Lu: [Standard errors and confidence intervals for variable importance in random forest regression, classification, and survival†](https://europepmc.org/articles/pmc6279615)
* 2018: Roy: [Three Essays on Nonparametric Prediction Intervals and Robust Variable Selection](http://biblos.hec.ca/biblio/theses/2018NO10.pdf#page=23)
  - Ch1: Prediction Intervals with Random Forests 
  - Ch2:  Prediction Intervals for Finite Mixture of Regressions Based on Random Forests
* 2019: Zhang et al:  [Random Forest Prediction Intervals](https://www.tandfonline.com/doi/abs/10.1080/00031305.2019.1585288)
  

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
  
# Calibration
* 2008: Bostrom: [Calibrating Random Forests](https://people.dsv.su.se/~henke/papers/bostrom08b.pdf)
* 2016: Dankowski & Ziegler: [Calibrating random forests for probability estimation](https://onlinelibrary.wiley.com/doi/pdf/10.1002/sim.6959)


# Categorical Variable Stuff
* 2019: Wright & Konig: [Splitting on categorical predictors in random forests](https://peerj.com/articles/6339/)


# Additivity
* 2017: Mentch & Hooker: [Formal Hypothesis Tests for Additive Structure in Random Forests](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6428414/)

# Statistical Testing
* 2019: Hediger et al: [On the Use of Random Forest for Two-Sample Testing](https://arxiv.org/pdf/1903.06287.pdf)

--------------------------------
  
# Misc Internal Workings and Mathiness of RF
* 2012: Oshiro & Baranauskas: [Root Attribute Behavior within a Random Forest](https://www.researchgate.net/profile/Jose_Baranauskas/publication/230766592_Root_Attribute_Behavior_within_a_Random_Forest/links/09e415040fc2c75e36000000.pdf)
* 2019: Lopes: [Estimating the Algorithmic Variance of Randomized Ensembles via the Bootstrap](http://anson.ucdavis.edu/~melopes/info/AOS_2019.pdf)
* 2019: Lopes et al: [Measuring the Algorithmic Convergence of Randomized Ensembles: The Regression Setting](https://arxiv.org/pdf/1908.01251.pdf)

--------------------------


# Model Optimization

* 2001: Latinne et al: [Limiting the Number of Trees in Random Forests](https://www.researchgate.net/profile/Olivier_Debeir/publication/221093985_Limiting_the_Number_of_Trees_in_Random_Forests/links/0c96053392aa346efb000000/Limiting-the-Number-of-Trees-in-Random-Forests.pdf)
* 2009: Bernard et al: [Influence of Hyperparameters on Random Forest Accuracy](https://hal.archives-ouvertes.fr/hal-00436358/file/mcs09.pdf)
* 2009: Bernard et al: [On the selection of decision trees in Random Forests](https://hal.archives-ouvertes.fr/file/index/docid/436355/filename/ijcnn09.pdf)
* 2009: Bernard et al: [Towards a Better Understanding of Random Forests Through the Study of Strength and Correlation](https://hal.archives-ouvertes.fr/hal-00436361/document)
* 2010: Bernard et al: [A Study of Strength and Correlation in Random Forests](https://hal.archives-ouvertes.fr/hal-00598466/document)
* 2012: Oshiro et al: [How many trees in a random forest?](https://www.researchgate.net/profile/Jose_Baranauskas/publication/230766603_How_Many_Trees_in_a_Random_Forest/links/0912f5040fb35357a1000000/How-Many-Trees-in-a-Random-Forest.pdf)
* 2016: Orlandi: [Random Forests Model Selection](https://www.elen.ucl.ac.be/Proceedings/esann/esannpdf/es2016-48.pdf)
* 2017: Probst & Boulesteix: [To Tune or Not to Tune the Number of Trees in Random Forest](http://www.jmlr.org/papers/volume18/17-269/17-269.pdf)
* 2019: Probst et al: [Hyperparameters and Tuning Strategies for Random Forest](https://arxiv.org/pdf/1804.03515.pdf)



## Reduced Computational Burden (Tiny Forest Techniques)
* 2009: Zhang & Wang:  [Search for the smallest random forest](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2822360/)
* 2010: Bernard et al: [A Study of Strength and Correlation in Random Forests](https://hal.archives-ouvertes.fr/hal-00598466/document)
* 2015: Fawagreh et al:  [On Extreme Pruning of Random Forest Ensembles for Real-time Predictive Applications](https://arxiv.org/pdf/1503.04996.pdf)
* 2019: Khan & Lausen: [Ensemble of optimal trees, random forest and random projection ensemble classification](https://link.springer.com/article/10.1007/s11634-019-00364-9)


---------------------------

# Model Interpretability

## Variable Importance and Selection

* 2005: Diaz-Uriarte & Alvarez de Andres: [Variable selection from random forests: application to gene expression data]

* 2007: Ishwaran: [Variable importance in binary regression trees and forests](https://projecteuclid.org/download/pdfview_1/euclid.ejs/1195157166)
  - "Averaging over trees [in a random forest], in combination with the randomization used in growing a tree, 
  enables random forests to approximate a rich class of functions while maintaining low generalization error. This 
  enables random forests to adapt to the data, automatically fitting higher order interactions and non-linear 
  effects, while at the same time keeping overfitting in check."
  - PImp:  Historically, in "regression trees, node impurity is measured by mean squared error, whereas in classification 
  problems, the Gini index is used. The most popular VIMP method to date, however, adopts a prediction error 
  approach involving 'noising-up' a variable. In random forests, for example, VIMP for a variable x\[v\] is the 
  difference between prediction error when x\[v\] is noised up by permuting its value randomly, compared to prediction 
  error under the original predictor."
  - Basically, these guys do not use PImp b/c its too hard to tract theoretically.  They instead use something they
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
  - Paper gets pretty heavy with theorems and proofs going forward.  Shows that for VImp to have certain
  important properties, certain conditions have to be met (e.g., trees should be rich enough to 
  approximate f(X) and trees should be orthogonal), and that these are same conditions for RFs to
  achieve low generalization errors...
  
  
* 2007: Strobl et al: [Bias in random forest variable importance measures: Illustrations, sources and a solution](https://link.springer.com/article/10.1186%2F1471-2105-8-25)

* 2008: Strobl et al: [Conditional variable importance for random forests](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2491635/)

* 2009: Grömping: [Variable Importance Assessment in Regression: Linear Regression versus Random Forest](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C31&q=Variable+Importance+Assessment+in+Regression%3A+Linear+Regression+versus+Random+Forest&btnG=)
  - "Variable importance in regression is an important topic in applied statistics that keeps coming up in 
    spite of critics who basically claim that the question should not have been asked in the first place."
  - "Linear regression is a classical parametric method which requires explicit modeling of nonlinearities and 
    interactions, if necessary. It is known to be reasonably robust, if the number of observations n is distinctly 
    larger than the number of variables p (n>p). With more variables than observations (p>n or even p>>n), linear regression 
    breaks down, unless shrinkage methods are used like ridge regression, the lasso, or the elastic net as a 
    combination of both."
  - "Random forests, on the other hand, are nonparametric and allow nonlinearities and interactions to be learned 
    from the data without any need to explicitly model them. Also, they have been reported to work well not only 
    for the n>>p setting but also for data mining inthe p>>n setting."
  - "The splitting approach in CART trees has been known for a long time to be unfair in the presence of regressor 
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
  - "A random forest is random in two ways: (i) each tree is based on a random subset of the observations, and 
    (ii) each split within each tree is created based on a random subset of mtry candidate variables. Trees are 
    quite unstable, so that this randomness creates differences in individual trees’ predictions. The overall 
    prediction of the forest is the average of predictions from the individual trees—because individual trees 
    produce multidimensional step functions, their average is again a multidimensional step function that can 
    nevertheless predict smooth functions because it aggregates a large number of different trees."
  - An important difference between CART-like RFs and CI-RFs is that the first builds new n-sample data sets
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
  - "Variable importance is not very well defined as a concept. Even in the well-developed linear model and 
    the standard n>>p situation, there is no theoretically defined variable importance metric in the sense 
    of a parametric quantity that a variable importance estimator should try to estimate."
  - "In the absence of a clearly agreed true value, ad hoc proposals for empirical assessment of variable 
    importance have been made, and desirability criteria for these have been formulated, for example, 'decomposition' 
    of R2 into 'nonnegative contributions attributable to each regressor' has been postulated. Popular approaches 
    for empirically assessing a variable’s importance include squared correlations (a completely marginal approach) 
    and squared standardized coefficients (an approach conditional on all other variables in the model)."
  - Given no clear definition of variable importance, and the plethora of choices available to define such a thing,
    one must be prudent.  "Depending on the research question at hand, the focus of the variable importance
    assessment in regression can be explanatory or predictive importance or a mixture of both."  What definition
    best suits your goals?  (Heck, you can use more than one, as long as you clearly know what each measure
    means and what are its pros and cons.)
  - For an intuition concerning what constitutes an explanatory measure of importance versus a predictive 
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
  - This leads us into variable importances associated with random forests.  For example, the conditional permutation
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
  
  
* 2010: Altmann et al: [Permutation importance: a corrected feature importance measure](https://academic.oup.com/bioinformatics/article/26/10/1340/193348)

* 2010: Calle & Urrea: [Letter to the Editor: Stability of Random Forest importance measures](https://academic.oup.com/bib/article/12/1/86/243935)
  
* 2010: Genuer et al: [Variable Selection using Random Forests](https://hal.archives-ouvertes.fr/file/index/docid/755489/filename/PRLv4.pdf)


* 2011: Nicodemus: [Letter to the Editor: On the stability and ranking of predictors from random forest variable importance measures](https://academic.oup.com/bib/article/12/4/369/241163)

* 2012: Hapfelmeier et al: [A new variable importance measure for random forests with missing data](http://www.psychologie.uzh.ch/methpsy/publications/STCO_20120809.pdf)
  
* 2013: Janitza, Strobl, & Boulesteix : [An AUC-based permutation variable importance measure for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-119)

* 2015: Painsky & Rosset:  [Cross-Validated Variable Selection in Tree-Based Methods Improves Predictive Performance](https://arxiv.org/pdf/1512.03444.pdf)

* 2017: Gregorutti et al: [Correlation and variable importance in random forests](https://arxiv.org/pdf/1310.5726.pdf)
  
* 2017: Epifanio: Intervention in prediction measure: a new approach to assessing variable importance for random forests
  

* 2017: Behnamian et al: [A Systematic Approach for Variable Selection With Random Forests: Achieving 
Stable Variable Importance Values](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8038868)
  - We've all been there: train your RF, look at the feature importances.  Maybe you want to find the
    top 10 features to go ahead and make a more computationally efficient model (that also lessens the
    burden of future data collection needs).  Or maybe you just want to better explain the internal
    logic of your model to a stakeholder.  Whatever: the results look interesting, but you decide to
    add more trees and retrain to see if you get better accuracy.  Nice!  You do!  But, wait: 
    the imporantces changed rankings.  So you add some more trees, train again -- and again a slightly
    different set of importance rankings.  What's worse: your curiosity gets the best of you and without
    changing any hyperparameters whatsoever, you retrain and get yet a different ranking. It doesn't 
    matter if you're using Gini importance (aka mean decrease in impurity) or permutation importance
    (aka mean decrease in accuracy).  Both  prove to be fairly unstable.  What's the deal here?!
  - This paper looks at (i) how large a forest must be to produce stable importance estimates, and
    (ii) how class separability affects this result.
    * They do find that, for large enough nTree, the importances stabilize
    * They also find that averaging the rankings over multiple runs with smaller nTrees produces stable estimate
    * The specifics are driven by the class separability
  - Interestingly, this paper is about the use of RFs in signal and image processing, where the data is
    collected at high temporal and/or spatial frequency.  
    * This motivates the need for stable importance rankings: "Reducing model data load can reduce processing
      times and storage requirements, and can also be used to inform longterm analyses, as attention can focus 
      on just the sensors and variables that provide relevant information to a given classification problem."
    * Furthermore, in this signal/image analysis field, it has been shown that RFs can be improved quite a 
      bit by finding and removing any noise variables.
  - Importantly, independent of what VImp measure one uses, "because of the random way in which training data 
    and variables are selected to determine the split at each node in Random Forests, importance rankings 
    differ from one model run to another, especially when if only a small ntree are generated."
      

* 2017: Mentch & Hooker: [Formal Hypothesis Tests for Additive Structure in Random Forests](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6428414/)

* 2018: Nembrini et al: [The revival of the Gini importance?](https://academic.oup.com/bioinformatics/article/34/21/3711/4994791)
* 2018: Parr et al: [Beware Default Random Forest Importances](https://explained.ai/rf-importance/)

* 2018: Roy: [Three Essays on Nonparametric Prediction Intervals and Robust Variable Selection](http://biblos.hec.ca/biblio/theses/2018NO10.pdf#page=23)
  - Ch3: High-Dimensional Variable Screening using a Robust Ensemble Method 
 
* 2019: Fisher et al: [All Models are Wrong, but Many are Useful: Learning a Variable’s Importance by Studying an Entire Class of Prediction Models Simultaneously](https://arxiv.org/pdf/1801.01489.pdf)

* [Interpretable ML Book](https://christophm.github.io/interpretable-ml-book)


## Variable Importance Stability
Some of these are not exclusive to RFs.

* 2005: Kalousis et al: [Stability of Feature Selection Algorithms](https://www.researchgate.net/profile/Alexandros_Kalousis/publication/4207687_Stability_of_feature_selection_algorithms/links/0fcfd50abb62563e08000000/Stability-of-feature-selection-algorithms.pdf)
* 2007: Kalousis et al: [Stability of feature selection algorithms: a study on high-dimensional spaces](http://doc.rero.ch/record/312354/files/10115_2006_Article_40.pdf)
* 2008: Saeys et al: [Robust Feature Selection Using Ensemble Feature Selection Techniques](https://link.springer.com/content/pdf/10.1007/978-3-540-87481-2_21.pdf)
* 2011: Toloşi & Lengauer: [Classification with correlated features: unreliability of feature ranking and solutions](https://academic.oup.com/bioinformatics/article/27/14/1986/194387)
* 2014: Kursa: [Robustness of Random Forest-based gene selection methods](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-15-8)
* 2016: Wang et al: [An experimental study of the intrinsic stability of random forest variable importance measures](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-016-0900-5)
* 2017: Behnamian et al: [A Systematic Approach for Variable Selection With Random Forests: Achieving 
Stable Variable Importance Values](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8038868)
* 2019: Fisher et al: [All Models are Wrong, but Many are Useful: Learning a Variable’s Importance by Studying an Entire Class of Prediction Models Simultaneously](https://arxiv.org/pdf/1801.01489.pdf)



## LIME
2016: Ribeiro et al: [“Why Should I Trust You?” Explaining the Predictions of Any Classifier](https://arxiv.org/pdf/1602.04938.pdf)

Undated: [Ch 5.7 of Intepretable Machine Learning: Local Surrogate (LIME)](https://christophm.github.io/interpretable-ml-book/lime.html)

Undated: Wright: [Justifying a random forest’s predictions](https://roywright.me/2018/02/09/justifying-random-forest/)


## Simplified Global Models
* 2016: Tan et al: [Tree Space Prototypes: Another Look at Making Tree Ensembles Interpretable](https://arxiv.org/pdf/1611.07115.pdf)
* 2016: Zhou & Hooker: [Interpreting Models via Single Tree Approximation](https://arxiv.org/pdf/1610.09036.pdf)


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


* 2018: Janitza: [On the overestimation of random forest’s out-of-bag error](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0201904)


------------------------------------------

# Missing Values

* Undated: Becker: Kaggle: Handling Missing Values (Link: https://www.kaggle.com/dansbecker/handling-missing-values)
  - Basically, shows the importance of imputation + indication for predictive power

* 1998: Sarle: [Prediction with Missing Inputs](https://pdfs.semanticscholar.org/f1f5/d604e31c615d357a7729c1e7d506f3c3528d.pdf)
* 2007: Saar-Tsechansky & Provost: [Handling Missing Values when Applying Classification Models](http://jmlr.csail.mit.edu/papers/volume8/saar-tsechansky07a/saar-tsechansky07a.pdf)
* 2009: Janssen et al: [Dealing with Missing Predictor Values When Applying Clinical Prediction Models](https://pdfs.semanticscholar.org/0c67/97f88f12edef205a314188f936ffd5cc3e88.pdf)
  - Not specific to RFs, but important for this kind of work in general
* 2009: Garcia-Laencina et al: [Pattern classification with missing data: a review](https://sci2s.ugr.es/keel/pdf/specific/articulo/pattern-classification-with-missin-data-a-review-2009.pdf)
* 2010: Ding & Simonoff: [An Investigation of Missing Data Methods for Classification Trees Applied to Binary Response Data](http://www.jmlr.org/papers/volume11/ding10a/ding10a.pdf)
* 2010: Rieger et al: [Random Forests with Missing Values in the Covariates](https://epub.ub.uni-muenchen.de/11481/1/techreport.pdf)
* 2010: Vergouwe et al: [Development and validation of a prediction model with missing predictor data: a practical approach](https://www.sciencedirect.com/science/article/abs/pii/S0895435609001188)
* 2015: Wood et al: [The estimation and use of predictions for the assessment of model performance using large samples with multiply imputed data](https://onlinelibrary.wiley.com/doi/pdf/10.1002/bimj.201400004)
  - Not specific to RFs, but important for this kind of work in general
* 2015: Masconi et al: [Effects of Different Missing Data Imputation Techniques on the Performance of Undiagnosed Diabetes Risk Prediction Models in a Mixed-Ancestry Population of South Africa](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0139210)
  -- Not specific to RFs
* 2016: Held et al: [Methods for Handling Missing Variables in Risk Prediction Models](https://academic.oup.com/aje/article/184/7/545/2594506)
  - Not specific to RFs...
* 2018: Mercaldo & Bloom: [Missing data and prediction: the pattern submodel](https://academic.oup.com/biostatistics/advance-article/doi/10.1093/biostatistics/kxy040/5092384)
  - Not about RFs
  - Instead, this about a multi-tiered modeling approach where a model is developed for every missing data pattern
  - RFs or any other model can technically be used for each sub-model

* 2019: Josse et al: [On the consistency of supervised learning with missing values](https://arxiv.org/abs/1902.06931)
  - paper that addresses that missing data is often studied in the inferential setting, but much less is known about 
    the predictive setting; for example, it finds that mean imputation isn't so bad in predictive setting as it 
    is considered in inferential setting


-----------------------------------------------


# Deployment
Most of these articles are not necessarily specific to RFs, but are important in general for 
this type of work.

## Misc
* 2008: Guha: [Flexible Web Service Infrastructure for the Development and Deployment of Predictive Models](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.453.7552&rep=rep1&type=pdf)
* 2008: Bellazzi & Zupan: [Predictive data mining in clinical medicine: Current issues and guidelines](http://eprints.fri.uni-lj.si/994/1/2008-IJMI-BellazziZupan.pdf)
* 2009: Janssen et al: [A simple method to adjust clinical prediction models to local circumstances](https://link.springer.com/article/10.1007/s12630-009-9041-x)
* 2011: Vickers et al: [Everything you always wanted to know about evaluating prediction models (but were too afraid to ask)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2997853/)
* 2013: Paxton et al: [Developing Predictive Models Using Electronic Medical Records: Challenges and Pitfalls](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3900132/)
* 2013: Lin & Ryaboy: [Scaling Big Data Mining Infrastructure: The Twitter Experience](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.728.2&rep=rep1&type=pdf)
* 2013: Hendriksen et al: [Diagnostic and prognostic prediction models](https://onlinelibrary.wiley.com/doi/full/10.1111/jth.12262)
* 2014: Steyerberg & Vergouwe: [Towards better clinical prediction models: seven steps for development and an ABCD for validation](https://academic.oup.com/eurheartj/article/35/29/1925/2293109)
  - Not specifically about RFs, but important in general for this work
* 2015: Sculley et al: [Hidden Technical Debt in Machine Learning Systems](http://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)
* 2015: Khalilia et al: [Clinical Predictive Modeling Development and Deployment through FHIR Web Services](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4765683/)
* 2016: Breck et al: [What’s your ML Test Score? A rubric for ML production systems](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/45742.pdf)
* 2017: Polyzotis et al: [Data Management Challenges in Production Machine Learning](https://dl.acm.org/citation.cfm?id=3054782)
* 2018: Google: [Is My Data Any Good? A Pre-ML Checklist (v2)](https://services.google.com/fh/files/blogs/data-prep-checklist-ml-bd-wp-v2.pdf)
* 2018: Murphree et al: [Deploying Predictive Models In A Healthcare Environment - An Open Source Approach](https://ieeexplore.ieee.org/abstract/document/8513689)
* 2019: Tonekaboni et al: [What Clinicians Want: Contextualizing Explainable Machine Learning for Clinical End Use](https://arxiv.org/pdf/1905.05134.pdf)
  - Not specifically about RFs, but important in general for this work


## Data Leakage / Illegitimacy
* 2011: Kaufman et al: [Leakage in Data Mining: Formulation, Detection, and Avoidance](https://www.researchgate.net/publication/221653692_Leakage_in_Data_Mining_Formulation_Detection_and_Avoidance)
* Undated: Larsren: [Data leakage in healthcare machine learning](https://healthcare.ai/data-leakage-in-healthcare-machine-learning/)
* 2018: Bergmann (SalesForce): [Hindsight Bias: How to Deal with Label Leakage at Scale](https://www.datacouncil.ai/talks/hindsight-bias-how-to-deal-with-label-leakage-at-scale)
* 2018: SalesForce: [Back to the Future: Demystifying Hindsight Bias](https://www.infoq.com/articles/data-leakage-hindsight-bias-machine-learning/)


# Causal Inference & Significance
* 2016:  Athey & Imbens:  [Recursive partitioning for heterogeneous causal effects](https://www.pnas.org/content/pnas/113/27/7353.full.pdf)
* 2016:  Athey & Tibshirani:  [Solving Heterogeneous Estimating Equations with Gradient Forests](https://www.gsb.stanford.edu/sites/gsb/files/rp3475.pdf)
* 2016:  Chernozhukov et al:  [Double machine learning for treatment and causal parameters](https://www.econstor.eu/bitstream/10419/149795/1/869216953.pdf)
* 2016:  Mentch & Hooker:  [Quantifying Uncertainty in Random Forests via Confidence Intervals and Hypothesis Tests](http://www.jmlr.org/papers/volume17/14-168/14-168.pdf)
* 2017: Hahn et al:  [Bayesian regression tree models for causal inference: regularization, confounding, and heterogeneous effects](https://arxiv.org/abs/1706.09523)
* 2017:  Wager & Athey:  [Estimation and Inference of Heterogeneous Treatment Effects using Random Forests](https://www.tandfonline.com/doi/full/10.1080/01621459.2017.1319839)
* 2018:  Chernozhukov et al:  [Double/debiased machine learning for treatment and structural parameters](https://academic.oup.com/ectj/article/21/1/C1/5056401)
* 2019:  Athey & Imbens:  [Machine Learning Methods Economists Should Know About](https://arxiv.org/abs/1903.10075)
* 2019:  Athey & Wager:  [Efficient Policy Learning](https://arxiv.org/pdf/1702.02896.pdf)
* 2019:  Athey et al:  [Generalized random forests](https://arxiv.org/abs/1610.01271)
* 2019:  Kunzel et al:  [Metalearners for estimating heterogeneous treatment effects using machine learning](https://www.pnas.org/content/pnas/116/10/4156.full.pdf)

-------------------------------------------------

# Comparison with other classifiers

* 2006: Caruana et al: [An empirical comparison of supervised learning algorithms](http://www.cs.cornell.edu/~caruana/ctp/ct.papers/caruana.icml06.pdf)
* 2009: Ben-David & Frank: Accuracy of machine learning models versus “hand crafted” expert systems–a credit scoring case study
* 2015: Saha & Nandi: [Data classification based on decision tree, rule generation, bayes and statistical methods: an empirical comparison](https://pdfs.semanticscholar.org/7780/afa76792478b715d92857f8648f2e9c794da.pdf)
* 2014: Fernandez-Delgado et al: [Do we Need Hundreds of Classifiers to Solve Real World
Classification Problems?](http://www.jmlr.org/papers/volume15/delgado14a/delgado14a.pdf)
* 2016: Wainberg et al: [Are Random Forests Truly the Best Classifiers?](http://www.jmlr.org/papers/volume17/15-374/15-374.pdf)
* 2016: Mera et al: Comparison of a massive and diverse collection of ensembles and other classifiers for oil spill detection in sar satellite images
* 2018: Couronne et al: [Random forest versus logistic regression: a large-scale benchmark experiment](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-018-2264-5)
* 2019: Barnard et al: [Cannot see the random forest for the decision trees: selecting predictive models for restoration ecology]
* Undated: [Neural Networks vs. Random Forests – Does it always have to be Deep Learning](https://blog.frankfurt-school.de/wp-content/uploads/2018/10/Neural-Networks-vs-Random-Forests.pdf)


https://www.quora.com/When-should-random-forest-be-used-over-logistic-regression-for-classification-and-vice-versa

--------------------------------------------------

# Related Forests

### Alternating Decision Forests
* 2013: Schulter1 et al: [Alternating Decision Forests](http://openaccess.thecvf.com/content_cvpr_2013/papers/Schulter_Alternating_Decision_Forests_2013_CVPR_paper.pdf)

### Causal Forests
Note that some of these forests  differ in implementation.  However, since they are developed to be capable
for causal inference, I keep them together here.  (Also, "causal forest" is in most of their names.)

* 2016:  Athey & Tibshirani:  [Solving Heterogeneous Estimating Equations with Gradient Forests](https://www.gsb.stanford.edu/sites/gsb/files/rp3475.pdf)
* 2017: Hahn et al:  [Bayesian regression tree models for causal inference: regularization, confounding, and heterogeneous effects](https://arxiv.org/abs/1706.09523)
* 2017:  Wager & Athey:  [Estimation and Inference of Heterogeneous Treatment Effects using Random Forests](https://www.tandfonline.com/doi/full/10.1080/01621459.2017.1319839)


### Deep and Neural Forests
* [Deep Forest](https://arxiv.org/pdf/1702.08835.pdf)
* [Neural Random Forests](https://arxiv.org/abs/1604.07143)
* [Deep Neural Decision Forests](https://www.cv-foundation.org/openaccess/content_iccv_2015/papers/Kontschieder_Deep_Neural_Decision_ICCV_2015_paper.pdf)

### Dynamic Random Forests
2012: Bernard et al: Dynamic Random Forests](https://hal.archives-ouvertes.fr/hal-00710083/file/drf2.pdf)

### Extremely Randomized (ExtRa) Trees 

### Forests of Probability Estimation Trees (PETs)
* 2012: Bostrom: [Forests of Probability Estimation Trees]

### Functional Random Forests
* 2019: Rahman et al: [Functional random forest with applications in dose-response predictions](https://www.nature.com/articles/s41598-018-38231-w)

### Fuzzy Forests
2010: Bonissone et al: Fuzzy Random Forest: Fundamental for Design and Construction
2010: Bonissone et al: A Fuzzy Random Forest

### Hough Forests
* 2011: Gall: [Hough Forests for Object Detection, Tracking, and Action Recognition](http://www.ipb.uni-bonn.de/html/projects/MoD/literatur/pdf/p7-2011_Gall_Houghforests.pdf)
* 2011: Schulter et al: [On-line Hough Forests](https://pdfs.semanticscholar.org/b321/626fb582012d0e5d102ef5eba4db5a151363.pdf)
* 2011: Girshick et al: [Efficient regression of general-activity human poses from depth images](https://ieeexplore.ieee.org/abstract/document/6126270)
* 2013: Gall & Lempitsky: [Class-Specific Hough Forests for Object Detection](https://www.robots.ox.ac.uk/~vilem/cvpr2009.pdf)

### Isolation Forest
2008: Liu et al: [Isolation Forest](https://cs.nju.edu.cn/zhouzh/zhouzh.files/publication/icdm08b.pdf?q=isolation-forest)

### Local Linear Forests
* 2019: Friedberg et al: [Local Linear Forests](https://arxiv.org/pdf/1807.11408.pdf)

### Meta Random Forests
* 2008: Boinee et al: [Meta Random Forests](https://pdfs.semanticscholar.org/c173/cbe5a681324575742a62fd01b04529582039.pdf)

### Mondrian Forests
2014: Lakshminarayanan et al: [Mondrian Forests: Efficient Online Random Forests](https://papers.nips.cc/paper/5234-mondrian-forests-efficient-online-random-forests.pdf)
2018: Thapliyal: [Mondrian Forests : Making Random Forests better and efficient](https://medium.com/mlrecipies/mondrian-forests-making-random-forests-better-and-efficient-b27814c681e5)

### Oblique Random Forests
* 2011: Menze: [On Oblique Random Forests](http://people.csail.mit.edu/menze/papers/menze_11_oblique.pdf)
* 2016: Poona et al: [Investigating the Utility of Oblique Tree-Based Ensembles for the Classification of Hyperspectral Data](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5134577/)
* 2018: Agjee et al: [The Impact of Simulated Spectral Noise on Random Forest and Oblique Random Forest Classification Performance](https://www.hindawi.com/journals/jspec/2018/8316918/)

### Quantile Regression Forests
2006: Meinshausen: [Quantile Regression Forests](http://www.jmlr.org/papers/volume7/meinshausen06a/meinshausen06a.pdf)
Undated: [Scikit Garden: Quantile Regression Forests](https://scikit-garden.github.io/examples/QuantileRegressionForests/)

### Random Forests for Spatial Prediction (RFsp)
* 2017:  Hengl et al:  [Random forest as a generic framework for predictive modeling of spatial and spatio-temporal variables](https://peerj.com/articles/5518/)

### Random Rotation Ensembles
* 2016: Blaser & Fryzlewicz: [Random Rotation Ensembles](http://www.jmlr.org/papers/volume17/blaser16a/blaser16a.pdf)

### Reinforcement Learning Trees
[Reinforcement Learning Trees](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4760114/)

### Rotation Forest
* 2006: Rodriguez et al: [Rotation Forest: A New Classifier Ensemble Method](https://ieeexplore.ieee.org/abstract/document/1677518)

### Semi-Supervised Random Forests
2009: Leistner et al: [Semi-Supervised Random Forests](http://www.ymer.org/papers/files/2009-SemisupervisedRandomForests.pdf)

### Spatially-Biased Random Forests
* 2019: Mitchell & Sheppard: [Spatially Biased Random Forests](https://www.cs.montana.edu/sheppard/pubs/flairs-2019.pdf)

### Structured Forests
* 2013: Dollar et al: [Structured forests for fast edge detection](http://openaccess.thecvf.com/content_iccv_2013/papers/Dollar_Structured_Forests_for_2013_ICCV_paper.pdf)
* 2014: Dollar et al: [Fast edge detection using structured forests](https://arxiv.org/pdf/1406.5549.pdf)

### Unsupervised Stuff
2005: Shi & Horvath: Unsupervised Learning With Random Forest Predictors](https://www.researchgate.net/profile/Tao_Shi3/publication/228650239_Unsupervised_Learning_With_Random_Forest_Predictors/links/555bd0e908ae91e75e766bf5.pdf)
2016: Afanador et al: [Unsupervised random forest: a tutorial with case studies](https://onlinelibrary.wiley.com/doi/abs/10.1002/cem.2790)

### Weighted RFs
2014: Baumann et al: [Thresholding a Random Forest Classifier](http://www.tnt.uni-hannover.de/papers/data/1055/isvc2014_baumann.pdf)


-------------------------------------------------

# Peculiarities of Predictive Models (comparison w/ explanatory/inferential objectives)

* 2001: Breiman: [Statistical modeling: The two cultures (with comments and a rejoinder by the author)](https://projecteuclid.org/download/pdf_1/euclid.ss/1009213726)
* 2010: Schmeli: [To Explain or to Predict?](https://www.stat.berkeley.edu/~aldous/157/Papers/shmueli.pdf)
* 2011: Shmueli & Koppius: [Predictive Analytics in Information Systems Research](https://core.ac.uk/download/pdf/18520155.pdf)
* 2014: Allison: [Prediction vs. Causation in Regression Analysis](https://statisticalhorizons.com/prediction-vs-causation-in-regression-analysis)
2015: Lo et al: [Why significant variables aren’t automatically good predictors](https://www.pnas.org/content/112/45/13892.long)
* 2017: Yarkoni & Westfall: [Choosing Prediction Over Explanation in Psychology: Lessons From Machine Learning](https://journals.sagepub.com/doi/abs/10.1177/1745691617693393)
* 2017: Mullainathan & Spiess: [Machine Learning: An Applied Econometric Approach](https://pubs.aeaweb.org/doi/pdf/10.1257/jep.31.2.87)
* 2019:  Athey & Imbens:  [Machine Learning Methods Economists Should Know About](https://arxiv.org/abs/1903.10075)


----------------------------------------------

# RF Optimization (Scikit-Learn)

https://towardsdatascience.com/end-to-end-python-framework-for-predictive-modeling-b8052bb96a78

---------------------------------------------

# Reviews

* 2013: Kulkarni & Sinha: [Random Forest Classifiers :A Survey and Future Research Directions](https://pdfs.semanticscholar.org/9ae3/ecab3dc662862ae4009baaf78290ce4e0199.pdf)
  - a systematic survey of work done on Random Forests
  - provides a taxonomy of  Random Forest classifiers, and a comparison chart of relevant parameters
  - relevant findings to consider:
    * accuracy: there is potential for improvement in accuracy by using different split measures and combining functions
    * performance: its found that performance can be improved by dynamically pruning a forest and estimating an 
      optimal subset of the forest
    * generalizations and/or extensions: there are avenues of research into streaming data, imbalanced data, and
      semi-supervised learning
  - their description of a RF: "Random Forest generates multiple decision trees; the randomization is present in 
    two ways: (1) random sampling of data for bootstrap samples as it is done in bagging and (2) random selection 
    of input features for generating individual base decision trees."
  - paths to improvement: "Strength of individual decision tree classifier and correlation among base trees are 
    key issues which decide generalization error of a Random Forest classifier." In other words, individual 
    trees should be fairly good on their own, and uncorrelated with each other (for the most part).
      * You may wonder, "What does correlation between trees actually mean? How is it defined? Are we talking
        regular ol' correlation?"  
      * I looked this up, and almost no one gives much more than a hand-wavy description (e.g., it just means
        the trees should look different).
      * However, I did find a resource that defines it: [Yi Yang (McGill University): RFs Ch15](http://www.math.mcgill.ca/yyang/resources/doc/randomforest.pdf)
      * Basically, if RF(x) = (1/N)Sum{T(i,x)}, and we consider the N tree to be i.i.d, all with
        variance s^2, then the average of the trees would have variance (1/N)s^2. However, the 
        trees are not i.i.d., they are better approximated as i.d. (identically distributed, but no
        independent). In this case, the RF variance is rs^2 + (1/N)(1-r)s^2, where r is the average
        pairwise correlation between trees.  Here we can shrink the 2nd term with more trees (larger N), 
        but the first term remains. 
  - "As per Breiman [11], Random Forest runs efficiently on large databases, can handle thousands of
    input variables without variable deletion, gives estimates of important variables, generates an 
    internal unbiased estimate of generalization error as forest growing progresses, has effective method 
    for estimating missing data and maintains accuracy when a large proportion of data are missing, and 
    has methods for balancing class error in class population unbalanced data sets."
  - parameters to tweak include: nTrees, mtry, minNodeSize, numNodes, etc
  - OOB: For each tree in the forest, the bootstrap sample excludes about 1/3rd of the original instances;
    this is the Out-of-Bag data for that tree.  This allows a forest to have its own self-validation set:
    each data point should be left out of about 1/3 of the trees, which can be used for validation.
  - Meta RF: "Meta Random Forest is based on the concept of using random forest themselves as base 
    classifiers for making ensembles, and the performance of this model is tested and compared with the 
    existing Random Forest algorithm. Meta Random Forests are generated by both bagging and boosting 
    approaches i.e. ensemble using Random Forest as base classifier with bagging approach, and ensemble
    using Random Forest as base classifier with boosting approach. Comparative study of both these techniques 
    and original Random Forest technique has shown that Bagged Random Forest gives the best results among the 
    three techniques."
  - Varying the split fcn by tree (no signif improvement): "Robnik and Sikonja (2004, Improving Random Forests) 
    experimented with Random Forest using five different attribute measures; each fifth of the trees in the forest 
    is generated using different split measure (Gini index, Gain ratio, MDL, ReliefF). This helped in decreasing 
    correlation between the trees while retaining their strengths. The performance increase observed was not 
    significant."
  - Varying the number of subsampled features per node (no signif improvement): Bernard et al 
    pitched Forest Rk, where k is the number of features selected at a node, which is randomized
    at each node.  They found that a Forest Rk is statistically equivalent a Random Forest, so
    do not consider k an important hyperparameter.
  - Weighted Voting Schemes (some improvement):  technique is called Dynamic Integration (Tsymbal et al); has
    something to do with optimizing trees to be good in local areas of the data space...
  - nTrees: "Theoretical and empirical results have proved that above a certain number of trees, adding more 
    trees in the forest does not improve accuracy (Bernard 2009, Trees in Random Forest)." 
  - Tree Removal (some improvement): You can find the worst performing trees and remove them.  One way to
    do this is to remove one tree at a time, then compute the change in accuracy; remove the worst 
    offender(s). Another way: reduce correlation among trees by computing all pairwise correlations; remove
    one from each pair of worst offender(s).
  - Imbalanced data --> Ensemble of RFs (improvement):  Basically, for imbalanced data, use RF as a base 
    learner.  For each RF, use stratified subsampling to produce a smaller, but bootstrap-like balanced subsample
    of the data.  (2004: Chain et al: Using Random forest to Learn Imbalanced Data.)
  - "By reviewing all work done related to performance improvement of Random forest there is still an important 
    issue which is unresolved is: what is the optimal number of base classifiers in Random Forest and how to select 
    the optimal subset without growing the entire forest."
  - Online RFs.. Doesn't seem too useful to me at the moment... But basically, it is an answer to when you
    have so much data streaming in that you cannot save (too much data), but you could save classifications
    made in real-time...  How do you build the RF on-the-spot w/o having historical data...?  (Something like 
    that.) "Internet traffic monitoring, Telecommunications billings, etc. produce huge amount of data and it 
    is practically impossible to store this real-time stream. Also it is not possible to have multiple passes 
    through this data."


* 2015: Biau & Scornet: [A Random Forest Guided Tour](https://arxiv.org/pdf/1511.05741.pdf)
  - Howard and Bowles (2012):  "Ensembles of decision trees (often known as 'random forests') have been the most 
    successful general-purpose algorithm in modern times."
  - Lin and Jeon (2006):  highlighted an interesting connection between random forests and a particular 
  class of nearest neighbor predictors
  - Scornet et al. (2015): show that Breiman’s (2001) forests are consistent in an additive regression framework.




* 2015: [Understanding Random Forests: from theory to practice](https://arxiv.org/pdf/1407.7502.pdf)


* Good notes:  http://www.math.mcgill.ca/yyang/resources/doc/randomforest.pdf

---------------------------------------------

# Time Series

* 2017: Tyralis & Papacharalampous: [Variable Selection in Time Series Forecasting Using Random Forests](https://www.mdpi.com/1999-4893/10/4/114)


--------------------------------------------

# Applications

### Bio / Healthcare
* 2006: Díaz-Uriarte & Andres: [Gene selection and classification of microarray data using random forest](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-7-3)
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

### Image Processing
* 2013: Fanelli et al: [Random Forests for Real Time 3D Face Analysis](http://doc.rero.ch/record/313914/files/11263_2012_Article_549.pdf)

# Land Surveying
* 2012: Immitzer et al: [Tree Species Classification with Random Forest Using Very High Spatial Resolution 8-Band WorldView-2 Satellite Data](https://www.researchgate.net/profile/Markus_Immitzer/publication/258710192_Tree_Species_Classification_with_Random_Forest_Using_Very_High_Spatial_Resolution_8-Band_WorldView-2_Satellite_Data/links/0f317532dc7018bcaa000000.pdf)
* 2013:  Millard & Richardson: [Wetland mapping with LiDAR derivatives, SAR polarimetric decompositions, and LiDAR–SAR fusion using a random forest classifier](https://www.tandfonline.com/doi/abs/10.5589/m13-038)
  - no pdf
* 2014: Beijma et al: [Random forest classification of salt marsh vegetation habitats using quad-polarimetric airborne SAR, elevation and optical RS data](https://www.researchgate.net/profile/Sybrand_Van_Beijma2/publication/262019604_Random_forest_classification_of_salt_marsh_vegetation_habitats_using_quad-polarimetric_airborne_SAR_elevation_and_optical_RS_data/links/5b8fd51992851c6b7ec0981d/Random-forest-classification-of-salt-marsh-vegetation-habitats-using-quad-polarimetric-airborne-SAR-elevation-and-optical-RS-data.pdf)
* 2015:  Millard & Richardson:  [On the Importance of Training Data Sample Selection in Random Forest Image Classification: A Case Study in Peatland Ecosystem Mapping](https://www.researchgate.net/profile/Koreen_Millard/publication/279852440_On_the_Importance_of_Training_Data_Sample_Selection_in_Random_Forest_Image_Classification_A_Case_Study_in_Peatland_Ecosystem_Mapping/links/559c15e508ae0035df23856a/On-the-Importance-of-Training-Data-Sample-Selection-in-Random-Forest-Image-Classification-A-Case-Study-in-Peatland-Ecosystem-Mapping.pdf)

--------------------------------------

# Implementation

In TensorFlow:  
* https://terrytangyuan.github.io/2016/08/06/tensorflow-not-just-deep-learning/
* https://github.com/aymericdamien/TensorFlow-Examples/blob/master/examples/2_BasicModels/random_forest.py
* https://www.kaggle.com/salekali/random-forest-classification-with-tensorflow

### Misc
TF also has Boosted Trees:  
* https://www.tensorflow.org/api_docs/python/tf/estimator/BoostedTreesClassifier
* https://medium.com/tensorflow/how-to-train-boosted-trees-models-in-tensorflow-ca8466a53127


---------------------------------

# Questions

# Multi-Random Random Forests?
Is there a name for a random forest that randomizes things even more than usual, e.g.:
* features are subsampled before even going to a tree (then nodes subsample features from the subsample)
* different constraints (e.g., a forest of shallow trees combined with a forest of terminal-purity 
  trees, combined with a forest that uses a different split function, etc)

Potential Yes: In one of the reviews above, I read about Meta RFs, which are bagged, boosted, or 
bagged-and-boosted RFs.  I say "potential" yes b/c from that description, e.g., it's not clear
they do any more than boostrap the data before passing it to a base RF.  

An actual yes (for split fcns): see Robnik and Sikonja (2004, Improving Random Forests).  That said,
their results showed very little improvement (similar to my own experience of just comparing forests
with different split functions, e.g., a "gini forest" vs an "entropy forest").

# How can I cut out the dead trees?
There exist quite a few methods to detect underperforming trees in a random forest, and has been
shown that removing some of them can increase the performance of the RF.  My question is: how do 
I do this with a sklearn forest?  In other words, is there a method included in the package, or
do I have write the code myself?  That's all... Want to look into it.  

One idea: rank the OOB errors from the trees and get rid the the worst ones.

Another idea: remove highly correlated trees.  This is more brute force pairwise 
comparisons...  

Another idea: remove a tree, compute change in accuracy; do for all trees; remove lowest performers. (2009: 
Zhang: Search for the smallest Random Forest.)

Many of these ideas have been tried out in several papers.  I've read that some of the constraints
you can put on a forest (e.g., minNodeSize, numNodes, etc) basically serve as a pre-pruning
strategy (i.e., pruning that occurs before pruning is necessary, making pruning relatively unnecessary).
