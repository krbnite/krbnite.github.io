
NEXT UP:

* Module 2: https://www.coursera.org/lecture/crash-course-in-causality/confounding-VT3H4
  - disjunctive cause criterion can also be called "disconnective criterion" or "simply disconnect criterion"
  since "disjunctive" means "lacking connection" and the criterion basically says "only worry about
  disconnecting nearest neighbor nodes that flow directly into A or Y" (btw, doesn't always work, but good rule of thumb)

* Module 3: https://www.coursera.org/lecture/crash-course-in-causality/observational-studies-V6pDQ

* Module 4: https://www.coursera.org/lecture/crash-course-in-causality/intuition-for-inverse-probability-of-treatment-weighting-iptw-nrrCT

* Module 5: https://www.coursera.org/lecture/crash-course-in-causality/introduction-to-instrumental-variables-ueIMD
  - Ertefaie et al [2017]: [A tutorial on the use of instrumental variables in pharmacoepidemiology](https://www.cceb.med.upenn.edu/sites/default/files/uploads/cter/A%20tutorial%20on%20the%20use%20of%20instrumental_0.pdf)
  - Baiocchi et al [2015]: [Tutorial in Biostatistics: Instrumental Variable Methods for Causal Inference](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4201653/)
------------------------

## Misc Reading
* https://en.wikipedia.org/wiki/Causal_inference
* https://en.wikipedia.org/wiki/Rubin_causal_model
* https://en.wikipedia.org/wiki/Instrumental_variables_estimation

http://mlg.eng.cam.ac.uk/zoubin/SALD/Intro-Causal.pdf

https://www.inference.vc/untitled/

http://www.jmlr.org/papers/volume11/spirtes10a/spirtes10a.pdf

https://blog.acolyer.org/2018/09/17/the-seven-tools-of-causal-inference-with-reflections-on-machine-learning/

http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.726.5229&rep=rep1&type=pdf


* Chen & Pearl [2013]: [Regression and Causation: A Critical Examination of
Six Econometrics Textbooks](https://ftp.cs.ucla.edu/pub/stat_ser/r395.pdf)
* Saddiki & Balzer [2018]: [A Primer on Causality in Data Science](https://www.researchgate.net/publication/327549882_A_Primer_on_Causality_in_Data_Science)
* Mohan & Pearl [2018]: [Graphical Models for Processing Missing Data](https://ftp.cs.ucla.edu/pub/stat_ser/r473-L.pdf)

Course notes:
* http://bkenkel.com/psci8357/notes/08-causal.pdf
  - points out an interesting thing about causal models: in a regression, you do not want to include
  post-treatment variables, e.g., tar build up in lungs when your treatment is smoking and outcome is lung cancer;
  this is b/c the tar buildup variable will strongly predict the outcome, pushing the coeff on smoking towards
  zero, despite smoking being the cause for both tar build up and the outcome... In a ML predictive model you
  wouldn't care, but in a causal model it matters

--------------------------------

Some Google stuff:

http://www.unofficialgoogledatascience.com/2017/01/causality-in-machine-learning.html

* Google's R Package:  https://opensource.googleblog.com/2014/09/causalimpact-new-open-source-package.html
  - Corresponding paper:  https://ai.google/research/pubs/pub41854

-----------------------------------

Susan Athey (ML + CI)

https://github.com/susanathey/causalTree

https://github.com/swager/causalForest

https://www.coursera.org/learn/statistical-inferences

-----------------------------------

Excellent Medium articles on ML/CI

https://medium.com/teconomics-blog/machine-learning-for-decision-making-e776f9f8917e

https://medium.com/teconomics-blog/using-ml-to-resolve-experiments-faster-bd8053ff602e

https://medium.com/teconomics-blog/machine-learning-meets-instrumental-variables-c8eecf5cec95

------------------------------------


People working on ML+CI

http://www.mit.edu/~vchern/

https://www.gsb.stanford.edu/faculty-research/faculty/susan-athey

https://www.gsb.stanford.edu/faculty-research/faculty/guido-w-imbens

----------------

Potential models to look into:  
•	Already looking into:  marginal structural models (MSMs), structural nested models (SNMs), and the g-formula
•	Can also explore:  discontinuity design, difference-in-differences, fixed effects modeling, and instrumental variables modeling

------------------------------------------

Other MOOCs:
* https://www.coursera.org/learn/causal-inference
* https://www.coursera.org/learn/causal-effects
* https://www.coursera.org/learn/probabilistic-graphical-models
* https://www.edx.org/course/causal-diagrams-draw-assumptions-harvardx-ph559x
* https://www.coursera.org/learn/designexperiments

Courses:
* 2019 Columbia Course: http://www.cs.columbia.edu/~blei/seminar/2019-applied-causality/
  - weekly recommended readings (Pearl, Hernan, Robins, etc)
  - should go through the readings
