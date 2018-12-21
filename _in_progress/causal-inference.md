

From [intro](https://www.coursera.org/lecture/crash-course-in-causality/confusion-over-causality-x4UMR):
  - The goals of causal modeling:
    * formal definitions of causal effects
    * assumptions necessary to identify causal effects from data
    * rules about what (confounding) variables need to be controlled for
    * sensitivity analyses to determine the impact of violations of assumptions on conclusions
    
 Wright [1921] and Neyman [1923] were early pioneers of causal modeling, though the field did not
 full mature until the 1970s with Rubin's causal model (Rubin [1974]). In the 1980s, Greenland and Robins [1986]
 introduced causal diagrams, Rosenbaum and Rubin [1983] introduced propensity scores, and Robins [1986] introduced
 time-dependent confounding (followed up in Robins [1997]).  Then there  the later work on causal diagrams by 
 Pearl [2000] (side note: Pearl is from NJIT!).  Post-2000, there are the methods of optimal dynamic treatment
 strategies (Murphy [2003]; Robins [2004]) and a machine learning approach called targeted learning (van der Laan [2009]).
 
 These methods are primarily for observational studies and natural experiments -- things as they are in the world.  The ideal
 is to use randomized control... But you just cannot do that for many things (think weather, astronomy, disease, etc).
 
 
 --------------------------------
 
## Potential Outcomes and Counterfactuals

https://www.coursera.org/lecture/crash-course-in-causality/potential-outcomes-and-counterfactuals-0XWFc

Suppose we are interested in the causal effect of some treatment A (e.g., A = {1:vaccine|0:placebo}) on some outcome Y
(e.g., Y = {1:contractsDiseaseAnyTimeAfterTreatment|0:doesNotContractDiseaseAfterTreatment}).

* Observed outcome: what actually happens
* Potential outcome: an outcome that we **would** see given treatment A, e.g., given a binary treatment, each
person has two potential outcomes, Y[0] and Y[1].
  - Notation: Y[a] is the outcome that would be observed if treatment was set to A=a
  - Potential outcomes span the possibilities of treatments 
  
#### Example 1
Suppose the treatment is the flu shot and the outcome is the time until the individual gets the flu. Then
the potential outcomes are:
* Y[1]: time until the individual would get the flu if they received the flu vaccine
* Y[0]: time until the individual would get the flu if they did not receive the flu vaccine

#### Example 2
Suppose the treatment is regional (A=1) versus general (A=0) anesthesia for surgery, and that the outcome
(Y) is major pulmonary complications.  Then the potential outcomes are:
* Y[1]: equal to 1 if major pulmonary complications (0 otherwise), if given regional anesthesia
* Y[0]: equal to 1 if major pulmonary complications (0 otherwise), if not given general anesthesia


Counterfactual outcomes are ones that would have been observed, had the treatment been different
* Potential outcomes represent what could be, while counterfactual outcomes represent what could have been
  - e.g., if my treatment was A=1, then
      * before treatment, my potential outcomes were Y[1] and Y[0]
      * after treatment, my outcome is Y[1] and counterfactual outcome is Y[0]
      
Before a treatment decision is made, each patient's outcome spans the range of potential outcomes.  Once
a treatment has been give, each patient has an actual/observed outcome and a set of counterfactual outcomes.  The
idea, ultimately, is that we need a sizable population so that we can estimate all random variables of 
interest -- the range of potential outcomes -- despite having "missing data" on any one specific subject.

