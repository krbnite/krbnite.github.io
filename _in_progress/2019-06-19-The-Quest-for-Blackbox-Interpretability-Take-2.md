---
title: The Quest for Blackbox Interpretability (Take 2, PImp and CPImp)
layout: post
tags: machine-learning random-forest predictive-modeling model-interpretability easi
---

* [Be Aware of Bias in RF Variable Importance Metrics](https://blog.methodsconsultants.com/posts/be-aware-of-bias-in-rf-variable-importance-metrics/)
  - very useful article detailing when Gini importance is ok, when to instead opt for permutation importance, and when to
    upgrade to conditional permutation importance
* [Bias in random forest variable importance measures: Illustrations, sources and a solution](https://link.springer.com/article/10.1186%2F1471-2105-8-25)
  - for when you have to use sampling-without-replacement instead of bootstrapping (sampling-with-replacement)
* [Conditional variable importance for random forests](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2491635/)
  - paper that originally proposed conditional permutation importances
* [Selecting good features – Part III: random forests](http://blog.datadive.net/selecting-good-features-part-iii-random-forests/)
  - Provides short, to-the-point descriptions of Gini and Permutation importances
* [Interpretable Machine Learning: Feature Importance](https://christophm.github.io/interpretable-ml-book/feature-importance.html)
  - From the online book "[Interpretable Machine Learning](https://christophm.github.io/interpretable-ml-book/)"
  


[Interesting perspective](http://blog.datadive.net/selecting-good-features-part-iii-random-forests/) on permutation 
importance:
> "Keep in mind though that these measurements are made only after the model has been trained (and is depending) 
> on all of these features. This doesn’t mean that if we train the model without one these feature, the model 
> performance will drop by that amount, since other, correlated features can be used instead."

Note that it's also found that correlated predictors are not given their proper importance in permutation
importances since permuting one predictor is less meaningful if a highly correlated predictor exists to 
stand in for it....

