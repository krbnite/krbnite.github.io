Been reading through:  https://epiresearch.org/serlibrary/serplaylists/introduction-to-marginal-structural-models/


A marginal structural model is a type of statistical model used for causal inference.  The term mostly crops
up in epidemiology -- you know, the "study (scientific, systematic, and data-driven) of the distribution (frequency, pattern) and determinants (causes, risk factors) of health-related states and events (not just diseases) in specified populations (neighborhood, school, city, state, country, global)." (Source: [Principle of Epidemiology](https://www.cdc.gov/ophss/csels/dsepd/ss1978/index.html))


# What is a structural model?   
If you want to know what a "marginal structural model" is, then maybe you want to first know
what a "structural model" is.  Basically, "structural" is synonymous with "causal" -- these are models that
aim to identify causal relationships.

Marginal structural models are called structural models, because:
* "they model the probabilities of counterfactual variables and in the econometric and social science literature 
models for counterfactual variables are often referred to as structural." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)
* "models exploring causal relationships are referred to as structural in the econometric and social sciences 
literature.” - [Williamson et al [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980)

# What is a marginal model?  
Ok, so structural means we are looking to infer cause-and-effect relationships from the data.  What
does "marginal" mean?

They are called marginal models, because: 
* "they model the marginal distribution of the counterfactual random variables Y[a=1] and Y[a=0] rather than the joint distribution." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)
* "they use the marginal distribution of the treatment (or exposure) variable at any time point to weight its relationship with outcome. Marginal in statistics means ‘unconditional’ —- i.e. not conditional on other variables, like the probabilities of one variable on the margins of a contingency table of two discrete variables." - [Williamson et al [2017]](https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980)

# What is a counterfactual variable?

# What is a saturated model?
MSMs are also considered saturated... What does that mean?

Marginal structural models are considered saturated, because: 
* "each has two unknown parameters and thus each model places no restriction on the possible values of the two unknown probabilities” pr(Y[a=0] = 1) and pr(Y[a=1] =1)." - [Robins et al [2000]](https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf)




https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980
“Ideally, the true causal effect of a treatment (or exposure) would be estimated by comparing what actually happened to a subject given a certain treatment with what would have happened to the same subject if (contrary to fact) they had received the alternative treatment. Since we can rarely observe the same patients under both conditions (even in a crossover randomized controlled trial carry-over effects induce systematic differences), the only way to determine causal effects is to compare two groups that are identical in every way except that one group was treated and the other was not. Marginal structural models (as other causal models) attempt to fully adjust for measured confounders to enhance group comparability and estimate causal effects in a similar way. While these methods have been found to replicate results of randomized controlled trials when all their assumptions are met, only randomization can ensure comparability of treatment groups by balancing both measured and unmeasured confounding factors.”









# References
* Coursera: [Video Lectures on Causal Inference](https://www.coursera.org/learn/crash-course-in-causality)
  - [Lecture on MSMs](https://www.coursera.org/lecture/crash-course-in-causality/marginal-structural-models-EUpei)
*	Helpful Dissertation:  http://discovery.ucl.ac.uk/1346448/1/1346448.pdf
  - left off on pg 21 (KEEP READING)
*	Very nice intro article:  https://academic.oup.com/ndt/article/32/suppl_2/ii84/2989980
* Advanced Reading / Deeper Dive: https://epiresearch.org/serlibrary/serplaylists/introduction-to-marginal-structural-models/
  - https://epiresearch.org/wp-content/uploads/2014/07/Robins_EPI_2000_11_550.pdf
    * read this one again after having read and watched other sources
  - https://epiresearch.org/wp-content/uploads/2014/07/Sato_EPI_2003_14_680.pdf
    * haven't read yet...


* YouTube Playlist on Causal Inference:  https://www.youtube.com/watch?v=RtBY1zRaE_4&list=PL2yD6frXhFoYSO4MF3utWG_UUv2bhNZj0
  - only a few mins into 1st video (KEEP WATCHING)