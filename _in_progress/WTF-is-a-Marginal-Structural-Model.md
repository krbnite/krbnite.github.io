Been reading through:  https://epiresearch.org/serlibrary/serplaylists/introduction-to-marginal-structural-models/

# What is a structural model?   
If you want to know what a "marginal structural model" is, then maybe you want to first know
what a "structural model" is.  Basically, "structural" is synonymous with "causal" -- these are models that
aim to identify causal relationships.

Marginal structural models are called structural models, because:
* "they model the probabilities of counterfactual variables and in the econometric and social science literature 
models for counterfactual variables are often referred to as structural." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)
* "models exploring causal relationships are referred to as structural in the econometric and social sciences 
literature.” - [Williamson et al [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980)


What is a counterfactual variable?

What is a marginal model?  (“They are marginal models, because they model the marginal distribution of the counterfactual random variables” Y[a=1] and Y[a=0] “rather than the joint distribution.”)

What is a saturated model?  (“Finally, they are saturated, because each has two unknown parameters and thus each model places no restriction on the possible values of the two unknown probabilities” pr(Y[a=0] = 1) and pr(Y[a=1] =1).”)


https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980

“Marginal structural models are a class of statistical models used for causal inference in epidemiology.”

Marginal:  “Marginal structural models are called marginal because they use the marginal distribution of the treatment (or exposure) variable at any time point to weight its relationship with outcome. Marginal in statistics means ‘unconditional’—i.e. not conditional on other variables, like the probabilities of one variable on the margins of a contingency table of two discrete variables.”


“Ideally, the true causal effect of a treatment (or exposure) would be estimated by comparing what actually happened to a subject given a certain treatment with what would have happened to the same subject if (contrary to fact) they had received the alternative treatment. Since we can rarely observe the same patients under both conditions (even in a crossover randomized controlled trial carry-over effects induce systematic differences), the only way to determine causal effects is to compare two groups that are identical in every way except that one group was treated and the other was not. Marginal structural models (as other causal models) attempt to fully adjust for measured confounders to enhance group comparability and estimate causal effects in a similar way. While these methods have been found to replicate results of randomized controlled trials when all their assumptions are met, only randomization can ensure comparability of treatment groups by balancing both measured and unmeasured confounding factors.”









# References
* https://epiresearch.org/serlibrary/serplaylists/introduction-to-marginal-structural-models/
  - https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf
* https://www.coursera.org/lecture/crash-course-in-causality/marginal-structural-models-EUpei
  - https://www.coursera.org/learn/crash-course-in-causality
*	http://discovery.ucl.ac.uk/1346448/1/1346448.pdf
*	https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980

