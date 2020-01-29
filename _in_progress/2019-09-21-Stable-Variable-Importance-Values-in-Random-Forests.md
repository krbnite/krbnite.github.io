


2017: Behnamian et al: [A Systematic Approach for Variable Selection With Random Forests: Achieving 
Stable Variable Importance Values](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8038868)
  - We've all been there: train your RF, look at the feature importances.  Maybe you want to find the
    top 10 features to go ahead and make a more computationally efficient model (that also lessens the
    burden of future data collection needs).  Or maybe you just want to better explain the internal
    logic of your model to a stakeholder.  Whatever: the results look interesting, but you decide to
    add more trees and retrain to see if you get better accuracy.  Nice!  You do!  But, wait: 
    the imporantces changed rankings.  So you add some more trees, train again -- and again a slightly
    different set of importance rankings.  What's worse: your curiosity gets the best of you and without
    changing any hyperparameters whatsoever, you retrain and get yet a different ranking. It doesn't 
    matter if you're using Gini importance (aka mean decrease in impurity) or permutation importance
    (aka mean decrease in accuracy).  Both  prove to be fairly unstable.  What's the deal here?!
  - This paper looks at (i) how large a forest must be to produce stable importance estimates, and
    (ii) how class separability affects this result.
    * They do find that, for large enough nTree, the importances stabilize
    * They also find that averaging the rankings over multiple runs with smaller nTrees produces stable estimate
    * The specifics are driven by the class separability
  - Interestingly, this paper is about the use of RFs in signal and image processing, where the data is
    collected at high temporal and/or spatial frequency.  
    * This motivates the need for stable importance rankings: "Reducing model data load can reduce processing
      times and storage requirements, and can also be used to inform longterm analyses, as attention can focus 
      on just the sensors and variables that provide relevant information to a given classification problem."
    * Furthermore, in this signal/image analysis field, it has been shown that RFs can be improved quite a 
      bit by finding and removing any noise variables.
  - Importantly, independent of what VImp measure one uses, "because of the random way in which training data 
    and variables are selected to determine the split at each node in Random Forests, importance rankings 
    differ from one model run to another, especially when if only a small ntree are generated."

Jupyter Notebook: https://github.com/krbnite/krbnite.github.io/blob/master/_notebooks/2019-09-22-Stabilizing-Variable-Importance-Fluctuations-in-a-Random-Forest.ipynb
