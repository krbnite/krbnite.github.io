---
title: Classifying Circumstances of Missing Data 
layout: post
tags: missing-data causal-inference data-science easi
---

When is data missing completely at random (MCAR)? Or conditionally at random (MAR)?  Or not 
missing at random (MNAR)?



Scenario 1: Patients are provided with two 10-item questionnaires over the course of a year
to help assess their heart health; a health score is assigned to each.  Aside from the 
answers provided and associated health scores, the clinic collects demographic information 
on the patients, such as age, income bracket, and health insurance provider.  


Scenario 1a: Data is provided to the analyst as an outer join between the two data sets, 
resulting in rows of data, where each row represents a patient and depicts their demographic information (if
available) as well as their responses to the questionnaire (if available).

It is found that missing questionnaire data can largely be explained by age: generally speaking, young
patients have no questionnaire data because they were not flagged as needing to fill it out.  After
stratifying by age, we find that there is further dependedence on health insurance provider: patients
with no health insurance are less likely to have filled out the questionnaire. We would
say this missing data is (conditionally) missing at random (MAR). 


Scenario 1b:  Data is provided to the analyst as an inner join between the demographic and questionnaire
data sets.  In other words, we now are looking at the population of patients that have been flagged
as questionnaire recipients.  

Some patients that are flagged as having filled out a questionnaire do not have any data recorded for one
or both questionnaires.  After some investigation, it is found that every time its Chris' turn to input the data, there 
are several missing data files because Chris is lazy and doesn't know WTF he's doing.  We would say that this 
data is MCAR. 

We also find the number of men and women filling out the questionnaire is about equal, even in the
older age brackets, which does not match the statistics for those who die from heart attack.  It is unclear
if our patient population accurately reflects the true population, e.g., are their less older men in our
patient population because the dead don't go to the doctor, or because these men are already on
heart medication and don't need to fill out the questionnaire?  ... MNAR...?

MNAR:  We also find that it appears more older men are missing the responses and health score for the second 
questionnaire, which feels very MAR...but we also notice that the health scores available are suspicously high,
and so we investigate and find that the missing health scores for the second questionnaire are highly associated
with older men who have died of heart failure.  In other words, the missing health scores were MNAR: the unobserved
health scores were exceedingly low (like "0" for no health at all).  

It's weird, right?  One the one hand, it feels ok to call the missing data MAR... But the data are missing because
the heart health became so low that the patient ceased to exist, thus the data are missing based on the values
of the missing data........so MNAR, right?
