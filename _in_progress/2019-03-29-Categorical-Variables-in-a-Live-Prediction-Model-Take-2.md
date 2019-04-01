

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





