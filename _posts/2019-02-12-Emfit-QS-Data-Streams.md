---
title: Emfit QS Data Streams
layout: post
tags: digital-health sleep easi
---

Learning about a lot of different wearables and home sensors these days.  Notes and links clutter
my screen, sprinkled across a host of applications. Scribblings of tables and graphs charting out the 
landscape lay scattershot atop my desk, alongside printouts and to-do lists...at work...at home...and in 
my bookbag.  Madness and mental mutiny is just around the corner!

Why not make a blog post?!

Great idea: let's talk about the data available from the Emfit QS.

Note that you can learn all about these things and more at the Emfit QS website, but sometimes
scattered about.  Here, I try to condense the information from multiple sources in a "data stream"-centric view, listing
what data streams are available from Emfit QS (visually or for download), how they are defined, and why
they are important (i.e., how they map to physiological processes).  

Before we start, maybe a brief one-liner about what Emfit is and how it works:
* Emfit QS is a bed sensor. 
* "Emfit QS relies on ballistocardiography, which is a technique for producing a graphical representation of repetitive motions of the human body arising from the sudden ejection of blood into the great vessels with each heart beat."
* Check out their website: https://www.emfit.com
* Check out some publications:  https://www.emfit.com/publications



# Basic Sleep Session Parameters
* **Sleep Session Start Time**
  - beginning of sleep session
* **Sleep Session End Time**
  - end of sleep session
* **Sleep Session Total Time**
  - (duration of time in bed) + (duration of bed exits)
  - Question to Self:  Is this also equal to `end_time - start_time`?

### References
* [Best Sleep Data Tips [Part 1]](https://www.emfitqs.com/sleep-data/))


# Sleep Quantities
* **Sleep Awakenings**
  - number of times you woke up during sleep session
* **Sleep Stages**
  - divides sleep into three classes (like sleep labs do): light, deep, REM
  - provided as a time series of short-window classifications
  - Importance for tracking:  "For complete recovery of your body and mind you need to have sufficient amount sleep 
  in general, but also enough both REM and DEEP sleep. REM sleep should constitute 20-25% of your total sleep, and DEEP 
  sleep 10-20%. This leaves some 50-60% for LIGHT sleep."
* **Time Spent Awake in Bed**
  - total time (in seconds) that you were awake during night
  - bed exits are excluded
  - referred to as the "awake duration" in the data
* **Percentage of Time Spent Awake in Bed**
  - GUESSING: relative to total time in bed (wake+light+deep+REM)
* **Time Spent Asleep**
  - also referred to as sleep duraton
  - GUESSING: light time +deep time +REM time
* **Time Spent in Deep Sleep**
  - "In deep sleep physical movement and muscle tone is almost non-existent. Breath rate is slow and steady, heart 
  rate is slow and blood pressure low. DEEP sleep is essential for physical recovery and most physiological systems 
  in you are in a heightened anabolic state, showing increased production of proteins, the essential building blocks 
  needed for cell growth and repair and rejuvenation of the immune, nervous, skeletal and muscular systems."
* **Percentage of Time Spent in Deep Sleep**
  - GUESSING: relative to total time in bed (wake+light+deep+REM)
* **Time Spent in Light Sleep**
  - "Light sleep is transitionary state from awake state to DEEP sleep. Eyes are not moving, and muscle tone is decreased."
* **Percentage of Time Spent in Light Sleep**
  - GUESSING: relative to total time in bed (wake+light+deep+REM)
* **Time Spent in REM Sleep**
  - "REM sleep is dreaming phase, characterized by rapid eye movements, paralyzed muscles, and variable blood pressure 
  and heart rate. REM sleep is needed for mental recovery; during this sleep stage synaptic connections can be 
  re-organized, which enables learning, storage of memories, and forgetting unnecessary things."
* **Percentage of Time Spent in REM Sleep**
  - GUESSING: relative to total time in bed (wake+light+deep+REM)
* **Sleep Score**
  - "To calculate the sleep score, we use a combination of total hours in sleep, durations of REM and deep cycles, 
hours awake and awakenings. The more you sleep, the more REM and deep sleep you get and the better your sleep score 
is. On the flip side, the more awakenings there are and the longer you lie awake in the bed struggling to doze off, 
the worse the score is. The maximum value is 100, and values above 80 can be regarded as good." 
  - From Emfit's FAQs page: `Sleep Score = (total_duration_of_sleep + 0.5*(duration_of_REM_sleep) + 1.5*(duration_of_DEEP_sleep)) - 8.5*(0.5*(duration_awake/3600) + number_of_wakenings/15))`
  - Concerning this formula, they note:  "By this formulation Sleep Score can reach values over 100, but in this case 
  the value is truncated to a maximum of 100, which should be indication of sufficiently good sleep."
* **Sleep Efficiency**
* **Sleep Onset Duration**



### Emfit QS Sleep References
* [Best Sleep Data Tips [Part 1]](https://www.emfitqs.com/sleep-data/))
* [Why You Need to Start Tracking Recovery and Sleep](https://www.emfitqs.com/why-you-need-to-start-tracking-recovery-and-sleep/)
* [Emfit FAQs](https://www.emfitqs.com/faq/#collapseContent_)
* [Emfit QS Manual (pdf)](https://qs.emfit.com/docs/Emfit%20QS%20Wi-Fi_manual_9.2.2018_ENG_V1.10_view.pdf)

# HRV Quantities
* **HRV RMSSD**
  - time series: measured throughout the whole night in short time (3-minute) windows 
    * "We measure HRV throughout the entire night in 3-minute time windows, meaning that in one hour you’ll get 
    20 HRV measurements, and during a whole night there will be something between 140 and 180 HRV measurements."
  - RMSSD: Root Mean Square of Successive Differences
  - RMSSD is widely used time domain estimate of heart rate variability (HRV)
  - In general, HRV is an estimate of the activity of the central nervous system (CNS)
    * e.g., a higher number indicates better recovery and lower stress
  - higher values are generally better, but HRV data is overall highly individual and only comparisons
  with your own HRV measurements taken regularly over several weeks are truly meaningful
  - computed in short windows and provided as a time series for each sleep session
* **Evening HRV (RMSSD)**
  - "The evening value is similarly measured within the first 90 minutes in bed."
  - "A low evening value means that your day has been hard because of either mental stress or physical exercise."
  - Utility: "athletes can decide if their training load was sufficient by checking that Evening RMSSD is low enough"
  - provided as single number per sleep session
* **Morning HRV (RMSSD)**
  - "The morning HRV RMSSD value we use is the average of all 3-minute time window values measured during the last 90 
  minutes prior to waking up."
  - "The morning value should be higher than evening value, meaning that there has been efficient recovery and rest 
  during the night."
  - "If Morning RMSSD is back to baseline, an athlete is ready for another heavy exercise, otherwise it might be reasonable 
  to train little bit lighter on that day. If Morning RMSSD tends to remain under baseline, or there is no recovery, this 
  may be indication of overtraining syndrome for an athlete. In this case training load should be lightened for some time."
  - provided as single number per sleep session
* **Total Recovery**
  - the difference between a morning and (previous) evening HRV RMSSD values 
  - recovery is essentially an upward return to baseline, which means this number should generally be positive
    * the caveat to this is if you have no strenuous exercise to recover from, then low negative numbers may be just 
    as likely as low positive numbers
  - provided as single number per sleep session
* **Integrated Recovery**
  - the area under a sleep session's HRV RMSSD curve
  - an alternative metric of recovery to total recovery that attempts to account for recovery that 
  may have occurred during the night, which was masked by an HRV uptick due to morning stress
  - if both this number and total recovery are low, then recovery is likely insufficient and you should not
  attempt any strenuous workouts; however, if TR is low and this is intermediate or high, you might take it
  as evidence of recovery and feel confident in going ahead with a workout (again, HRV data is highly 
  individual, so build up a bit of an intution here)
  - provided as single number per sleep session
* **Recovery Rate**
  - seems to be same thing as recovery ratio
  - from the Emfit User Manual (page 9, entitled "7. Overview"):  "recovery rate (ratio between evening and morning HRV's)"
* **Recovery Ratio**
  - Definition:  Morning HRV divided by Evening HRV
  - referenced, but not defined in Emfit User Manual (e.g., on page 35 in reference to the RMSSD graph)
  - Interpretation (referenced on website [here](https://www.emfit.com/copy-of-sleep-recovery)):  
    * numbers around 1.5 should be regarded as good recovery
    * "If number is lower than 1, then there has been excessive stress or some problem with your physiological or mental system, preventing the recovery."
  - provided as a summary statistic for each sleep session
* **ANS Balance**
  - ANS: Autonomic Nervous System
  - "In our app, we define the ANS balance by measuring LF/HF (Low Frequency/High Frequency Ratio), both of which are 
  common frequency domain measures of heart rate variability."
* **High-freqency HRV**
  - AKA: sometimes just referred to as HF or HF HRV
  - Definition: "HF is the area measured in a frequency band of 0.15-0.4 Hz."
    * this is likely a band-integrated power spectral density of interbeat intervals (aka r-r intervals in ECG data)
    * cannot find what units Emfit uses, though this quantity is often reported in ms^2 (milliseconds squared)
  - Importance: "It is considered a state indicator of parasympathetic nervous systems."
  - provided as a summary statistic for entire sleep session
  - also provided as a time series of shorter windows (overlapping? not sure)
* **Low-frequency HRV**
  - AKA: sometimes just referred to as LF or LF HRV
  - Definition: "LF is the area measured in a frequency band of 0.04-0.15 Hz."
      * this is likely a band-integrated power spectral density of interbeat intervals (aka r-r intervals in ECG data)
      * cannot find what units Emfit uses, though this quantity is often reported in ms^2 (milliseconds squared)
  - Meaning: "It is considered a state indicator of both sympathetic/parasympathetic nervous systems."
  - provided as a summary statistic for entire sleep session
  - also provided as a time series of shorter windows (overlapping? not sure)
  
  
### Emfit QS HRV References
* [Best Sleep Data Tips [Part 2]](https://www.emfitqs.com/sleep-data-2-hrv/)
* [Everything About Measuring Recovery](https://www.emfitqs.com/everything-about-measuring-recovery/)
* [Why You Need to Start Tracking Recovery and Sleep](https://www.emfitqs.com/why-you-need-to-start-tracking-recovery-and-sleep/)
* [Emfit QS Manual (pdf)](https://qs.emfit.com/docs/Emfit%20QS%20Wi-Fi_manual_9.2.2018_ENG_V1.10_view.pdf)


# Heart Rate Quantities
* **Heart Rate**
  - "A healthy person should see clear pattern of the heart rate going down as the sleep deepens and up in light 
  and REM sleep."
  - this is provided as a time series throughout the sleep session
* **Average Heart Rate**
  - What they say: "The Average BPM is simply your average heart rate for the whole night."
  - What they mean: I'm assuming over total sleep session, but not sure 
  - this is provided as a summary statistic per sleep session
* **Mininum Heart Rate**
  - AKA: resting heart rate
  - "The Resting BPM, on the other hand, is the smallest 3-minute average heart rate you had during sleep. It can 
  be used as a mild indication of stress or overtraining. "
  - this is provided as a summary statistic per sleep session
  - Like any of the HRV quantities, resting heart rate is highly individual: you want to establish what is your baseline
  resting heart rate over several weeks; then track it for large deviations from that baseline
* **Maximum Heart Rate**
  - from reading their article about resting heart rate, I'd say this the largest 3-minute average heart you had during
  sleep (though I could be wrong, e.g., could be max over entire sleep session)
  - this is provided as a summary statistic per sleep session
  
### HR References
* [Best Sleep Data Tips [Part 3]](https://www.emfitqs.com/sleep-data-3/)
* [Emfit FAQs](https://www.emfitqs.com/faq/#collapseContent_)


# Breathing (Respiration) Quantities
* **Average Respiration Rate**
  - provided as single summary statistic for each sleep session
  - Typical Range: "The typical respiratory rate for a healthy adult at rest is 12–20 breaths per minute."
  - Importance:  "Respiration rates may increase with medical conditions, such as fever or illness."
    * In fact, for the flu, they state elsewhere that this can begin occurring several days before you notice any strong
    symptoms
* **Maximum Respirate Rate**
  - provided as single summary statistic for each sleep session
* **Minimum Respiration Rate**
  - provided as single summary statistic for each sleep session
* **Respiration Rate**
  - provided as time series


### Respiration References
* [Best Sleep Data Tips [Part 3]](https://www.emfitqs.com/sleep-data-3/)
* [Emfit QS Manual (pdf)](https://qs.emfit.com/docs/Emfit%20QS%20Wi-Fi_manual_9.2.2018_ENG_V1.10_view.pdf)



# Movement (Activity) Quantities
* **Number of Bed Exits**
* **Duration of Bed Exits**
* **Average Activity Measure**
  - "Avg Activity measures larger movements than those caused by heart beating and respiration, such as twitching leg 
  or arm or changing position only slightly."
  - From what I can tell: This takes into account intermediate-level motion in general; movement not quite large enough
  to be considered a "toss" or "turn" 
  - this quantitiy is referenced on page 36 of Emfit's user guid ([Installation &
Operating Instructions](https://images-eu.ssl-images-amazon.com/images/I/B1Mgem5LdiS.pdf))
* **Tossing and Turning (Activity)**
  - number of tosses and turns throughout the sleep session
  - I refer to this as "restlessness" in my notes, which seems to be congruent with Emfit: "The movement data gives an indication of how **restless** your sleep is."
  - count provided as a total (over sleep session); called `tossnturn_count` in the data
  - count provided as a short-windowed time series; called `activity` in the data

### Activity References
* [Best Sleep Data Tips [Part 3]](https://www.emfitqs.com/sleep-data-3/)
* [Emfit QS Manual (pdf)](https://qs.emfit.com/docs/Emfit%20QS%20Wi-Fi_manual_9.2.2018_ENG_V1.10_view.pdf)


# Closing Remarks
Note that, as of this writing (Feb 2019), they haven't updated their [blog](https://www.emfitqs.com/blog/) since
August 2017...which means I'm a bit late to the game on this one, and makes me wonder: am I too late?  That is,
if they aren't pushing their brand like crazy, is it because it was a flop?  We have some at the lab, but (i) I haven't
used it yet myself, and (ii) I think it was purchased quite a while back.  


# Further Reading on Things & Stuff
* [Heart Rate Variability – How to Analyze ECG Data](https://imotions.com/blog/heart-rate-variability/)
* 2017: Shaffer and Ginsberg: [An Overview of Heart Rate Variability Metrics and Norms](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5624990/)
* 2017: Roseberg et al:  [Resolving Ambiguities in the LF/HF Ratio: LF-HF Scatter Plots for the Categorization of Mental and Physical Stress from HRV](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5469891/)
* [Sleep Apnea Index](https://www.webmd.com/sleep-disorders/sleep-apnea/sleep-apnea-ahi-numbers)
