---
layout: post
title: The Perils of PCA
tags: r machine-learning
---

Need to reduce the dimensionality of your data? Principal Component Analysis (PCA) is often spouted as a go-to tool. No doubt, the procedure will reduce the dimensionality of your feature space, but have you inadvertently thrown out anything of value?


# Moral / TLDR

Before taking PCA out of the toolbelt, make sure the utility of PCA is in harmony with your ultimate goal.

The goal of the PCA step is reduce to the dimensionality of your feature space, while maintaining as much variability as possible. Why do we care about variability? The idea is that variability can serve as a stand-in for information. This likely makes sense for most unsupervised machine learning objectives (e.g., finding clusters in your data using k-means clustering).

But in a supervised scenario, like that below, we do not necessarily need a stand-in for information. If we need to do some dimensionality reduction, we have a target variable that can inform us how to proceed, e.g. by computing pairwise correlations, (feature_x, target), and discarding any features not meeting some correlation threshold, e.g., corr_thresh = 0.15. To apply PCA is to willingly ignore this information: a PCA reduction step does not use any information about the target variable, and does not promise to provide any either.

# Case Study
One of the most immediate dangers of applying PCA with wild abandon is accidentally discarding important, small-scale variables. From PCA’s perspective, these are no match against unimportant, large-scale variables.

In the example below, our features consist of a uniform random variable, a normal random variable, and a sinusoid. The target is also a sinusoid; it is a simple linear transformation of the input sinusoid. The point here is that we have foresight: only the sinusoidal feature is important, while the two noisy variables can be discarded. However, a blindly-applied PCA tells us to do the opposite: keep the noise, discard the signal.

```r
x = seq(-100,100,1)
features = data.frame(
    urv = 12*rnorm(x),
    nrv = 7*runif(x),
    sig = 0.1*sin(x) + 0.05*rnorm(x)
)
target = 5*features$sig -1 + runif(x)
# Principle Components
pc = prcomp(features)
pc
```
```
Standard deviations:
[1] 12.51706946  2.06053092  0.07782538

Rotation:
             PC1          PC2           PC3
urv 0.9999360514 -0.011308745  7.407587e-05
nrv 0.0113083952  0.999929280  3.681737e-03
sig 0.0001157065  0.003680664 -9.999932e-01</code></pre>
```

The PCA barely rotates the feature space: each component is dominated by a single input feature by a factor of ~100. This is because our features started out independent of each other. The minor rotation is just due to noise. Importantly, if we were aiming to reduce the feature space to two dimensions, we would have just thrown out the signal almost entirely.

```r
prcomp(features, tol=0.1)
```
```
Standard deviations:
[1] 11.772573  2.073877

Rotation:
              PC1          PC2
urv  0.9999266428 -0.012107898
nrv -0.0121072421 -0.999924838
sig -0.0003516282 -0.001928026
```

Therefore, if you’re thinking about using PCA, your first step is to properly normalize each of your feature variables. At the very least, normalizing each feature will hedge aginst throwing out small-scale signals in the noise. We will z-score each feature, which will do a good job here. (However, z-scoring assumes that variables are approximately normally-distributed, so if you have extremely heavy-tailed feature distributions, watch out!)

```r
# One could simply write prcomp(features, center=TRUE, scale=TRUE),
#   however, I write out the zscore and explicitly normalize to emphasize
#   that this is one of many normalization schemes that could be employed.
zscore = function(x) { (x - mean(x)) / sd(x) }
zfeatures = data.frame(
    zurv = zscore(features$urv),
    znrv = zscore(features$nrv),
    zsig = zscore(features$sig)
)
# Principle Components
zpc = prcomp(zfeatures, tol=0.92)  
zpc
```
```
Standard deviations:
[1] 1.0523303 0.9793253

Rotation:
            PC1        PC2
zurv  0.5993543 -0.3964375
znrv -0.6039024  0.3463062
zsig -0.5254296 -0.8502408
```

In the first case, it was pretty clear that the third component should be thrown out (assuming that variance is a good proxy for information value). In this case, that’s not so obvious since each component has a similar variance. The threshold parameter, tol, had to be set to greater than 90% to reduce the space at all. In reality, it probably isn’t a good idea to set this threshold so high (tol=0.1 is a better idea).

Throwing out the third component or not, we would not have discarded all the signal information. However, we did rotate it into noise, which was stupid. Look what we did to the correlations with the target:

```r
# Original Correlations
cor(features, target)
```
```        
           [,1]
urv 0.068047584
nrv 0.009189832
sig 0.542915065
```

```r
# Correlation of remaining component when we did not normalize the features
pc_features = as.matrix(features) %*% pc$rotation
cor(pc_features, target)
```
```
           [,1]
PC1 0.06801666
```

```r
# Correlation of the two remaining components after normalizing the features
zpc_features = as.matrix(zfeatures) %*% zpc$rotation
cor(zpc_features, target)
```
```
           [,1]
PC1 -0.2375954
PC2 -0.4956501
```

We started with a great variable and high correlation. Without normalization, our PCA step threw this info out and we were left with nearly-zero predictive power. When we did normalize the variable, at best the PC step didn’t totally screw us over.

