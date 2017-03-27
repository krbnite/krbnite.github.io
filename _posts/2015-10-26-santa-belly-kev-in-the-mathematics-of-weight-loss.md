---
id: 639
title: 'Santa-Belly Kev in&#8230; The Mathematics of Weight Loss'
date: 2015-10-26T16:49:50+00:00
author: ogkubl
layout: post
guid: http://kevin-urban.com/blog/?p=639
permalink: /index.php/2015/10/26/santa-belly-kev-in-the-mathematics-of-weight-loss/
categories:
  - Uncategorized
---
It just so happens that I&#8217;ve fattened up quite a bit over the past month.

Not to say that I&#8217;m back to Ol&#8217; Santa-Belly Kev (&#8220;Hello folks, Santa-Belly Kevin here lying through my teeth.&#8221;), but in the jargonese of a mathematical biologist, one might say I&#8217;ve become rather &#8220;adipose&#8221; as of late. (That means fat.)

Alas, all my pants seem to be shrinking due to the &#8220;**Quasi-Conservation of Bad Decision-Making**&#8221; [QCBDM] theorem, which tells us, for example, that if you decide to work harder towards your goals (I&#8217;ve been punching out 10-15 hours days trying to complete my dissertation), then you must eat comfort food en masse.

Coincidentally, [Diana Thomas](http://www.montclair.edu/profilepages/view_profile.php?username=thomasdia) from Montclair State will be speaking at NJIT this coming Tuesday about &#8220;guiding patient weight loss using objective measures.&#8221; That is, she is here to say that cold, hard facts (measurements) combined with icier, diamond-hard mathematics is more reliable than patient honesty (&#8220;I swear I stopped eating all those ice cream-cheeseburgers!&#8221;).<!--more-->

**Reader Beware**: A_lthough I reference [Thomas et al [2012]](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3771367/) throughout in establishing observational facts, all my talk on the &#8220;theory of bad decision-making&#8221; does not reflect the mathematical elements developed in that article at all. The actual article is more sophisticated, practically-focused, and well-thought out than my ramblings, which are meant to entertain more than anything. _

**The Basics of Weight Loss
  
** In order to lose weight, the [first law of thermodynamics](https://en.wikipedia.org/wiki/First_law_of_thermodynamics) suggests that one can either reduce their energy intake, increase their energy expenditure, or both:

$$ E\_{stored} =  E\_{intake} &#8211; E_{expended} < 0 $$

In colloquial terms, for best results, this equation chides you to &#8220;stop eating so much&#8221; and to &#8220;get out of the dang house and exercise already!&#8221; More eloquently put, one might refer to this as combining a low caloric diet with exercise. It&#8217;s really a nice equation, actually. Hell, it&#8217;s the first law of thermodynamics.

But the first law of thermodynamics is bunk!

Well, you know&#8212;not really &#8220;bunk&#8221; per se.  But if we are to believe in the QCBDM theorem, then we must admit that losing weight isn&#8217;t as easy as &#8220;eat less&#8221; and &#8220;exercise more.&#8221;

This is because, according to the good &#8216;ol human need to conserve bad decisions, if one exercises more, then one is going to eat more nachos \(\left(E\_{intake} \sim E\_{expended} + \epsilon_{noise} \right)\). That&#8217;s a law embedded into the fabric of the universe, circumvented only by a focused beam kinetic discipline-energy! Conversely, the theorem tells us that if we eat healthier, we&#8217;re going to probably stop going to the gym&#8212;because who has the mental capacity to (i) focus on not eating nachos, and (ii) lift a weight? This is another universal force not easily swayed.

Controlling for exercise alone isn&#8217;t the answer! This is the premise of [Thomas et al [2012]](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3771367/).  Weight loss associated with exercise intervention tends to be lower than predicted, likely due to weak or invalid assumptions of the predictive model. To better understand the role exercise plays in weight loss, [Thomas et al [2012]](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3771367/) systematically review previous studies that measured exercise-induced change in body composition in patients given compliance-to-exercise prescriptions.

**QCBDM Violation #1:  Constant Intake, Increased Expenditure
  
** If your dietician is planning an exercise-only routine for losing weight, they might tell you to keep a calorie log for a week or two before putting you on the exercise program, which can then be used to represent \(E\_{intake}\) as the daily average, \(\bar{E}\_{intake}\).**
  
** 

However, this has one fatal flaw: the assumption is that a patient will not overeat nachos (as per the law of the universe) after vigorously exercising for an hour. The dietician has assumed constrained eating while not actually controlling for constrained eating (e.g., by enforcing a calorie cap). This gives the patient a license eat at will!

Just as the speed of a free particle determines its kinetic energy (\(E\_{k}=\frac{1}{2}mv^{2}\)), the quasi-conservation of bad decision-making tells us that the amount of food one smashes to their face (\(E\_{intake}\)) is strongly dependent on how much energy they expend during the day: \(E\_{expended} = E\_{exercise} + E\_{gen}\), where \(E\_{gen}\) takes into account all other energy expenditures aside from exercise.

In fact, idealized QCBDM theory tells us that the patient will eat just about enough to make up for any energy expenditures:

$$ E\_{intake}=E\_{expended} $$

That is, QCBDM tells us that a &#8220;free patient&#8221; (that is, a patient with no external constraints, like a free particle) has only one degree of freedom between \(E\_{intake}\) and \(E\_{expended}\). If this is true, then the notion of a free patient is incompatible with the condition that \(E_{intake}=CONST\) before and during an exercise program. Such a possibility does not describe a free patient, but a patient with  restraint.

To understand a the dynamics of a restrained patient, let&#8217;s refer again to the analogy with a free particle.

In order to be able to vary a free particle&#8217;s kinetic energy without varying its speed, one must allow a new degree of freedom to enter the equation. In this case, there is an obvious naturally-constrained parameter to free up: the mass of the particle. Similarly, to be able to vary \(E\_{expended}\) without varying \(E\_{intake}\), a new degree of freedom must be allowed for:

$$ E\_{intake}=(1-\rho) E\_{expended} $$

The new parameter, \(\rho\), is what I call the coefficient of restraint. This coefficient parameterizes one&#8217;s energy intake between two extremes: no restraint (QCBDM theory) and full restraint (celibate monk theory). Restraint is necessary to overcome the quasi-coservation of bad-decision making.

The coefficient of restraint typically varies between 0 and 1, but to allow for the anti-restraint phenomenon of overeating, one may include negative values as well. (Whoa, my cartoonized nutrition theory is starting to get serious! :-p)

**QCBDM Violation #2:  Everyday life remains constant upon the addition of exercise
  
** &#8220;Non-exercise activity&#8221; is another hidden, potentially confounding variable.  The idea is that one might be tempted to estimate \(E\_{expended}\) by monitoring a patient&#8217;s exercise activity alone. However, in reality \(E\_{expended}\) is also dependent on non-exercise activities:**
  
** 

$$ E\_{stored} = E\_{intake} &#8211; \left( E\_{exercise} + E\_{walkDog} + E\_{cleanHouse} + E\_{etc} \right) $$

If these activities remained constant, differencing the stored energy equation day-to-day would remove the terms and they would not affect the conclusions. However, without additional restraint parameters, QCBDM theory would suggest that additional exercise would result in shirking some responsibilities. It seems though that a patient naturally has restraint in the realm of non-exercise activity: for example, [Thomas et al [2012]](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3771367/) were unable to fully derive an inverse relationship between \(E\_{exercise}\) and \(E\_{gen}\). They were able to surmise that if a relationship exists, it is small&#8212;that increases in exercise activity does not promote large changes in non-exercise activity.

**Time to Get Serious: Above and Beyond Bad Decision-Making**
  
What happens if there is a biologically-adaptive response that threatens the results of a seemingly-reasonable exercise program conducted in combination with an isocaloric diet?

Translated: even when practicing restraint, your body does \*not\* consider \(E\_{intake}\) and \(E\_{expended}\) to be independent variables. This is likely because, if they were, you could starve yourself to death pretty easily&#8212;and your body hedges against that!

By increasing \(E\_{expendend}\) while keeping \(E\_{intake}\) constant, the results of [Thomas et al [2012]](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3771367/)  suggest that \(E\_{stored} =  E\_{intake} &#8211; E_{expended}\) does not linearly decrease, but instead has been found to remain constant, diminish only slightly, or possibly even increase slightly. If we assume the patient is properly restraining themselves, then this variation must come from somewhere else: they argue it is a physical adaptation. To equationize this, I will call upon the coefficient of adaptation, \(\alpha\):

$$ E\_{intake}= (1-\rho+\alpha) E\_{expended} $$

This effect was found to be more emphatic in leaner individuals, suggesting that weight loss is hyperbolically-dependent on fat content: as you become more lean, you lose less and less weight for the same amount of exercise and energy intake:

$$ E\_{intake}= \left(1-\rho+\frac{\alpha}{L}\right) E\_{expended} $$

&nbsp;

**Conclusion & Final Equation (for now)**
  
I have to get back to working on my dissertation (and restraining myself from drinking Mountain Dews), but this has been a fun excursion!

$$ E\_{stored} = E\_{intake}-E\_{expended} \\ \quad\quad\quad\quad\quad\quad = \left(1-\rho+\frac{\alpha}{L}\right)E\_{expended}-E\_{expended} \\ \quad\quad= \left(\frac{\alpha}{L}-\rho\right)E\_{expended} \\ \quad\quad\quad\quad = \left(\frac{\alpha}{L}-\rho\right)(E\_{exercise} + E\_{gen})$$

If this equation is true, it tells us that, in order to lose weight, our restraint must beat out our body&#8217;s adaptation! However, the body&#8217;s adaptation is likely a function of restraint (caloric restriction), so it is not so straightforward.

And this equation does not even take into account the type of food you put in your body: 1 nacho calorie is tastier than 1 calorie of kale, but it is also 1000x worse, give or take a factor of 1000. (Reference: _Charlie McGee, &#8220;Random quips about food that sound like they must be true,&#8221; The Journal of Things People Say, Vol. 10, 2015._)