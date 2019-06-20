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

I also thought this [article by SydneyF from the Alteryx community](https://community.alteryx.com/t5/Alteryx-Knowledge-Base/Seeing-the-Forest-for-the-Trees-An-Introduction-to-Random-Forest/ta-p/158062) had a very nice breakdown as well -- also worth saving!

> "Creating and then combining the results of a bunch of decisions trees seems pretty basic, however, simply creating multiple trees out of the exact same training data wouldn’t be productive – it would result in a series of strongly correlated trees. All of these trees would sort data in the same way, so there would be no advantage to this method over a single decision tree. This is where the fancy part of random forests starts to come into play. To decorrelate the trees that make up a random forest, a process called bootstrap aggregating (also known as bagging) is conducted. Bagging generates new training data sets from an original data set by sampling the original training data with replacement (bootstrapping). This is repeated for as many decision trees that that will make up the random forest. Each individual bootstrapped data set is then used to construct a tree. This process effectively decreases the variance (error introduced by random noise in the training data, i.e., overfitting) of the model without increasing the bias (underfitting). On its own, bagging the training data to generate multiple trees creates what is known as a bagged trees model."

> "A similar process called the random subspace method (also called attribute bagging or feature bagging) is also implemented to create a random forest model. For each tree, a subset of the possible predictor variables is sampled, resulting in a smaller set of predictor variables to select from for each tree. This further decorrelates the trees by preventing dominant predictor variables from being the first or only variables selected to create splits in each of the individual decision trees. Without implementing the random subspace method, there is a risk that one or two dominant predictor variables would consistently be selected as the first splitting variable for each decision tree, and the resulting trees would be highly correlated. The combination of bagging and the random subspace method result in a random forest model."

> "The aggregating part of bootstrap aggregating comes from combining the predictions of each of these decision trees to determine an overall model prediction. The output of the overall model is then the mode of classes (classification) or the mean prediction (regression) of all the predictions of the individual trees, for each individual record."


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

Yes! 
* 2001: Lipovetsky & Conklin: [Analysis of regression in game theory approach](https://onlinelibrary.wiley.com/doi/abs/10.1002/asmb.446)
    - Covers Shapley values in multiple regressions suffering from multicollinearity (coefficients are biased)
* 2007: Strobl et al: [Bias in random forest variable importance measures: Illustrations, sources and a solution](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-8-25)
* 2008: Strobl et al [Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307)
    - Introduces the conditional permutation importance, which does not give higher importantance to correlated predictors
    - Note: the so-called "weakness" of regular ol' permutation importance is not a weakness, if for example, you want
      to highly weight predictors that are correlated with a known predictor; this is a design question ("What do
      I consider important?")
* 2010: Altmann et al: [Permutation importance: a corrected feature importance measure](https://academic.oup.com/bioinformatics/article/26/10/1340/193348)
    - They call this "permutation importance", but it seems to be a different method than what is 
      usually referred to as permutation importance (haven't read this one yet, so not sure)
* 2013: Janitza et al: [An AUC-based permutation variable importance measure for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-119)
* 2013: Hapfelmeier et al: [A new variable importance measure for random forests
with missing data](http://www.psychologie.uzh.ch/methpsy/publications/STCO_20120809.pdf)
    - This one caught my eye b/c it made me distinguish between feature importance for prediction vs inference
    - In prediction: if I assume the data generating process will remain the same, then missing values
      hold information in and of themselves, and the associated feature importance reflect what I care about.
    - In Inference:  if the missing values are an inconvenience (that is, something we wanted to measure, but
      couldn't and didn't for some reason), then the feature importances do not necessarily rank or measure
      what we want.  In this case, one might want to use multiple imputation before running the data through
      the random forest -- or, the method proposed in this paper.
    - For now, I personally care about "feature importance" as it relates to prediction, but can foresee
      needing to care about how it relates to inference in the near future (that is, in the data I look at,
      we definitely have missing values that we would have, should have, could have measured, but didn't
      due to some inconvenience or another -- and though we consider prediction very important, we also care
      about explanatory power that is invariant to missing values, if not cause-and-effect)
* 2016: Ribeiro et al: ["Why Should I Trust You?" Explaining the Predictions of Any Classifier](https://arxiv.org/pdf/1602.04938.pdf?mod=article_inline)
* 2016: Lundberg & Lee: [An unexpected unity among methods for interpreting model predictions](https://arxiv.org/pdf/1611.07478.pdf)
    - Covers expectation Shapley (ES) values, which unify various interpretative methods, such as LIME and DeepLift
* [Learning Important Features Through Propagating Activation Differences](https://arxiv.org/pdf/1704.02685;Learning)
    - Covers DeepLift

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

### Gini Importance (aka Mean Decrease in Impurity)
Note that for random forests in scikit-learn, 
(i) the CART algorithm is used to build the trees, and (ii) the Gini criterion is used for splitting.  The 
Classification and Regression Trees (CART) algorithm "creates a binary tree — each node has exactly two outgoing 
edges — finding the best numerical or categorical feature to split using an appropriate impurity 
criterion. For classification, Gini impurity or twoing criterion can be used. For regression, CART 
introduced variance reduction using least squares (mean square error)." ([source](https://medium.com/@srnghn/the-mathematics-of-decision-trees-random-forest-and-feature-importance-in-scikit-learn-and-spark-f2861df67e3)) 

The splitting criterion doesn't necessarily dictate the feature importance algorithm, though it obviously affects
the outcome in one way or another.  Specifically, one can use the Gini criterion for splitting, but choose
not to use the Gini importance for ranking feature importances.  (Having said that, the Gini importance
itself is strictly associated with the Gini splitting criterio.  So, e.g., if you use the `entropy` criterion
in scikit learn, a similar algorithm can be used to compute the importances, but I'm not sure you would
call them Gini importances at that point.)

Here, we will talk about the Gini importance.

The most important thing to understand is that "feature importance" is not synonymous with "Gini importance."  That is,
despite random forests in scikit-learn having a ranked list referred to as `rf.feature_importance_`, the way this
list is created is but one way to assess the importances of the input features -- and not a great way either. There
exist other algorithms to determine/estimate variable importance, such as "permutation importance" (aka "mean 
decrease in accuracy"), which are implemented in complementary packages to scikit-learn, such as `rfpimp`.  

> The Gini importance "is based on the principle of impurity reduction that is followed in most traditional 
> classification tree algorithms. However, it has been shown to be biased when predictor variables vary in their 
> number of categories or scale of measurement, because the underlying Gini gain splitting criterion is a 
> biased estimator and can be affected by multiple testing effects."  (Source: [Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307))



Scikit-learn's defense of the Gini importance can be found in its documentation.  Not so sure it is a great
defense all things considered, but worth recording here:
> "The relative rank (i.e. depth) of a feature used as a decision node in a tree can be used to assess the relative 
> importance of that feature with respect to the predictability of the target variable. Features used at the top of the tree
> contribute to the final prediction decision of a larger fraction of the input samples. The expected fraction of 
> the samples they contribute to can thus be used as an estimate of the relative importance of the features. In 
> scikit-learn, the fraction of samples a feature contributes to is combined with the decrease in impurity from 
> splitting them to create a normalized estimate of the predictive power of that feature."
>
> "By averaging the estimates of predictive ability over several randomized trees one can reduce the variance 
> of such an estimate and use it for feature selection. This is known as the mean decrease in impurity, or MDI."


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
is [another article](https://roamanalytics.com/2016/10/28/are-categorical-variables-getting-lost-in-your-random-forests/)
that addresses this:

> "Whatever you do, at least be aware that, despite theoretical results suggesting the expressive power of random 
> forests with one-hot encoded features is the same as for categorical variables, the practical results are 
> very different!"

This last article also addresses how a high-cardinality catvar in the H20 framework is assigned 70% of 
the importance, while its one-hot encoded components in the sklearn framework do not even accumulate
10% of importance (even has a section called "why one-hot encoding is bad bad bad for 
trees").  One thing that I did not understand about the article is the line from the TLDR, "Our primary comparison 
is between H2O (which honors categorical variables) and scikit-learn (which requires them to be one-hot 
encoded)."  There is nothing stopping you from numerically encoding categorical variables (e.g., with integers)
and using them in a scikit-learn random forest.  That said, I do see many blogs where folks one-hot encode
anything and everything by default, so the article serves as a great read for those inclined to one-hot their
catvars.

Anyway, major take away here is that (Gini) feature importances in scikit-learn's RF have a certain arbitrariness
to them that is dependent on variable representations.  If your only option was Gini importance, then to keep
the "inherent" Gini importances, do not one-hot encode
the categorical variable (though encode it numerically, if not already).  A nice side effect here is that the model
should also perform better in this representation for low-cardinality catvars (`levels < 1k`).

# Dependence on Number of CatVar Levels
One potential setback though: [for Gini importances](https://towardsdatascience.com/explaining-feature-importance-by-example-of-a-random-forest-d9166011959e), high-cardinality catvars can artificially outrank lower-cardinality
categorical variables.  

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
in the data "as is".  Fortunately, there do appear to be feature importance measures that do not accidentally
bias towards high-cardinality categorical variables (e.g., conditional permutation importance).


# Some Explanation
Turns out that the two sections above are two sides of the same coin: Gini importance biases
towards continuous and/or high-cardinality categorical variables.  

The author of "[Be Aware of Bias in RF Variable Importance Metrics](https://blog.methodsconsultants.com/posts/be-aware-of-bias-in-rf-variable-importance-metrics/)" offers a nice, succinct explanation.

Gini importance is biased because:
> "Each time a break point is selected in a variable, every level of the variable is tested to find the best 
>  break point. Continuous or high cardinality variables will have many more split points, which results in 
>  the 'multiple testing' problem. That is, there is a higher probability that by chance that variable happens 
>  to predict the outcome well, since variables where more splits are tried will appear more often in the tree."

The author then goes over permuation importance and conditional permutation importance, which solve some 
of the issues with Gini importance.  At the end of the article, he provides a nice set of rules for when
to use each type of importance (especially when computational time or resources are limited).  If using
continuous variables that are not correlated, then Gini importance is ok.  Similarly, if using 
only categorical variables that are not correlated with each other, the Gini importance is ok, but only
if those catvars have the same number of levels (or at least, nearly so). 

Once you have categorical variables with vastly different cardinalities, or once you introduce a continuous
variable, then all bets are off: do not use the Gini importance!  

On this last note, after running a bunch of simulations myself, I would have to wholeheartedly agree.  Just by
introducing a random, continuous variable that had nothing to do with my target variable, the Gini importances
immedidately became suspect: seemed no matter how many parameters I tweaked, the noise variable ranked in
first or second place every time!

Final note: given the various articles showing that one-hot encoding also diminishes model accuracy 
(e.g., [this](https://roamanalytics.com/2016/10/28/are-categorical-variables-getting-lost-in-your-random-forests/)),
I would bias towards keeping high-cardinality categorical variables, but using a better measure of
feature importance.

# Design Flaws: Noise & Overfitting
At least one of the reasons that a very high-cardinality catvar can appear to have more
importance attributed to it than seems reasonable is due to overfitting.  That is, if you are
not ensuring that the random forest has enough restrictions on it (by changing the defaults on
parameters like `max_depth`, `min_samples_leaf`, etc), then it turns out that something like 
zipcode can proxy as a semi-unique ID in the data records.  In highly populated zipcodes, this
obviously is unlikely to be the case, but when you consider more rarefied regions, it becomes possible
to only have several customers per zipcode.  Depends on the size of your customer base, or
number of patients in your trial, etc.

In the same way, a pure noise variable having no correlation with the target can serve as
an ID variable: the forest can overfit to the noise and decide that the noise is more important
than it is (say on a holdout set).

In "[How not to use random forest](https://medium.com/turo-engineering/how-not-to-use-random-forest-265a19a68576), the 
author depicts a simulation that has an target-relevant binary variable, a target-relevant continuous
variable (Poisson distributed), a random noise binary variable, and a random noise continuous variable (normally 
distributed).  His intent is to show that using scikit-learn's random forest with default
parameters runs folks into trouble, especially when one cares about feature importance.  The noisy continuous
variable is ultimately ranked as most important.  This should send shivers down your spine: in this case,
we know what variable is just noise, so we know the feature importances are somehow bogus, but in the workplace
setting, you cannot be so sure what is noise and what is signal (that is, without further investigation, etc).

I've run my own simulations, similar to this article, and have come up with the same: the noisy continuous
variables always rise to the top!  Very disheartening.  The author in the aforementioned article attributes
this behavior to both using scikit-learn's default feature importance (Gini importance), default hyperparameters 
(e.g., `max_depth=None, max_leaf_nodes=None, min_samples_leaf=1, min_samples_split=2`), and the ambiguities that
can arise (especially when using the Gini importance) when mixing continuous and categorical variables.  This is 
what allows
a continuous noise variable, where each element is likely to be unique in the column, to serve as an ID for
each record in the data set.  That said, I experimented with tweaking the hyperparameters quite a bit and
was still amazed/horrified to see that the noise variable floated to the top of feature importances.

Question: Should feature importances be defined on the training data?  Probably not, but it appears they are.  So
what can you do?

Solution: At minimum, look at the out-of-bag (OOB) error.  If it sucks, then there is no reason to trust
your feature importances.  If you also have an out-of-hand validation set not used in training, then you
can use that in the same manner.  

Take Away: Feature importances are only worthwhile if the associated model serves as a worthwhile 
prediction model.  This is an issue of generalizability: do not overfit the model.


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

The article, "[Selecting good features – Part III: random forests](http://blog.datadive.net/selecting-good-features-part-iii-random-forests/)," very nicely shows how correlated features impact the interpretaction of the
Gini importance rankings.  For example, simulating a model where 3 correlated features contribute about the
same level of importance to the response, the author shows their estimated importance to be all over the place (e.g.,
one var is shown to be 10x more important than another, despite them having equal importance in reality).

That said, permutation importance is also known to not deal well with correlated predictors either, but for
a different reason (will talk more about this in an upcoming article).  Generally, permutation importance
does work better though -- if you have to choose GImp or PImp.

Final note:  conditional permutation importance > permutation importance >> Gini importance.

Next!


# Dependence on Random Seed / Number of Trees
Another thing to worry about is how tightly your results are coupled to the chosen
random seed parameter...  That is, if you change the random seed, how drastically do your
feature importances change?  Solution: Add more trees until there is little-to-no change.

I leave you with these quotes...

From ["Conditional variable importance for random forests"](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2491635/)
> "[I]t should also be noted that any interpretation of random forest variable importance scores can only be 
> sensible when the number of trees is chosen sufficiently large such that the results produced with different 
> random seeds do not vary systematically. Only then it is assured that the differences between, e.g., unconditional 
> and conditional importance are not only due to random variation."

From ["Be Aware of Bias in RF Variable Importance Metrics"](https://blog.methodsconsultants.com/posts/be-aware-of-bias-in-rf-variable-importance-metrics/):
> "Note: Always check whether you get the same results with a different random seed before interpreting 
>  any importance rankings. If the results change, increase the number of trees with ntree."


# Last Words

If all of this
has made you uncomfortable with feature importances from scikit-learns's random forests, then good.  If 
not?  Well, you have been warned!

Ultimately, we want to provide explanatory power when using random forests.  The major two points of this
article are: (i) this is possible, but (ii) not out-of-the-box in the scikit-learn version of random forests. 

Though to my mind, it seems like one should never use the Gini importance, there apparently circumstances
when it is ok.  [This author](https://blog.methodsconsultants.com/posts/be-aware-of-bias-in-rf-variable-importance-metrics/) 
reasons that the Gini importance is just fine (and desirable in terms of computation time) under either of 
two conditions:
* all predictors are continuous and not correlated with each other
* all predictors are categorical, have the same number of levels, and are not correlated with each other

Don't know about you, but I'm thinking those make for some pretty stringent, potentially artificial 
scenarios.  But good to know nonetheless. 

In the next installment, I'll be talking about more robust feature importance measures, namely
permutation importance and conditional permutation importance.

-----------------------------------------------------------

# Some References and Further Reading
* [How Not to Use a Random Forest](https://medium.com/turo-engineering/how-not-to-use-random-forest-265a19a68576)
* [Beware Default Random Forest Importances](https://explained.ai/rf-importance/)
* [Are categorical variables getting lost in your random forests?](https://roamanalytics.com/2016/10/28/are-categorical-variables-getting-lost-in-your-random-forests/)
* [One-Hot Encoding is making your Tree-Based Ensembles worse, here’s why](https://towardsdatascience.com/one-hot-encoding-is-making-your-tree-based-ensembles-worse-heres-why-d64b282b5769)
* [Visiting: Categorical Features and Encoding in Decision Trees](https://medium.com/data-design/visiting-categorical-features-and-encoding-in-decision-trees-53400fa65931)
* [Kaggle/Zillow Competition: Why does OHE give worse scores?](https://www.kaggle.com/c/zillow-prize-1/discussion/38793)
* [Explaining Feature Importance by example of a Random Forest](https://towardsdatascience.com/explaining-feature-importance-by-example-of-a-random-forest-d9166011959e)
* [Be Aware of Bias in RF Variable Importance Metrics](https://blog.methodsconsultants.com/posts/be-aware-of-bias-in-rf-variable-importance-metrics/)
* [Selecting good features – Part III: random forests](https://blog.datadive.net/selecting-good-features-part-iii-random-forests/)
* [Conditional variable importance for random forests](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-9-307)
* [The Mathematics of Decision Trees, Random Forest and Feature Importance in Scikit-learn and Spark](https://medium.com/@srnghn/the-mathematics-of-decision-trees-random-forest-and-feature-importance-in-scikit-learn-and-spark-f2861df67e3)
* [Feature Importance Measures for Tree Models — Part I](https://medium.com/the-artificial-impostor/feature-importance-measures-for-tree-models-part-i-47f187c1a2c3)
* [Cornell CS4780: Lecture 31 "Random Forests / Bagging"](https://www.youtube.com/watch?v=4EOCQJgqAOY)
* [Seeing the Forest for the Trees: An Introduction to Random Forest](https://community.alteryx.com/t5/Alteryx-Knowledge-Base/Seeing-the-Forest-for-the-Trees-An-Introduction-to-Random-Forest/ta-p/158062)


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
