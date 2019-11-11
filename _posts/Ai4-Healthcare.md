---
title: Ai4 Healthcare - Day 1
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
  



# Building an AI Future that Benefits Patients and Doctors

# Business of AI: What Healthcare Could Look Like in 10 Years

# 11:05, Hub2: Deep Neural Networks Improve Radiologists' Performance in Breast Cancer Screening

# 12:05, Hub2: Explainable AI for Training with Weakly Annotated Data


# 1:35, Hub2:  What AI Will Bring to Medicine, and Why Human Experts are Here to Stay


# 2:10, Hub2:  Best Practices in Applied Deep Learning for Digital Pathology Multiplex Image Analysis












# Connections / Folks I met
* Emma Wypkema
