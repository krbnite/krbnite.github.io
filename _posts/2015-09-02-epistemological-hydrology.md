---
id: 86
title: 'Hydrologists be Gettin&#8217; all Epistemological'
date: 2015-09-02T16:47:20+00:00
author: ogkubl
layout: post
guid: http://kevin-urban.com/blog/?p=86
permalink: /index.php/2015/09/02/epistemological-hydrology/
categories:
  - hydrology
  - modeling
  - Uncategorized
---
The solar wind is constantly crashing into the earth, alternating between times of turbulent cacophony and moments of serenity.

On a finer scale, we see variations in its density, bulk speed, and intrinsic magnetic field&#8212;sometimes in sync, and sometimes not so much. It is clear that certain features of the solar wind are correlated with various activities in Earth&#8217;s ionosphere and magnetosphere, and as a scientist studying this stuff, my day-to-day is figuring out how to transform such correlative knowledge into a story of causation&#8230;

But how?

Yesterday I forayed into the realm of hydrology&#8212;a field where this question has also been taken quite seriously. Although a stranger to hydrological parlance, I found the mathematical landscape (stats, time series, modeling) and philosophical quandaries (&#8220;This high correlation cannot be for naught, can it?!&#8221;) were familiar.

I came across many fascinating papers (see &#8220;Further Reading&#8221; below)&#8212;and while I skimmed them all, I only have time today to ponder about and quote from one of them. The paper I spent the most time with was James W. Kirchner&#8217;s 2006 paper, &#8220;[Getting the right answers for the right reasons: Linking measurements, analyses, and models to advance the science of hydrology](http://onlinelibrary.wiley.com/doi/10.1029/2005WR004362/abstract).&#8221; <!--more-->

The accuracy of a broken clock illustrates at least part of the article&#8217;s sentiment: though broken and frozen at 4:40, it accurately tells the time twice a day &#8212; and approximately does so for a couple minutes just before and after (&#8220;good enough for most applications!&#8221;). 

Lessons Learned: If the modeler is careful not to use the clock at times departing too far from 4:40, then this broken clock is a great model for the time of day!

[<img class="size-medium wp-image-101 alignright" src="http://kevin-urban.com/blog/wp-content/uploads/2015/09/broken-clock-300x193.png" alt="broken-clock" width="300" height="193" srcset="http://kevin-urban.com/blog/wp-content/uploads/2015/09/broken-clock-300x193.png 300w, http://kevin-urban.com/blog/wp-content/uploads/2015/09/broken-clock.png 517w" sizes="(max-width: 300px) 100vw, 300px" />](http://kevin-urban.com/blog/wp-content/uploads/2015/09/broken-clock.png)

The point is, oftentimes misplaced faith is put into mathematical models describing some physical phenomenon. It might be that the model is only good for one type of condition (&#8220;late afternoon&#8221;), yet it is used to say things about other types of conditions (&#8220;early morning&#8221;).

Sorry, I&#8217;m being purposely vague here&#8230; A concrete example might be a model that assumes stationarity for a geophysical time series: by its construction, it cannot&#8212;without modification&#8212;describe or predict the nonstationary features of the actual recorded time series.

Over-parameterization of a model was a hot topic in the paper: the fundamental pitfall of over-parameterizing is that too many extraneous degrees of freedom allow one to fit their model to almost any data set, even if that model is wrong or the data is bad. This allows one to describe a phenomenon (&#8220;get the right answers&#8221;) using a model that does not represent the reality (&#8220;for the wrong reasons&#8221;). 

&#8220;Whatever,&#8221; says the token engineer, &#8220;If it works, it&#8217;s good enough for me.&#8221; This view has some merit given the model is consistently able to explain independent data sets or make accurate predictions, etc. However, the token scientist however is not so satisfied: &#8220;But why these parameters?&#8221; the token scientist asks. &#8220;And why is it that parameter set of this model is not unique? Certainly there is one true storyline going on in reality!&#8221;

Kirchner amusingly remarks that being able to tune parameters in this way &#8220;violates a basic principle of mathematical modeling, namely that the constants should stay constant while the variables vary.&#8221;

He argues that overparameterization is not only a cheap way to the right answers (or worse, the &#8220;right&#8221; answers), but fundamentally hides from us the true structural and geometric information that is inherent to the phenomenon under study. &#8220;Whereas the problem of parameter identification has been emphasized in the hydrologic literature, the more fundamental (and difficult) problem of structural identification has received less attention than it deserves. Likewise, whereas many hydrologists recognize that overparameterization makes parameter identification problematic, it is less clearly understood that overparameterization also makes structural identification difficult. Parameter tuning makes models more flexible, and thus makes their behavior less dependent on their structure. This in turn makes validation exercises less effective for diagnosing models&#8217; structural problems. By making it easier for models to get the right answer, overparameterization makes it harder to tell whether they are getting the right answer for the right reason.&#8221;

Some people might say, &#8220;Oh, but what&#8217;s the harm in a little parameter tuning, Kirch?&#8221; to which the Kirch Man says, &#8220;Very little parameter tuning is still too much!&#8221; He discusses a type of hydrological study in which the data sets contain only enough information to constrain simple models with up to four free parameters, and another study that despite using detailed data sets could not constrain a six-parameter model. &#8220;Ok,&#8221; says my reader, &#8220;So just use four or five free parameters. Gah, what&#8217;s the big deal?!&#8221; The big deal, Kirchner says, is that many types of hydrological models come chock full of free parameters&#8212;dozens of them!

**Hydrologic Models**
  
From what I gather, hydrologists largely use two classes of models: lumped-parameter and spatially-distributed.

A lumped parameter model is like a circuit diagram: in reality you have a voltage applied across a copper wire made up of jazillions of atoms and used to power a light bulb (another jazillion atoms, heat, photons, and the works!), but in the lumped-parameter model you have a 1-dimensional loop that has a few symbols representing macroscopic, emergent features like resistance and a continuous current.<figure id="attachment_110" style="width: 300px" class="wp-caption alignleft">

[<img class="size-medium wp-image-110" src="http://kevin-urban.com/blog/wp-content/uploads/2015/09/lumped-parameter-model-300x187.png" alt="How do you usefully trivialize the existence of a jazillion atoms? Lumped parameters, baby!" width="300" height="187" srcset="http://kevin-urban.com/blog/wp-content/uploads/2015/09/lumped-parameter-model-300x187.png 300w, http://kevin-urban.com/blog/wp-content/uploads/2015/09/lumped-parameter-model.png 426w" sizes="(max-width: 300px) 100vw, 300px" />](http://kevin-urban.com/blog/wp-content/uploads/2015/09/lumped-parameter-model.png)<figcaption class="wp-caption-text">How do you usefully trivialize the existence of a jazillion atoms? Lumped parameters, baby!</figcaption></figure> 

Lumped-parameter models like circuit diagrams are obviously useful, but they operate on a low-resolution scale.

Spatially-distributed models seem to be just the opposite, going for tons of geographic detail and often suffering from a proliferation of free parameters due to spatial disaggregation. (&#8220;Dis-aggra-wha?&#8221; you ask? Well, when you aggregate, you lump things together, so when you disaggregate, you take them apart &#8212; you want the details, not the averages. Spatial disaggregation is also called downscaling: it is the process of mapping information from a coarse spatial scale to a finer one while maintaining consistency with the original dataset.&#8221; For the time series analysts out there, in the context of a time series, temporal disaggregation would basically be a form of upsampling&#8212;that is, going down in scale is going up in sampling rate.)

Anyway!

Lumped-parameter models can obscure the physics on smaller scales, while spatially-distributed models can obscure the broad-stroke physics at best, and tell a completely wrong story at worst. Kirchner suggests a compromise between lumped-parameter models and spatially-distributed models: quasi-lumped, quasi-distributed. Not so simple as to trivialize away all the detail, but not so detailed as to overparameterize and hide away the structure. More importantly, his mid-scale-type model must be falsifiable: it must be able to succumb to available data sets. In other words, if our data sets can only constrain four parameters, then this model should only have four parameters.

Kirchner says, &#8220;All hydrological knowledge ultimately comes from observations, experiments, and measurements&#8230; Mathematical tools can at best only clarify (and at worst, obscure or distort) the information those data contain.&#8221; He therefore argues, &#8220;In order to know whether we are getting the right answers for the right reasons, we will need to develop reduced-form models with very few free parameters&#8230; The collision of theory and data &#8230; will be more scientifically productive if we can develop models that are parametrically efficient.&#8221; Furthermore, we need to &#8220;develop model testing regimens that compare models against data more incisively.&#8221;

What types of model testing is he talking about? Well, when I have the time, I&#8217;ll tell you: for now know, the hydrologic-y technical argument might go like: &#8220;Split-sample tests bad. Differential split-sample tests good!&#8221;

**Further Philosophically-Heightened Hydrological Reading**

  * 2014: [Hydrology: A Science for Engineers](https://books.google.com/books?id=czr6AwAAQBAJ&pg=PA100)
  * 2003: Seibert: [Reliability of Model Predictions Outside Calibration Conditions](http://www.seibert-space.com/hydro/pdf/Seibert_2003_NordicHydrology_floodestimation.pdf) (pdf)
  * 2002: Beven: [Towards a Coherent Philosophy for Modeling the Environment](http://rspa.royalsocietypublishing.org/content/458/2026/2465.short)
  * 1996: Kirchner et al: [Testing and Validating Environmental Models](http://www.sciencedirect.com/science/article/pii/0048969795049711)
  * 1993: Beven: [Prophecy, Reality, and Uncertainty in Distributed Hydrological Modeling](http://www.researchgate.net/profile/Keith_Beven/publication/222481223_Prophecy_reality_and_uncertainty_in_distributed_hydrological_modelling/links/548abc5c0cf225bf669ca688.pdf) (pdf)
  * 1992: Beven and Binley: [The Future of Distributed Models: Calibration and Uncertainty Prediction](http://www.researchgate.net/profile/Andrew_Binley/publication/200472186_Future_of_distributed_models_Model_calibration_and_uncertainty_prediction/links/02bfe51463df177f92000000.pdf) (pdf)
  * 1986: Klemes: [Operational Testing of Hydrological Simulation Models](http://www.tandfonline.com/doi/abs/10.1080/02626668609491024#.VeYSXNNViko)
  * 1986: Klemes: [Dilettantism in Hydrology: Transition or Destiny?](http://onlinelibrary.wiley.com/doi/10.1029/WR022i09Sp0177S/full)

**Further Reading on Temporal Disaggregation**

  * [R vignette about temporal disaggregation](http://journal.r-project.org/archive/2013-2/sax-steiner.pdf)

&nbsp;