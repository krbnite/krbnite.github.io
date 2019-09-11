

https://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm


So you've used a decision tree, and a few more -- eventually, you put 'em all
to work at the same time and hand yourself a random forest.

```python
from sklearn.ensemble import RandomForestClassifier
```

```r
library(randomForest)
```

Great.  Here's more info about those things!

https://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm



----------------------

Updated from 2019-Sep-11:

RFs are good for both classification and prob estimation: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3250568/
* “Trees should not be grown to purity. In fact, if nodes are pure, the probability estimate is either 0 or 1 in a terminal node.  As a result, a larger number of trees might be required to obtain consistent probability estimates. If trees are too small, probability estimates might be imprecise. Therefore, as a default value, the terminal node size is generally 10% of the total sample size. Alternatively, the optimal terminal node size may be tuned.”

RFs are low bias, low variance:  https://hal.inria.fr/hal-01590513/document

RFs should be Grid/RandomSearched w/ terminal node size as a search parameter (originally believed that growing trees to terminal purity was always optimal; basically, this requires more trees in the forest to have a consistent estimator):  http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.75.2319&rep=rep1&type=pdf

Are RFs consistent estimators:  http://www.jmlr.org/papers/volume9/biau08a/biau08a.pdf

RFs can be updated (recalibrated) to new patient populations (either using the Elkan rule, or the Dankowski rule).
