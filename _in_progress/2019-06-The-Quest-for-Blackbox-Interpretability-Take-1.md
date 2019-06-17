---
title: The Quest for Blackbox Interpretability (Take 1)
layout: post
tags: machine-learning random-forest predictive-modeling model-interpretability easi
---

So you created a random forest, and it seems like its making great predictions! But now your
boss wants to know why.  

This is where things can get tricky.  

The first stab at interpreting your random forest seems simple
engouh: the RF models in sklearn provide a ranked list of feature importances, right? They
do, and I've taken them seriously once upon a time.  It might be the case that
you take them seriously, or that you don't, but you print them out for your boss anyway.  


In this post 
I tell a cautionary tale about the "feature importances" of a random forest in scikit-learn.  This has
been talked about ad nauseum (e.g., [here] and [here]), yet people (including myself sometimes) will
take a look-see at the feature importances as if they mean something ... uh, important! 

Hard to say it better than said [here](https://explained.ai/rf-importance/):
> "Have you ever noticed that the feature importances provided by scikit-learn's Random Forests™ seem a 
> bit off, perhaps not jiving with your domain knowledge? We've got some bad news -— you can't always trust 
> them. It's time to revisit any business or marketing decisions you've made based upon the default feature 
> importances (e.g., which customer attributes are most predictive of sales)."


In follow-up posts, I will 
talk about LIME, Shapley numbers, and more -- and associated cautionary tales.

Let's get started!


# Dependence on Variable Representation
If you have
an important categorical variable that you've dummied down into 100 binary variables, good luck showing
that those categories are more important than some weak signal in a continuous variable, or that the
original categorical variable had immense explanatory power before getting castrated.  Imagine 
a categorical feature with 100 levels, which explains  90% of any
given prediction, and a few continuous variables that hold the remaining 10% (say 4 vars at 2.5% 
each).  Now imagine that 100-level 
feature is one-hotted into 100 binary variables, each taking an equal share of the predictive 
power (i.e., 1% of 90% each, or 0.9%).  All 4 continuous variables will now rank higher than
any one-hotted portion of the categorical variable.  Worse, some pure noise variables might 
out rank the dummies too, if any overfitting took place.  

When is this relevant?  Well, say the catvar is geographic region.  Your explanation to stakeholders should 
start by saying, "First of all, we found that geographic region has the strongest role in determining
the outcome of our prediction model."  But after one-hotting the variable, you might have all kinds
of weak signals (and potentially noise signals) come up first.  The story that follows from the feature 
importances in some sense is not wrong, but it's not exactly right -- at the least, it likely has lost
a lot of its intuitive power.

What can you do?

If you care about feature importance, then encoding a categorical variable with integers is 
a great way to keep everything together.  In my experience, some folks feel uncomfortable encoding
a nominal  variable as if it were an ordinal variable ("label encoding" in `sklearn` parlance), 
but it is perfectly reasonable in
a tree-based method like a random forest!  A tree is just looking for rules -- doesn't matter if the rule corresponds
to "Is this a cat?" or "Is this the number 3?"

This [article](https://medium.com/data-design/visiting-categorical-features-and-encoding-in-decision-trees-53400fa65931)
does an excellent job showing that not only is a numeric representation of categorical variable ok for a 
decision tree, but almost always a better choice than one-hot encoding in terms of model accuracy, computational time,
and more.  (In fact, they show that one-hotting is almost never an optimal representation in this scenario, but
instead recommend choosing a numeric representation for catvars with less than ~1k levels, and a binary encoding
for any catvar with more than ~1k levels).

You might be thinking: "Wtf?  If one-hot encoding is so bad, then why do I always hear about it?" It does seem
like one-hot encoding has a captive audience, and not for nothing: it works great for models like logistic regression
and neural networks.  Another [author finds](https://towardsdatascience.com/one-hot-encoding-is-making-your-tree-based-ensembles-worse-heres-why-d64b282b5769):

> "In general, one hot encoding provides better resolution of the data for the model and most models end up performing 
> better. It turns out this is not true for all models and to my surprise, random forest performed consistently worse for 
> datasets with high cardinality categorical variables."  

To be fair, this is why things like LASSO are used in logistic regression, or embeddings for neural networks.  At one
point, the one-hot representation of a high-cardinal categorical variable becomes a nuisance for any model (at least
when considering how much data you're willing to collect, or how long you're willing to wait for the model to train,
etc). Just so happens that tree-based methods seem to suffer from it the worst.  And it kills interpretability
in the feature importance rankings, so why use it? The author in the last article 
has an excellent example where he has a high cardinality categorical
feature which is the ranked as the most important in the model.  After one-hotting this variable, the dimensionality
of the model explodes and none of the variable's one-hot associates appears in the top 10 rankings.  Worse, even
the order of importance of unrelated variables has changed.  The authors says:

> "One-hot encoding categorical variables with high cardinality can cause inefficiency in tree-based 
> ensembles. Continuous variables will be given more importance than the dummy variables by the algorithm 
> which will obscure the order of feature importance resulting in poorer performance."

Admittedly, my own intuition misguides me: thinking about how trees simply look for rules, I would guess 
it doesn't matter whether that rule is encoded in one numerically-encoded variable or 1000 dummies...  Clearly
the latter case has a suboptimal feel to it: more variables probably means more trees and splits are
required, i.e., there is more paperwork to go through, but the answer is in there somewhere!  But here
is another article that addresses this:

> "Whatever you do, at least be aware that, despite theoretical results suggesting the expressive power of random 
> forests with one-hot encoded features is the same as for categorical variables, the practical results are 
> very different!"

This last article also addresses how the known most important features do not make it in the top
rankings of the sklearn RF feature importances (even has a section called "why one-hot encoding is bad bad bad for trees").

Anyway, major take away here is that "feature importances" in sklearn's RF have a certain arbitrariness
to them that is dependent on variable representations.  To keep some interpretability, do not one-hot encode
the categorical variable (but encode it numerically, if necessary).  A nice side effect here is that the model
should also perform better in this representation for low-cardinality catvars (`levels < 1k`).

# Dependence on Number of CatVar Levels
One potential setback though: [apparently](https://towardsdatascience.com/explaining-feature-importance-by-example-of-a-random-forest-d9166011959e), high-cardinality catvars also artificially outrank other variables.  

Honestly, this section is going to be short because I am not exactly sure where the "happy medium" exists.  This
has to do with choosing a representation that is right for your problem.  Sticking with the idea of a geographical
variable, like zipcode, you might find that it beats out variables that should be more important from a business
standpoint.  Breaking zipcode up into city and state variables might align things better with expectation...but 
this fudging can make one uneasy if the original intent is to understand feature importance.

If all of this
has made you uncomfortable with feature importances from sklearn's RF, then good.  

If not?  Well, you have been warned!

### Side note:  CART & Gini Importance
Getting more technical, the reason for a lot of this wacky behavior in the feature rankings of sklearn RFs is that 
(i) the CART algorithm is used to build the trees, and (ii) the Gini criterion is used for splitting.  

In this set up, feature importance is synonymous with "Gini importance."  (This is not always the case: there
exist other algorithms to determine/estimate variable importance, such as "permutation importance" or "entropy
importance".)

> The Gini importance "is based on the principle of impurity reduction that is followed in most traditional 
> classification tree algorithms. However, it has been shown to be biased when predictor variables vary in their 
> number of categories or scale of measurement, because the underlying Gini gain splitting criterion is a 
> biased estimator and can be affected by multiple testing effects."  (Source: [Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307))

The Classification and Regression Trees (CART) algorithm "creates a binary tree — each node has exactly two outgoing 
edges — finding the best numerical or categorical feature to split using an appropriate impurity 
criterion. For classification, Gini impurity or twoing criterion can be used. For regression, CART 
introduced variance reduction using least squares (mean square error)." ([source](https://medium.com/@srnghn/the-mathematics-of-decision-trees-random-forest-and-feature-importance-in-scikit-learn-and-spark-f2861df67e3)) 

### Some References


# Correlated Features
Another problem with feature importances is that there is some sort of arbitrariness when
highly correlated variables are in the feature set.  

> Random forest "variable importance measures have recently been suggested as screening tools 
> for, e.g., gene expression studies. However, these variable importance measures show a bias 
> towards correlated predictor variables."

> "[C]orrelations between predictor variables ... severely 
> affect the original random forest variable importance measures, because they can be considered as 
> measures of marginal importance, even though what is of interest in most applications is the conditional 
> effect of each variable."




# Noise & Overfitting
Try this on for size:

```python
import pandas as pd
import numpy as np
from sklearn.ensemble import RandomForestClassifier

# Potential predictors we have collected
#  -- in practice, we do not know if 1, 2, all, or none of these help predict y
#  -- in experiment, 
#         * we know that y directly depends only on x0 and x2
#         * we also know that x1 is fairly correlated with x1, so should hold some signal
#         * finally, we know that x3 has nothing to do with the outcome
x0 = np.random.choice([1,2,3,4,5,6,7,8,9,10],10000) 
x1 = 3*x0 + 15 + np.random.choice([0,1]) - np.random.choice([0,1])
x2 = np.random.choice([0,1],10000)
x3 = 3 * np.random.randn(10000) + 15
xdf = pd.DataFrame({'x0': x0, 'x1': x1, 'x2': x2, 'x3': x3})

# The Outcome Variable
#   -- pay attention to the fact that we do not even add any noise
#   -- this is just a thresholding problem; simple!
y = x0*x0 - 99*x2 > 0

# Note that y is close to being balanced
round(100 * y.sum()/len(y), 1)
    55.0

# Scenario 1
# We have collected data for x0, x1, x2, x3, and y
rf1 = RandomForestClassifier(
  random_state=2, 
  n_estimators=100, 
  max_depth=3, 
  min_samples_leaf=2
)       
rf1.fit(xdf,y)
xdf.columns[np.argsort(rf1.feature_importances_)]

# Scenario 2
# We collected data for x1, x2, x3, y
rf2 = RandomForestClassifier(
  random_state=2, 
  n_estimators=100, 
  max_depth=15, 
  min_samples_leaf=2
)       
rf2.fit(xdf.drop('x0', axis=1),y)
xdf.drop('x0', axis=1).columns[np.argsort(rf2.feature_importances_)]


# Scenario 3
# We collected data for x1, x3, y
rf3 = RandomForestClassifier(
  random_state=2, 
  n_estimators=100, 
  max_depth=15, 
  min_samples_leaf=2
)       
rf3.fit(xdf.drop(['x0','x2'], axis=1),y)
xdf.drop(['x0','x2'], axis=1).columns[np.argsort(rf3.feature_importances_)]

```


-----------------------------------------------------------

# Some References and Further Reading
* [How Not to Use a Random Forest](https://medium.com/turo-engineering/how-not-to-use-random-forest-265a19a68576)
* [Beware Default Random Forest Importances](https://explained.ai/rf-importance/)
* [Are categorical variables getting lost in your random forests?](https://roamanalytics.com/2016/10/28/are-categorical-variables-getting-lost-in-your-random-forests/)
* [One-Hot Encoding is making your Tree-Based Ensembles worse, here’s why](https://towardsdatascience.com/one-hot-encoding-is-making-your-tree-based-ensembles-worse-heres-why-d64b282b5769)
* [Visiting: Categorical Features and Encoding in Decision Trees](https://medium.com/data-design/visiting-categorical-features-and-encoding-in-decision-trees-53400fa65931)
* [Kaggle/Zillow Competition: Why does OHE give worse scores?](https://www.kaggle.com/c/zillow-prize-1/discussion/38793)
* [Explaining Feature Importance by example of a Random Forest](https://towardsdatascience.com/explaining-feature-importance-by-example-of-a-random-forest-d9166011959e)
* [Selecting good features – Part III: random forests](https://blog.datadive.net/selecting-good-features-part-iii-random-forests/)
* [Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307)
* [The Mathematics of Decision Trees, Random Forest and Feature Importance in Scikit-learn and Spark](https://medium.com/@srnghn/the-mathematics-of-decision-trees-random-forest-and-feature-importance-in-scikit-learn-and-spark-f2861df67e3)
* [Feature Importance Measures for Tree Models — Part I](https://medium.com/the-artificial-impostor/feature-importance-measures-for-tree-models-part-i-47f187c1a2c3)
