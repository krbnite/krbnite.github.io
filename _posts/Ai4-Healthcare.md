---
title: Ai4 Healthcare
layout: post
tags: healthcare machine-learning deep-learning easi
---


# 9:00: AI and The Art of Human Care (Hassan Tetteh, DoD Joint Artificial Intelligence Center)
Heart transplants.  

Human care theory: a theory of comprehensive care that advance each person's total health in body, mind, and 
spirit that challenges
the current healthcare system.  HCT consideres the current healthcare systemto be not patient focusesed -- but 
instead provides fragmented, inefficient, unnecessarily expensive healthcare.  

What is the JAIC?  The center of AI excellence for the DoD.

This talk is basically a sales pitch for JAIC initiatives... Building partnerships.

He did have the insight to know that really getting any ML/AI pipelines to be useful, you 
need a huge team of related skill sets (security, DevOps, software engineering).  

5 Product Lines at jAIC
* Health Records Analysis
* Medical Imagery Classification
* Automated Medical Logistics
  - Successful implementation of Unmanned Aircraft Use for Delivery of a Human Organ for Transplantation
* Suicide Prevention
* Point-of-Injury Treatment Support

His major advice: you really, really need to develop partnerships to be successful.  Basically, you
can't know everything!

"A ship in port is safe, but this is not what ships are built for. Sail out to the sea and do new things."


# 9:25: Solving Genomic Data Privacy in the Age of AI (Kyle Knack, Pure Storage)
DNA Data Deluge: The amount of genomic data is increasing exponentially, keeping pace with the exponential decreases
in sequencing costs.

How do you keep genomic data private?  

How do you anonymyze DNA data since it is inherently personalized?  In other realms, like medical imaging,
one can hope to anonymize patient data...but this is not necessarily true for DNA data.  But current
supervised methods require lots of data, and we likely need to share DNA/genomic data.  

Paper reference:  "Better medicine through machine learning: What's real and what's artificial?"

Irration Extrapolation:  Can we reasonably assume that an algorithm trained on 
an eas-to-obtain patient set will lead to accurate models that act in the best interests
of patients inside and out of that patient set?  How do we correct for such biases and reason
about disease/symptoms severity and trajectory?

How doe we share highly confidential data?  Enter federated learning:  
* models are DL'd locally and trained w/ local data
* the updated trained model is then shared and other cloud-connected models
  are updated 
* this process does not require that private data is shared offsite or exposed

Example of this:  Google Gboard.  

Example:  TensorFlow Federated Framework ("...train across data from multiple sources, all 
while keeping each of those sources separate and local...").

Challenges to Federated Learning: 
* combining separate models risk creating a master model that's worse than each of its parts
* for hosptial use cases, it requires every interested hospital to have the necessary tech infrastructure
  and personnel in place (e.g., folks who can train the local model)
  



# 9:50: Building an AI Future that Benefits Patients and Doctors (Christopher Khoury, AMA)
People are most worried about healthcare-related issues (e.g., unexpected medical bills and health
insurances costs rank in the top 2).


Paper reference: The healthcare claims adjudication process in the United States - a picture 
is owrht a thousand words.... This
paper has a figure that shows the trajector of a patient in the healthcare system (hint: it's friggn' 
crazy).

Burnout is 2x more common in physicians (48%) than the general population (24%)

For digital health:  Can is make something better, quicker, or smoother, so that the
patient or doctor can benefit?

Augmented Intelligence: an alternative conceptuatlization that focuses on AI's assistive role, 
emphasizing the fact that its design enhance HI ranther than rpelaces it.


Paper reference: Current state and near-term priorities for ai-enable diagnostic support software in healthcare

Paper reference: how to develop ml models for healthcare
  - this is literally the type of paper I was hoping to write for the dropout stuff I was doing

Showed a slide about ~30 different definitions of accuracy, which showed words like accuracy, PPV,
precision, recall, F1 score, AUC, etc.  My question: Haven't people always dealt with these terms,
even for basic studies?  That said, point is:  clinicians and decision makers do not always know these 
terms, and the speaker is wondering how to simplify the terminology for better discussion between
AI stakeholders...

Some uh-ohs
* IBM's Watson supercomputer recommended 'unsafe and incorrect' cancer treatments
* Automating Inequality (some algorithm was unfarily rejecting black people from care)

Black box intepretability: Important!


# 10:15 Business of AI: What Healthcare Could Look Like in 10 Years (Deepa Varadharajan, CB Insights)
Healthcare industry has consistently seen the most demand for AI, especially over the past 5 years (finance
is a close second).  

So many players in the game:  oldies (GE, Philips, Siemans) and newbs/tech (Google, Apple, etc) and
unusuals (Epic, Cerner, Roche).

Unmanned health clinics.

Google, Amazon, and Apple are jumping in deep for both healthcare and AI.

Google:  Verily Calicao CapitalG DeepMind

Holdbacks
* data silos & outdated infra
* feat of regulartory risk and untested tech
* slow to react
* companies don't knwo where to start

Motivations
* if you don't, someone else will
* appear to a change demography and customer base
* improve margins and generate additional revenue streams

Trends to watch in healthcare
* bigger companies are partnering w/ (or acquiring) smaller startups
* bull case for OEMs:  manufacturer will become an end-to-end service provider
* Will Google takeover the hospital?
  - they usually have a smart approch
    * Android: did not try to creat a new phone, just a new OS (ecosystem approach)
    * Waymo: did not try to reinvent the wheel, just acquired
  - they have tech and capabilities that no competitor has
    * data access and expertise
    * global reach
    * disposable cash for research, experimentation, failing
  - Focused on many diseases right now
    * diabetic retino....
    * more exmples
    * vitual diabetes clinic
    * Verily is building a hihg-tech campus to treat opioid addiction
  - one of the 3 major cloud services providers (Google Cloud Platform)
    * contributes to modern healthcare IT needs
  - purhcased Fitbit
  - Verily in general
    * patent fo smart syring
    * patent for verb surgical, a robotics startup 
    * patent grant for digital atery blood pressure monitor
  - Citiblock health
  - Smart City project w/ Smart Healthcare initiatives

Basically, Google will likely be a major player in all areas of healthcare and smaller business should
think about how they can form a symbiotic relationship with that so they can provide a value-added service, etc.
    


# 11:05, Hub2: Deep Neural Networks Improve Radiologists' Performance in Breast Cancer Screening
Looking at screening mammography for breast cancer, the goal is "no cancer", "benign, "malignant."

Challenge: not a lot of data just laying around!
Comparing public data set for screening mammography to ImageNet:  14M images vs 10k images.  So the
folks at NYU created their own data set (speaker spent at least half of year preparing and cleaning
the data set) -- 1M images.
* His technical report:  The NYU Breast Cancer Screening Data Set

1M images
* each image is at least 2290x1890 pixels
* 220k examps of over 140k patients
* ~96% of exams are negative w/o pathology
  - 0.5% exams contain malignancy


Challenge:  image resolution.  Think of ImageNet again -- 256x256 pixel images, whereas the
screening mammography images are ~2kx2k.  

* End-to-End Solution:  Custom ResNet to rapidly downscale the resolutions.  
* Some manual points:  cut out relevant parts of image...

Not a lot of events (0.5%).  So they use transfer learning by training on a task with more events,
then re-purposing for the task of interest.  Here, every mammogram is accompanies by an initial screening assesmment of BI-RADS...which are they were able to train on before transfer.

Challenge: multi-modal output.  How to integrate information between views?  

For each image, resnet22 generates a 256d vector.. How to combine?
* combine all images for both breasts
* combine same image types from each breast
* use each image separately...
* etc

Some stuff:
* BI-RADS: 15 days on 4 Nvidia v100 GPUs
* Generating heatmaps:  1k hours on single v100
* breast cancer classification: 1 day

Comparison to Human Performance
* 360 exams w/ biopsy, 360 negative exams
* 14 readers
* radiologist asked for a prediction of probability of malignancy
* Model AUC 0.876;  individual reader AUC 0.778;  avg of ensemble of radiologist 0.895;  model + 1 radiologist 0.891; model + ensemble of radiologist 0.909





# 12:05, Hub2: Explainable AI for Training with Weakly Annotated Data


# 1:35, Hub2:  What AI Will Bring to Medicine, and Why Human Experts are Here to Stay


# 2:10, Hub2:  Best Practices in Applied Deep Learning for Digital Pathology Multiplex Image Analysis












# Connections / Folks I met
* Emma Wypkema
