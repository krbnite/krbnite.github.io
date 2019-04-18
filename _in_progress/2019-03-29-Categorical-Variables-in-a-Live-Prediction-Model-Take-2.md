

Ok, so it's been about a week of reading, thinking, toying around...  

I found, if you look hard enough, people definitely have thought about prediction models in practice.  Not
that I assumed this wasn't the case.  What surprised me is how much generic advice about CCA, mean 
imputation, etc, is given on so many various StackExchange and Quora posts, and various blogs.  I read
so many bits where the writer wouldn't even bat an eye when recommending CCA for dealing with
missing data when developing a prediction model... Like, when the fuck does the writer think the
prediction model is going to be used?  Just on a similar CCA'd-ass test set?   Snore.

Anyway, I didn't want to distort my first article and continue updating it... It needs to be journaled, and
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

Anyway, so you can definitely find info on this topic in the KDNuggets community and in sources
where people actually use predictive modeling...  But also, you'll find a lot of people talking
about dropping rows and columns...noting that it might not be ideal, but offering it as a possibility
anyway......and so often without even just noting, "This technique does not inform you how the fuck
you would use the resulting model on live, incoming data, some of which may have missing values."


* 2007: [Handling Missing Values when Applying Classification Models](http://jmlr.csail.mit.edu/papers/volume8/saar-tsechansky07a/saar-tsechansky07a.pdf)
* 2009: Janssen et al: [Dealing with Missing Predictor Values When Applying Clinical Prediction Models](https://pdfs.semanticscholar.org/0c67/97f88f12edef205a314188f936ffd5cc3e88.pdf)
* 2010: Ding & Simonoff: [An Investigation of Missing Data Methods for Classification Trees
Applied to Binary Response Data](http://www.jmlr.org/papers/volume11/ding10a/ding10a.pdf)
* 2014: Wood et al: [The estimation and use of predictions for the assessment of model performance using large samples with multiply imputed data](https://onlinelibrary.wiley.com/doi/pdf/10.1002/bimj.201400004)
2018: Mercaldo & Blume: [Missing data and prediction: the pattern submodel](https://academic.oup.com/biostatistics/advance-article/doi/10.1093/biostatistics/kxy040/5092384)


* https://www.kaggle.com/dansbecker/handling-missing-values
  - shows that imputation + indication performs best

* https://towardsdatascience.com/6-different-ways-to-compensate-for-missing-values-data-imputation-with-examples-6022d9ca0779
  - goes beyond the typical "delete rows, delete cols, try imputation"
* http://r-statistics.co/Missing-Value-Treatment-With-R.html
  - very similar article to the above
  
* https://arxiv.org/abs/1902.06931
  - paper that addresses that missing data is often studied in the inferential setting,
    but much less is known about the predictive setting;  for example, it finds that mean
    imputation isn't so bad in predictive setting as it is considered in inferential setting
  - no citations yet, and I didn't read yet...so tread carefully


* though I created all my own python functions, sci-kit learn comes stock w/ 'em, e.g.:
  - https://scikit-learn.org/stable/auto_examples/plot_missing_values.html
  
* Seems like R package MICE for predictive mean matching gets a lot of attention and callouts, even
  for articles that are about predictive models
  - e.g., https://statistical-programming.com/predictive-mean-matching-imputation-method/
  - so given the imputation+indication often works best for prediction, I want to see if
    MICE+indication is the best in that class
  - eBook: https://stefvanbuuren.name/fimd/sec-pmm.html
  
  
* [Towards better clinical prediction models: seven steps for development and an ABCD for validation](https://academic.oup.com/eurheartj/article/35/29/1925/2293109)
  - excellent article that focuses on risk prediction rather than on inference (like most other articles)
  - there are actually a bunch of articles that cite this article that might be worth reading too (look
    at its citations page on G Scholar)
    
* [Choosing Prediction Over Explanation in Psychology: Lessons From Machine Learning](https://journals.sagepub.com/doi/abs/10.1177/1745691617693393)

* [Statistical modeling: The two cultures (with comments and a rejoinder by the author)](https://projecteuclid.org/download/pdf_1/euclid.ss/1009213726)

* [Why significant variables aren’t automatically good predictors](https://www.pnas.org/content/112/45/13892.long)

* [Machine Learning: An Applied Econometric Approach](https://pubs.aeaweb.org/doi/pdf/10.1257/jep.31.2.87)
  
* http://people.stern.nyu.edu/jsimonof/jmlr10.pdf
  - this paper looks at effects of different imputation methods when using a DT-based model and binary outcome
  - basically, creating a "missing" category works best ... which is opposite to so much "stat analysis" advice

* Learning missingness representation from an embedding
  - https://proceedings.allerton.csl.illinois.edu/media/files/0202.pdf
  - haven't read it yet, but is it any different then the idea of encoding "missing" as a level, then
    doing an embedding?  (that's something I was going to try / figured was done since both the missing indicator
    and embedding techniques are done with catvars)

* Overall good end-to-end article on building a predictive model in python that deserves a shout out
  - https://towardsdatascience.com/end-to-end-python-framework-for-predictive-modeling-b8052bb96a78
  - not necessarily related to my "missing values" mission, but not unrelated
