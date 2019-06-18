---
title: The Quest for Blackbox Interpretability (Take 1)
layout: post
tags: machine-learning random-forest predictive-modeling model-interpretability easi
---

So you created a random forest, and it seems like its making great predictions! But now your
boss wants to know why.  This is where things can get tricky.  

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

# Random Forests (the Gist)
I wasn't going to include a part describing what a Random Forest is, but then I read through 
[this paper](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307) and liked
their description so much I wanted to save it somewhere.  It serves as a nice recap of these models
and gives the sections below some more context.

> "In random forests and the related method bagging, an ensemble of classification trees is created by means of drawing several bootstrap samples or subsamples from the original training data and fitting a single classification tree to each sample. Due to the random variation in the samples and the instability of the single classification trees, the ensemble will consist of a diverse set of trees. For prediction, a vote (or average) over the predictions of the single trees is used and has been shown to highly outperform the single trees: By combining the prediction of a diverse set of trees, bagging utilizes the fact that classification trees are instable but on average produce the right prediction. This understanding has been supported by several empirical studies and especially the theoretical results of Bühlmann and Yu, who could show that the improvement in the prediction accuracy of ensembles is achieved by means of smoothing the hard cut decision boundaries created by splitting in single classification trees, which in return reduces the variance of the prediction."

> "In random forests, another source of diversity is introduced when the set of predictor variables to select from is randomly restricted in each split, producing even more diverse trees. In addition to the smoothing of hard decision boundaries, the random selection of splitting variables in random forests allows predictor variables that were otherwise outplayed by their competitors to enter the ensemble. Even though these variables may not be optimal with respect to the current split, their selection may reveal interaction effects with other variables that otherwise would have been missed and thus work towards the global optimality of the ensemble."

In the original random forest, each tree grew indefinitely until each leaf had 1-2 data points.  In modern implementations,
this is often a default, but is something that you can tweak.  For example, you can choose a `max_depth`, which limits
how many splits can occur along a single pathway through the tree.  Or you might play with `min_samples_leaf`, which
hedges against overfitting to a noisy variable.  The number of features randomly selected at a split is also something
you can tune.  Often, given f features, the default is split on k randomly selected features, where is k < f and 
usually defaulted to k = sqrt(f).  This default is considered good enough for most cases (e.g.,
here is a great [video lecture](https://www.youtube.com/watch?v=4EOCQJgqAOY) on random forests), but 
you might find you can squeeze a bit more optimization out of your model by playing around in the neighborhood
of this default.  


That said, technically, growing out a huge forest should hedge against
overfitting too.  Quite possibly better, or not as well.  So how to choose?  Fortuantely, 
using skearn's `RandomSearch` or `GridSearch`, you can optimize these hyperparameters.  You might also have runtime 
requirements that help guide appropriate design decisions.




# Why Random Forests?
In upcoming articles, I will talk about things like Shapley values and LIME, which are model agnostic.  However,
I begin with random forests because, as far as predictive models are concerned, these almost work perfectly
right out of the box!  Moreover, random forests compete with neural networks for "best black box" in terms of performance,
depending on data set (e.g., Kagglers are never quite raving about neural networks, while computer vision folk
are rarely heard talking about "recurrent convolutional random forests").  

The out-of-the-box feature is most important though.  Oftentimes, you can run a baseline random forest on
a data set without no clean-up, scaling, or normalization.  This out-of-the-boxness scares some people -- those
who have spent years concerend with inference, proper imputation, taking logarithms, using Box-Cox and so on!  Even spooky
to the unspookable: random forests technically do not even require a validation set!  ("Say whaaat?!") It
can feel a little funny to (i) not care about any of that, and (ii) expect to have any understanding of how 
the many predictors affect the outcome.  But this is precisely what random forests offer!  If you suffer
from random forest fear, then I guarantee that getting your hands dirty with this technique will quickly begin
to allay your anxiety.  

Often, we pit predictive power versus explanatory power, but I think there has been enough research into
tree-based methods at this point to make that tradeoff less sensitive and severe.  Moreover, there has been
enough research to show that what we often consider to be "explanatory power" isn't as explanatory as 
we would like to think (e.g., regression coefficients) and some go as far as advising that the explanatory
methods for black box models be applied to models that are often not considered as such (namely, things like linear and
logistics regression).

"Do you have citations to back any of this up?"

Yes! But not on hand.  

Anyway, we will start with random forests as our black box model, and look at the out-of-the-box
explanatory method in the scikit-learn method, which is known as "Gini importance."  This might not be
a great place to start in terms of providing a black box model with "explanatory power," since it has
been shown to be a poor explanatory tool.  However, it is a great place to start in terms of
drawing a line between what parts of random forests work out of the box (e.g., training them) and 
what does not (e.g., the default "feature imporatance" rankings provided in the scikit-learn implementation).

In the remainder of this post, things will get a bit bleak.  What I'm trying to do is decide on my own
path to model building, while maintaining useful explanatory power... These are kind of a mish-mash, brainstorm
of lessons learned first- and second-hand.  We find that "explanatory power" is a bit hard to define, especially
for categorical variables, which can often take on any number of arbitrary representations at training time.  Personally,
my conclusion is that there is a bit of craftsmanship and artistry that must go into this: any given 
representation will not necessarily induce a false picture (in terms of feature importance), but
might provide a perspective that does not reflect one's intuition at first or second glance.  

You'll see what I mean...  Read on!

-------------------------------------

# Dependence on Variable Representation
If you have
an important categorical variable that you've dummied down into 100 binary variables, good luck showing
that those categories are more important than some weak signal in a continuous variable, or that the
original categorical variable had immense explanatory power before getting castrated.  

Imagine 
a categorical feature with 100 levels, which explains  90% of any
given prediction, and a few continuous variables that hold the remaining 10% (say 4 vars at 2.5% 
each).  Now picture that the 100-level 
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
etc). Just so happens that tree-based methods seem to suffer from it the worst.  And it can kill interpretability
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

In the last section, we assumed the feature importance of the high-cardinality categorical variable was true and 
that the one-hot representation obscured this truth.  But the default feature importance algorithm in scikit-learn's
RFs generally bias towards high-cardinality categorical variables.  

I guess this can hurt in a situation where you have a binary variable that is very important and a 
high-cardinality variable where each level is not all too important.  The storyline you might expect
from your data is that the binary variable is ranked higher in importance, but instead you might find
the high-cardinality catvar on top.  

I think this is less about the feature importance algorithm, though, and more about
choosing a representation that is right for your problem.  

Sticking with the idea of a geographical
variable, like zipcode, you might find that it beats out variables that should be more important from a business
standpoint.  Breaking zipcode up into city and state variables might align things better with expectation.

That all said: this fudging can make one uneasy if the original intent is to understand feature importance
in the data "as is".  To this, all I can say is -- toughen up!  :-p


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


# Ambiguity from Correlated Features
Another problem with feature importances is that there is some sort of arbitrariness when
highly correlated variables are in the feature set.  

> Random forest "variable importance measures have recently been suggested as screening tools 
> for, e.g., gene expression studies. However, these variable importance measures show a bias 
> towards correlated predictor variables."

> "[C]orrelations between predictor variables ... severely 
> affect the original random forest variable importance measures, because they can be considered as 
> measures of marginal importance, even though what is of interest in most applications is the conditional 
> effect of each variable."

Often, I've seen "permutation importance" as the answer to the problems that arise in "Gini importance," but
[these authors](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307) show that 
even permutation importance biases towards correlated predictors

Take away:  conditional permutation importance > permutation importance >> Gini importance.


# Design Flaws: Noise & Overfitting
At least one of the reasons that a very high-cardinality catvar can appear to have more
importance attributed to it than seems reasonable is due to overfitting.  That is, if you are
not ensuring that the random forest has enough restrictions on it (by changing the defaults on
parameters like `max_depth`, `min_samples_leaf`, etc), then it turns out that something like 
zipcode can proxy as a semi-unique ID in the data records.  In highly populated zipcodes, this
obviously is unlikely to be the case, but when you consider more rarefied regions, it becomes possible
to only have several customers per zipcode.  

In the same way, a pure noise variable having no correlation with the target can serve as
an ID variable: the forest can overfit to the noise and decide that the noise is more important
than it is (say on a holdout set).




# Dependence on Random Seed (?!)
Another thing to worry about is how tightly your results are coupled to the chosen
random seed parameter...  I leave you with this quote:

> "[I]t should also be noted that any interpretation of random forest variable importance scores can only be 
> sensible when the number of trees is chosen sufficiently large such that the results produced with different 
> random seeds do not vary systematically. Only then it is assured that the differences between, e.g., unconditional 
> and conditional importance are not only due to random variation."

# Last Words
Ultimately, we want to provide explanatory power when using Random Forests.  The major two points of this
article are: (i) this is possible, but (ii) not out-of-the-box in the scikit-learn version of random forests. 

If all of this
has made you uncomfortable with feature importances from sklearn's RF, then good.  

If not?  Well, you have been warned!



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
* [Cornell CS4780: Lecture 31 "Random Forests / Bagging"](https://www.youtube.com/watch?v=4EOCQJgqAOY)

-------------------------------------------------------------------------------

# Next Up
These notes were mostly focused on the shortcomings of Gini importance, which is the default feature ranking
algorithm for random forests in scikit-learn.  In upcoming write-ups, I want to focus a bit more on:
* permutation importance
    - this [article](https://towardsdatascience.com/explaining-feature-importance-by-example-of-a-random-forest-d9166011959e) has a good description
* conditional permutation importance
* the `treeinterpreter` package in Python
    - e.g., [RF Interpretation - Conditional Feature Contributions](http://blog.datadive.net/random-forest-interpretation-conditional-feature-contributions/)
* LIME
* Shapley Values
