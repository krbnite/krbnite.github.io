---
title: The Quest for Blackbox Interpretability (Take 1)
layout: post
tags: machine-learning random-forest predictive-modeling model-interpretability easi
---

So you created a random forest, and it seems like its making great predictions well! But now your
boss wants to know why.  

Model interpretability: This is where things can get tricky.  

The first stab at it seems simple
engouh: the random forest models in sklearn provide a ranked list of feature importances, right? Sure,
and they might satisfy your boss...  Or, if they've been around the block, it might not. 


In this post 
I tell a cautionary tale about the "feature importances" of a random forest in scikit-learn.  This has
been talked about ad nauseum (e.g., [here] and [here]), yet people (including myself sometimes) will
print out the provided feature importances as if they mean something ... well, as if they 
mean someting important!  I know a lot of people
do this because (i) I talk to other data scientists, and (ii) I like to point out that one can't trust the 
feature importance rankings when someone other than me brings 'em up (kind of like in traffic when other
people weave in and out of lanes, they're assholes, but if I do it -- it's ok b/c reasons :-p).  

Hard to say it better than said [here](https://explained.ai/rf-importance/):
> "Have you ever noticed that the feature importances provided by scikit-learn's Random Forests™ seem a 
> bit off, perhaps not jiving with your domain knowledge? We've got some bad news -— you can't always trust 
> them. It's time to revisit any business or marketing decisions you've made based upon the default feature 
> importances (e.g., which customer attributes are most predictive of sales)."


In follow-up posts, I will 
talk about LIME, Shapley numbers, and more -- and associated cautionary tales.

Let's get started!


# Dependence on Variable Representation
One of the things I find unsatisfying is that this list can be deceptive.  If you have
an important categorical variable that you've dummied down into 100 binary variables, good luck showing
that those categories are more important than some weak signal in a continuous variable.  An intuitive
storyline here is this: Imagine a categorical feature with 100 levels, which explains  90% of any
given prediction, and a continuous variable that holds the remaining 10%.  Now imagine that 100-level 
feature is one-hotted into 100 binary variables, each taking an equal share of the predictive 
power (i.e., 1% of 90% each, or 0.9%).  The list of feature importances will not reflect that the 
original categorical variable held nearly all the predictive power...

Where is this relevant?  Say it is geographic region.  Your explanation to stakeholders should 
start by saying, "First of all, we found that geographic region has the strongest role in determining
the outcome of a prediction model."  But after one-hotting the variable, you might have all kinds
of weak signals (and potentially noise signals) come up first.  The story might not be wrong...but
still...isn't that a bit unsettling?

If you care about feature importance, then encoding a categorical variable with integers is 
a great way to keep everything together.  In my experience, some folks feel uncomfortable encoding
a nominal  variable as if it were an ordinal variable ("label encoding" in `sklearn` parlance), but it is perfectly reasonable in
a tree-based method like a random forest!  A tree is just looking for rules -- doesn't matter if the rule corresponds
to "Is this a cat?" or "Is this the number 3?"

This [article](https://medium.com/data-design/visiting-categorical-features-and-encoding-in-decision-trees-53400fa65931)
does an excellent job showing that not only is a numeric representation of categorical variable ok for a 
decision tree, but almost always a better choice than one-hot encoding in terms of model accuracy, computational time,
and more.  (In fact, they show that one-hotting is almost never an optimal representation in this scenario, but
instead recommend choosing a numeric representation for catvars with ~1k levels or less, and a binary encoding
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
etc). Just so happens that tree-based methods suffer the worse, possibly.  Importantly, as I mentioned above,
the one-hot approach reduces a random forest's interpretability in terms of the "feature importance" 
perspective.  The author in the last article has an excellent example where he has a high cardinality categorical
feature which is the ranked as the most important in the model.  After one-hotting this variable, the dimensionality
of the model explodes and none of the variable's one-hot associates appears in the top 10 rankings.  Worse, even
the order of importance of unrelated variables has changed.  The authors says:

> "One-hot encoding categorical variables with high cardinality can cause inefficiency in tree-based 
> ensembles. Continuous variables will be given more importance than the dummy variables by the algorithm 
> which will obscure the order of feature importance resulting in poorer performance."

Admittedly, my own intuition misguides me: thinking about how trees simply look for rules, to my mind
it doesn't matter if that rule is encoded in one numerically-encoded variable or 1000 dummies...  Clearly
the latter case has a suboptimal feel to it: more variables probably means more trees and splits are
required, i.e., there is more paperwork to go through, but the answer is in there somewhere!  But here
is another article that addresses this:

> "Whatever you do, at least be aware that, despite theoretical results suggesting the expressive power of random 
> forests with one-hot encoded features is the same as for categorical variables, the practical results are 
> very different!"

This last article also addresses how the known most important features do not make it in the top
rankings of the sklearn RF feature importances (even has a section called "why one-hot encoding is bad bad bad for trees").

Anyway, major take away here is that "feature importances" in sklearn RFs have a certain arbitrariness
to them that is dependent on variable representations.  (You have been warned!)

### Some References
* [Are categorical variables getting lost in your random forests?](https://roamanalytics.com/2016/10/28/are-categorical-variables-getting-lost-in-your-random-forests/)
* [One-Hot Encoding is making your Tree-Based Ensembles worse, here’s why](https://towardsdatascience.com/one-hot-encoding-is-making-your-tree-based-ensembles-worse-heres-why-d64b282b5769)
* [Visiting: Categorical Features and Encoding in Decision Trees](https://medium.com/data-design/visiting-categorical-features-and-encoding-in-decision-trees-53400fa65931)
* [Kaggle/Zillow Competition: Why does OHE give worse scores?](https://www.kaggle.com/c/zillow-prize-1/discussion/38793)

# Correlated Features
Another problem with feature importances is that there is some sort of arbitrariness when
highly correlated variables are in the feature set.  


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

Some References and Further Reading
* [How Not to Use a Random Forest](https://medium.com/turo-engineering/how-not-to-use-random-forest-265a19a68576)
* [Beware Default Random Forest Importances](https://explained.ai/rf-importance/)

