

If you haven't heard: accuracy is garbage.

Sure, accuracy is great to use with perfectly balanced classes: but balance is the exception, 
not the rule! If I am attempting to classify whether or not a customer will churn, then 98%
accuracy is most likely horrible if the churn rate is 2%.  It's as good as assuming no customer
will ever churn.

## The problem
Imbalanced classification requires more attention than its balanced brethren.  Most
standard machine learning classifiers will essentially treat the minority class as if
it were ignorable noise.  Typical ways to resolve this issue come in two major forms:
random resampling methods and modification of balanced classification algorithms.

Note that most/all of these techniques are performed on the training set.  Do not
mess with the test set!  These techniques are meant to help train a good classifier,
which is then to be used on test (or in production) as it would be used otherwise.


## Resampling Methods to Reduce Class Imbalance

### Random Undersampling / Downsampling
Randomly discard majority class data so that the classes strike better balance. 

There is no hard rule here. For example, if you have 100k data points with a 5% event rate,
you might randomly select 10% of the majority class, giving 9.5k nonevents (65.5%) and 5k events (34.5%). 
The classes are still imbalanced, but the situation has dramatically improved. 

Should you do random sampling with or without replacement?  Hmm... Good question!  
On large enough data sets, it might not matter too much, but generally my gut says to go
with random sampling **with** replacement whenever possible.

An obvious pro here is reducing compute, but an obvious con is throwing out potentially useful data.


### Random Oversampling / Upsampling
Randomly sample the minority class with replacement until you achieve some desired size.

Again, there does not seem to be an exact rule to this.  Starting with 100k data points 
and a 5% event rate, you might oversample the minority class enough to achieve a tenfold
increase in size, giving 50k (34.5%) events and 95k nonevents (65.5%).

* Pro: you are no longer throwing out data.  
* Con: you are replicating minority-class data points
exactly, which likely promotes overfitting. 
* Pro: Apparently oversampling > undersampling, performance-wise.


### Under-Over Hybrid
Not sure if this helps, but one can also do a little of both upsampling and downsampling.

For example, taking 100k data points with a 5% event rate, we could randomly select 20% of the 
majority class and oversample the minority class fourfold, giving 19k nonevents (48.7%) and 
20k events (51.3%).


### SMOTE: Synthetic Minority Oversampling TEchnique
This technique helps mitigate the potential problem of overfitting associated with 
random oversampling, by generating similar-but-not-identical minority-class data
in the training set.  

Apparently SMOTE is not very efficient for high-dimensional data. 

Also note that there is a related technique called modified SMOTE, or MSMOTE.  


## Expanding Upon Balanced Classification Algorithms

### Bootstrap Aggregation (Bagging)
Typically when you bootstrap, you aggregate, so the name is kind of redundant.  
For example, if you want to nonparametrically estimate a robust estimate of some
statistic, you could bootstrap the data and take the median of that statistic. Or
the mean. Or any type of aggregation, really. This is how I've computed confidence
intervals in the past.  

Anyway, call it bootstrapping or bagging.  Whatever!  In the machine learning context,
the importance of boostrapping (i.e., creating N new M-element data sets by randomly
sampling-with-replacement the actual P-element data set) is that you get to create an
ensemble of classifiers.  The aggregation step then gives you a fairly robust estimate
of the classifier, just like it did for the plain ol' statistic mentioned above.

* Pro: reduces variance in classifier
* Pro: Mitigates overfitting
* Pro: Can find signals in noisy data (boosting suffers in this regard)
* Con: Classifiers must not be terrible in the first place.
  - I think this can be mitigated by applying the bagging technique to an oversampled data set



### Boosting

### Gradient Boosting



## Some Further Reading / References
* [Learning from Imbalanced Classes](https://svds.com/learning-imbalanced-classes/)
* [How to handle Imbalanced Classification Problems in machine learning?](https://www.analyticsvidhya.com/blog/2017/03/imbalanced-classification-problem/)
* [8 Tactics to Combat Imbalanced Classes in Your Machine Learning Dataset](http://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/)

## Some Code
* https://github.com/scikit-learn-contrib/imbalanced-learn


