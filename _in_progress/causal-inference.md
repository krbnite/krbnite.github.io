

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
person has two potential outcomes, Y[0] and Y[1] (each of which is a random variable).
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
a treatment has been given, each patient has an actual/observed outcome and a set of counterfactual outcomes.  The
idea, ultimately, is that we need a sizable population so that we can estimate all random variables of 
interest -- the range of potential outcomes -- despite having "missing data" on any one specific subject.

----------------

## Hypothetical Interventions

https://www.coursera.org/lecture/crash-course-in-causality/hypothetical-interventions-Lgb6O
 

We are interested in intervening, or taking action on, a variable in order to reveal causal effects. An 
"intervention variable" (or just intervention) is just that: a variable that we can "intervene on," or take an 
action on.  Intervening on a variable is often referred to as manipulating the variable, and so this type of
variable is also known as a manipulable variable. 

A quote from Holland [1986] states: "no causation without manipulation."  The point is: if a variable cannot 
be manipulated, then it is difficult to consider causal effects of that variable

The treatment variables (or treatments) we consider in this course will be interventions: a treatment is a something we can 
specify/manipulate at random in a hypothetical trial; it is manipulable; it is an intervention:
* The amount of drug X can be randomly assigned
* What race a subject is cannot be randomly assigned
* Whether or not a placebo is prescribed is a intervention/treatment
* Prescribing someone to be aged 45yo at random is not possible, so not an intervention/treatment

Not all variables are manipulable.

Things like age and race are not interventions -- they are immutable variables, not manipulable 
variables.  Immutable variables can still have causal effects, but they cannot be dealt with 
in the same way as manipulable variables -- e.g., they do not fit cleanly into the potential outcomes
framework.  To study immutable variables, one often looks for associated manipulable variables, e.g.,
one's name on a resume instead of race or gender (i.e., you can gain insight into the immutable variables
of race and gender on hiring practices by using the same resume with different names).

We focus on hypothetical interventions in this course
* b/c they're well defined
* b/c they're potentially actionable
* b/c we have a framework to parse out causal effects

So... What is a causal effect?
* Treatment A has a causal effect on Outcome Y if Y[a=1] differs from Y[a=0]

### Example: Ibuprofen
* A: take Ibuprofen (1) or not (0)
* Y: headache gone in an hour (1) or not (0)

Often you will hear people improperly reasoning about causality: "I took Ibuprofen an hour ago, and
now my headache is gone. Therefore, Ibuprofen works!"  This is equivalent to saying "Y[a=1]=1" -- it tells us
nothing about "Y[a=0]" or even the strength of effect between a=1 and Y=1 (how many times does one take
Ibuprofen and still have a headache in an hour?).

### FPCI:  The Fundamental Problem of Causal Inference
We can only observe one potential outcome for each person.  This means we cannot necessarily observe 
causal effects at the individual level, but it doesn't mean that we cannot measure causal effects
at the population level.  

This is important:  we are not really going to be looking at things at the individual/unit level.
* Hopeless: What would have happened if I had not taken Ibuprofen an hour ago?
* Possible: What would the rate of headache remission be if everyone took Ibuprofen when they had a headache
versus if no one did?

SIDE NOTE: an "intervention variable" is not the same thing as an "intervening variable," which is basically
synonymous with a "mediating variable" ... An "intervention variable" is a "manipulable variable."
* [Intervening Variable](https://www.statisticshowto.datasciencecentral.com/intervening-variable/)
* [Manipulable Variable](https://www.statisticshowto.datasciencecentral.com/manipulated-variable/)
* This is a nice list of various types of variable name in statistical parlance
  - http://www.indiana.edu/~educy520/sec5982/week_2/variable_types.pdf
  

----------------------

## Causal Effects

https://www.coursera.org/lecture/crash-course-in-causality/causal-effects-Qt0ic

We want to more formally defined what we mean by "causal effects."
* What is an average causal effect?
* What is the causal effect of a treatment on the treated?

Conditioning vs manipulating

In any experiment or knowledge-seeking trial, we have a population of interest.  In an ideal setting (for a binary 
treatment), we would have two worlds to experiment on, each world having the same people in the population of
interest: World 0 where no one is treated (A=0) and World 1 where everyone is treated (A=1). In this scenario, we
could compute the mean outcome in W0, mean(Y[A=0]), and the mean outcome in W1, mean(Y[A=1]) -- their difference
defining the **average causal effect**.

```
AvgCausalEff := E{Y[1]-Y[0]} = E{Y[1]} - E{Y[0]} != E{Y|A=1} - E{Y|A=0}
```

In reality, we cannot compute `E{Y[1]-Y[0]}` since each individual can only have a specific treatment and outcome.  However,
we can technically estimate each term in `E{Y[1]} - E{Y[0]}`, and so estimate `AvgCausalEff`.  The last inequality
speaks to a subtlety between setting a variable and conditioning on a variable that is important to understanding 
causal inference.  We talk about this more below.  

If Y is binary, then this is a risk difference (the mean of a binary variable is just a probability, or risk,
so that the difference makes it a difference in probability).  However, for other types of random variables, such
as continuous random variables, this does not directly translate into a difference of probabilities (you might
still call it a difference in "risk", but often the word "risk" is synonomous with probability in epidemiological
studies).


#### Example of a Binary Outcome: Anesthesia
Suppose the treatment is regional (A=1) versus general (A=0) anesthesia for surgery, and that the outcome
(Y) is major pulmonary complications.  Also suppose that we know the causal risk difference: 
`E{Y[1]-Y[0]} = -0.1`.  This means that taking regional anesthesia is less risky by 10 percentage points compared with
general anesthesia. In other words, if both World 0 and World 1 had 1000 people, we would expect 100
fewer people to have pulmonoary complications in World 0 than in World 1.  Admittedly, this is relative: we
are not really saying whether either of them are safe with this statement, just that regional anesthesia is
safer; e.g., if 950 people in World 1 had complications, then 850 people in World 1 did too.

#### Example of a Continous-Valued Outcome:
Suppose the treatment is binary -- patient is given thiazide diuretic (A=1) to treat hypertension, or not 
(A=0) -- and that the outcome Y is systolic blood pressure. Also suppose that we know the causal effect:
`E{Y[1]-Y[0]} = -20 mm Hg`.  This says that the patients in World 0 would have lower systolic blood
pressure, on average, by 20 mm Hg than those patients in World 1.

### Setting, Not Conditioning
Above, we wrote down an equation that might appear confusing at first:

```
E{Y[1]} - E{Y[0]} != E{Y|A=1} - E{Y|A=0}
```

In certain settings, the two sides of the equation would actually be equal -- but that is the 
exception, not the rule.  Bear with me here...  Our equation represented two worlds, W0 and W1, which
were parallel universes with the same people in the population of interest.  In each world, everyone
was assigned the same treatment, and we were able to compute the causal effect by comparing the 
averages between worlds.  The right hand side of the above equation is usually interpreted differently
than this: `E{Y|A=1}` often reads "the mean of Y given A=1", but it is not asking "what is the mean of
Y given A=1 for the entire population?"  The term `E{Y|A=1}` is better read as "the mean of Y when restricting
to the subpopulation where A=1."  The keyword is **subpopulation**: often the subpopulation receiving
treatment is much different than the population not receiving treatment.

So, in other words, the term `E{Y|A=1} - E{Y|A=0}` actually means "the average difference in outcome
between subpopulations defined by treatment group."  

Recap 1:
* E{Y|A=1}: the mean outcome among the subpopulation with A=1
  - often read as the mean outcome given A=1
* E{Y[1]}: the mean outcome if the entire population was given treatment A=1
  - read as "the mean outcome when setting A=1" to differentiate from conditionals

Recap 2:
* E{Y|A=1} - E{Y|A=0}: a difference between two subpopulations in the same universe; not necessarily a causal effect
* E{Y[1]} - E{Y[0]}: a counterfactual difference between two parallel universes, i.e., a causal effect

### Other Causal Effects

* E{Y[1]/Y[0]}
  - for a binary variable, this is the causal relative risk
* E{Y[1]-Y[0]|A=1}
  - this one is confusing at first, but only b/c we defined the worlds homogenously above
  - consider a binary treatment given heterogeneously in W0, then W1 is just defined as the flip of this (those
  who got treatment in W0 do not get treatment in W1, and vice versa)
  - now it should be more clear what the causal effect above means: "given the subpopulation treated with A=1, 
  what if they were treated with A=0"?
  - point is: there are two worlds we are interested in for a binary treatment: the actual and counterfactual worlds
  





------------------------

## Misc Reading
* https://en.wikipedia.org/wiki/Causal_inference
* https://en.wikipedia.org/wiki/Rubin_causal_model
* https://en.wikipedia.org/wiki/Instrumental_variables_estimation






