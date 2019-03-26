---
title: No Market, No Cry
layout: post
tags: data-science marketing wwe
---


Imagine that the Marketing Team at your company is designing an email campaign to ~1-2 million customers. Their goals 
are to increase customer retention and decrease subscription cancellation. Your data science team has been
asked to help Marketing achieve these goals and, importantly, to quantify that success.  How would you do this?

Generally, you want to follow 3 steps:
* formulate and deploy a multi-campaign testing strategy
* quantify the results using appropriate statistical techniques
* deliver insights and recommendations to Marketing

Let's look at these in more detail!


The Testing Plan (Experimental Design, KPIs, Etc)
===================================================
1. Formulate a well-defined, testable hypothesis:  Emailing our customers a 20% discount increases 
likelihood that they will make a purchase within one week
2. Control for natural variation over time and customer base by splitting customer base into test 
and control groups. However, more than one test group is required to properly assess our 
hypothesis and determine cause and effect.  We must also control for each variable within 
statement; this includes engaging via email, engaging via an email with discount, and engaging 
via email specifically having a 20% discount (we hold the one time constraint of one week 
constant).  For simplicity, split 1M customers evenly into groups of 250k:
  * G1: Do not send email
  * G2: Send an email 
  * G3: Send an email with 10% discount
  * G4: Send an email with 20% discount 
3. Individual Measurements:  
  * Did customer open email?
  * Did customer make a purchase within one week?
  * Optional: There are other measurements that can be made, such as, "Did the customer 
click on link in the email?"  However, the two measurements listed above are directly 
relevant to our hypothesis and can be used to determine the success or failure of the 
email campaign. (Actually, as a first cut, we really only need to compare purchase rates 
between groups.)
4. Corresponding Group Statistics:
  * Open rate
  * Purchase rate

Test Statistics
==============================================================

1.	We can bootstrap the purchase rate in each group, then use the 1st and 99th percentiles to 
establish 98% confidence intervals.  For a detectable effect, confidence intervals from one group 
should not overlap with that of the second group.  Bootstrapping is nice because it is 
nonparametric, assuming nothing about the data distribution.  However, bootstrapping can be 
computationally demanding on large data sets (though as a one-time computation not needing 
to run continuously in a production environment, this is not a huge deal).
2.	We can compute 2-sample t-tests between two groups.  Pro: well known.  Con: Quite a few 
assumptions, the most pernicious being assuming normality of the data distribution (not the 
case for many types of measurement); that said, works well enough most of the time and due to 
its popularity should probably be used instead of bootstrapping when communicating results if 
the results from both tests are similar.

Insights and Recommendations
=================================================================

We would inform the marketing team the statistically significant increases found in our study:
*	Did sending an email w/ no discount (G2) meaningfully increase the likelihood of a customer 
making a purchase within the next week when compared to the group who were not sent an 
email (G1)?  
*	Did sending an email w/ a 10% discount (G3) generate any lift over the next week compared to 
the group who were only sent an email without a discount (G2)?
*	Did sending an email w/ a 20% discount (G4) generate any lift over the next week compared to 
the group who were only sent an email with a 10% discount (G3)?

One can imagine a case where sending the email generated a significant, meaningful lift over the next 
week, while including discounts did not generate any further lift.  In this case, my recommendation 
would be to simply send our customers emails to keep them engaged.

Similarly, one can imagine a case where sending an email does not significantly increase the likelihood of 
a purchase over the next week, but sending an email with a 10% discount does.  In this situation, if the 
20% discount generates the same relative lift, my recommendation would be to not go beyond 10% 
discount and also test whether 5% would work just as well.  If the 20% discount generates even more lift 
than the 10% discount, my recommendation would be to assess whether the additional lift is significant 
enough to warrant choosing 20% discounts over 10% discounts in terms of short- and long-term 
profitability.  
