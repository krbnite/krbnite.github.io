---
title: Missing Values in a Live Prediction Model (Take 2)
layout: post
tags: prediction machine-learning deployment easi
---

Ok, so it's been about a week of reading, thinking, toying around...  My original objective was
looking into various ways to treat missing values in categorical variables with an eye towards
deploying the final predictive model.  Reading over several ideas that included continuous
variables (e.g., Perlich's missing indicator and clipping techniques), I've re-scoped it a bit
to missing values in general, specifically for predictive models.

I found, if you look hard enough, people definitely have thought about prediction models in practice.  Not
that I assumed this wasn't the case.  It was hard to find though.

What surprised me is how much generic advice about CCA, mean 
imputation, etc, is given on so many various StackExchange and Quora posts, and various blogs.  I read
so many bits where the writer wouldn't even bat an eye when recommending CCA for dealing with
missing data when developing a prediction model... Like, when the fuck does the writer think the
prediction model is going to be used?  On a castrated, CCA'd-ass test set?   Hardly!

Anyway, I didn't want to distort my first article and continue updating it... It needed to be journaled, and
now updated in the sequel.  I specifically sought out a bunch of articles today where the authors very
much keep in mind the fact that someone, somewhere ultimately will want to use the predictive model
in an environment with a data generating process similar to the one that produced the original data
set -- missing values and all!

For context,  my original article was motivated out of curiosity: I was working with some data and my
approach was to treat missing values as in a nominal categorical variable as another level.  But I questioned
whether or not this was really a great approach... I wanted to see what others had done.  I kept
reading about listwise-deletion and CCA, and it made my eyes cross:  why are so many people recommending
these things when talking about predictive models?  There was a particular Quora question where the
first several authors gave the standard advice, and then came Claudia Perlich's answer: yes, she was
actually answering the question that was asked!!!!!!!!

Below, I just include a list of the references I've found, sometimes with a note or two.  Over time,
I hope to continue this series of articles by providing some summaries of these references.


## Side Note: Multiple Imputation
As I found in previous articles, various implementations of multiple imputation seem to be
considered "best in class" -- but, if you read more carefully, this is specifically geared to
non-predictive settings, like statistical inference, unbiased population estimates, causal modeling,
etc.  

It is not obvious or clear that this "best in class" title for imputation carries over to the 
predictive setting.  For example, multiple imputation creates multiple "artificial" records
for each missing value, ultimately creating a data set that preserves the variance of its
variables (as opposed to someting like median imputation, which does not).  

But when would we want to create multiple records in prediction?  Maybe to generate multiple
prediction then aggregate (e.g., take the mean).  

Seems like R package MICE for predictive mean matching gets a lot of attention and callouts:
  - e.g., https://statistical-programming.com/predictive-mean-matching-imputation-method/
  - eBook: https://stefvanbuuren.name/fimd/sec-pmm.html

So given that combining an imputation method with missing indication often works best for prediction, I 
want to see if "MICE + indication" is the best in that class.  


  

--------------------------------------------------------

# Reading List
### Missing Value Misc
* 2007: [Handling Missing Values when Applying Classification Models](http://jmlr.csail.mit.edu/papers/volume8/saar-tsechansky07a/saar-tsechansky07a.pdf)
* 2009: Janssen et al: [Dealing with Missing Predictor Values When Applying Clinical Prediction Models](https://pdfs.semanticscholar.org/0c67/97f88f12edef205a314188f936ffd5cc3e88.pdf)
* 2010: Ding & Simonoff: [An Investigation of Missing Data Methods for Classification Trees Applied to Binary Response Data](http://www.jmlr.org/papers/volume11/ding10a/ding10a.pdf)
  - this paper looks at effects of different imputation methods when using a DT-based model and binary outcome
  - basically, creating a "missing" category works best ... which is opposite to so much "stat analysis" advice
* 2014: Wood et al: [The estimation and use of predictions for the assessment of model performance using large samples with multiply imputed data](https://onlinelibrary.wiley.com/doi/pdf/10.1002/bimj.201400004)
* 2018: Mercaldo & Blume: [Missing data and prediction: the pattern submodel](https://academic.oup.com/biostatistics/advance-article/doi/10.1093/biostatistics/kxy040/5092384)
* [Embedding for Informative Missingness: Deep Learning With Incomplete Data](https://proceedings.allerton.csl.illinois.edu/media/files/0202.pdf)
  - gist: learning missingness representation from an embedding
  - haven't read it yet, but is it any different then the idea of encoding "missing" as a level, then
    doing an embedding?  (that's something I was going to try / figured was done since both the missing indicator
    and embedding techniques are done with catvars)
* 2019: Josse et al: [On the consistency of supervised learning with missing values](https://arxiv.org/abs/1902.06931)
  - paper that addresses that missing data is often studied in the inferential setting,
    but much less is known about the predictive setting;  for example, it finds that mean
    imputation isn't so bad in predictive setting as it is considered in inferential setting

### Differences between the explanatory and predictive modeling settings
* 2001: Breiman: [Statistical modeling: The two cultures (with comments and a rejoinder by the author)](https://projecteuclid.org/download/pdf_1/euclid.ss/1009213726)
* 2010: Shmueli: [To explain or predict?](https://projecteuclid.org/download/pdfview_1/euclid.ss/1294167961)
* 2014: Allison: [Prediction vs. Causation in Regression Analysis](https://statisticalhorizons.com/prediction-vs-causation-in-regression-analysis)
* 2014: Steyerberg & Vergouwe: [Towards better clinical prediction models: seven steps for development and an ABCD for validation](https://academic.oup.com/eurheartj/article/35/29/1925/2293109)
  - excellent article that focuses on risk prediction rather than on inference (like most other articles)
  - there are actually a bunch of articles that cite this article that might be worth reading too (look
    at its citations page on G Scholar)
* 2015: Lo et al: [Why significant variables aren’t automatically good predictors](https://www.pnas.org/content/112/45/13892.long)
* 2017: Yarkoni & Westfall: [Choosing Prediction Over Explanation in Psychology: Lessons From Machine Learning](https://journals.sagepub.com/doi/abs/10.1177/1745691617693393)
* 2017: Mullainathan & Spiess: [Machine Learning: An Applied Econometric Approach](https://pubs.aeaweb.org/doi/pdf/10.1257/jep.31.2.87)

    
### Some Blogs & Stuff
* 2018: Krishnan: [End to End — Predictive model using Python framework](https://towardsdatascience.com/end-to-end-python-framework-for-predictive-modeling-b8052bb96a78)
  - overall good end-to-end article on building a predictive model in python that deserves a shout out
  - not necessarily related to my "missing values" mission, but not unrelated
* 2019 (accessed): Becker: [Handling Missing Values](https://www.kaggle.com/dansbecker/handling-missing-values)
  - shows that imputation + indication performs best
* 2019 (published): Badr: [6 Different Ways to Compensate for Missing Values In a Dataset (Data Imputation with examples)](https://towardsdatascience.com/6-different-ways-to-compensate-for-missing-values-data-imputation-with-examples-6022d9ca0779)
  - goes beyond the typical "delete rows, delete cols, try imputation"
  - mention kNN imputation, multiple imputation by chained equations (MICE), and imputation using deep neural nets
* 2019 (accessed): Prabhakaran: [Missing Value Treatment (in R)](http://r-statistics.co/Missing-Value-Treatment-With-R.html)
  - very similar article to the above (i.e., goes over basic BS like mode imputation, then mentions kNN and MICE)
  



    





