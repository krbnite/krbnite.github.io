---
title: Ai4 Healthcare
layout: post
tags: healthcare machine-learning deep-learning easi
---

Some notes and anecdotes about my experience at the Ai4 Healthcare conference in NYC, Nov 11-12.

# Day 1

## 9:00: AI and The Art of Human Care (Hassan Tetteh, DoD Joint Artificial Intelligence Center)

The opening session started off with sirens.  

The first speaker was announced and suddenly this booming
audio filled the room.  A video of ambulances and ERs played on screens from all sides.  A heart
was to be delivered, a transplant to be made.  

It was a pretty exciting opening, despite the rest of the talk having the vibe of
an insurance agent giving a Ted Talk.  The overall jist was basically a sales pitch for JAIC initiatives
and maybe a beacon for building partnerships.

What is the JAIC?  JAIC is the [Joint Artificial Intelligence Center](https://dodcio.defense.gov/About-DoD-CIO/Organization/JAIC/) -- it is an "AI center of excellence" for the DoD.

The speaker, Hassan Tetteh, promoted a vision of comprehensive care.  One that personalizes to each 
individual, idiosyncratically advancing a person's total health in body, mind, and 
spirit.  Importantly, this patient-centered vision is in contrast to 
the forces driving the current healthcare system, which culminates in an incoherent patient
experience that is fragmented, inefficient, unnecessarily expensive.  

To push this patient focus forward, the JAIC has 5 major product lines:
* Health Records Analysis
* Medical Imagery Classification
* Automated Medical Logistics
* Suicide Prevention
* Point-of-Injury Treatment Support

One insight the speaker had that I can certainly relate to is this:  to really extract practical value out ML/AI and develop
products that extend beyond proof-of-concept, one needs more than a few data scientists or machine learning engineers; to
be successful, one requires a strong, diverse team of skill sets spanning the tech spectrum (security, DevOps, 
software engineering).  His major advice concerning this is that developing partnerships is essential for success:
basically, you can't know and do everything yourself!

He ended with an interesting quote from Grace Hopper:  "A ship in port is safe, but that is not what ships are built for. Sail out to sea and do new things."


## 9:25: Solving Genomic Data Privacy in the Age of AI (Kyle Knack, Pure Storage)
Kyle wore a bright, blazing orange shirt, like the cones and caution signs you might find on
a construction site.  His demeanor fit the look: a rugged, Jersey-like aura.

The talk jumped right into the intricacies of DNA as data: How do you respect patient
privacy and HIPAA when the data under discussion and distribution is genetic?  In healthcare,
data de-identification is basically a given.  For example, given medical imaging data, one
can at least conceive that the patient data can be anonymized.  But DNA data?  A patient's
sequenced genome?  How do you de-identify a sequenced genome?

The de-identification of DNA is an issue:  supervised methods require lots of data, and so
for genomic models -- lots of genomes!  And these days, the data is there, damn-nigh
ready to share:  the amount of genomic data is increasing exponentially every year, keeping pace with 
the exponential decreases in sequencing costs.  

The parochial point of view is: "Oh well, let's just deal with what we've got."  So say you've
obtained the research rights to 10 genomes, or 100 genomes...  But is that enough?  Can you
develop a great machine learning model from so little?  This apathetic attitude can land
one in the precarious position of "irrational extrapolation."  Can we reasonably assume that an algorithm trained on 
an easy-to-obtain patient set will lead to accurate models that act in the best interests
of patients inside and out of that patient set?  How do we correct for such biases and reason
about disease/symptoms severity and trajectory?


Somehow, some way: we need to move beyond this apathetic approach; we need be able to share 
DNA/genomic data, while respecting patient privacy.  

**How doe we share highly confidential data?**

Enter federated learning:  
* models are downloaded locally and trained w/ local data
* the updated trained model is then shared and other cloud-connected models
  are updated 
* this process does not require that private data is shared offsite or exposed

Examples of this:  
* [Google Gboard](https://en.wikipedia.org/wiki/Gboard)
* [TensorFlow Federated](https://www.tensorflow.org/federated)  
  - "...train across data from multiple sources, all while keeping each of those sources separate and local..."

Challenges to Federated Learning: 
* combining separate models risk creating a master model that's worse than each of its parts
* for hosptial use cases, it requires every interested hospital to have the necessary tech infrastructure
  and personnel in place (e.g., folks who can train the local model)
  
  
### References in Talk
* 2018: Saria et al: [Better medicine through machine learning: What's real and what's artificial?](https://journals.plos.org/plosmedicine/article?id=10.1371/journal.pmed.1002721)
* [Google Gboard](https://en.wikipedia.org/wiki/Gboard)
* [TensorFlow Federated](https://www.tensorflow.org/federated)



## 9:50: Building an AI Future that Benefits Patients and Doctors (Christopher Khoury, AMA)
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


## 10:15 Business of AI: What Healthcare Could Look Like in 10 Years (Deepa Varadharajan, CB Insights)
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
    


## 11:05, Hub2: Deep Neural Networks Improve Radiologists' Performance in Breast Cancer Screening
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





## 12:05, Hub2: Explainable AI for Training with Weakly Annotated Data
Image-to-caption for medical images ("An irregular, non-homogeneous mass with indinstct margins").

Differences between cats and chest x-rays:

| Cats | Chest X-Rays |
|-------|-------------|
| any orientation, pose, location, bg, occlusion, etc | consistent orientation, pose, location, bg, anatomy, etc |
| Google images - a near-infiite supply | Need consent, de-id, privacy, limited availability |
| Lots of annotion - crowdsourcing, CAPTCHA, etc | Limited annoation - med experts, time consuming, subtle findings, inter-rater disagreement |
| lower stakes for accuracy | life or death stakes - need for explainability, FP, TN, high acc |

Ground truth annotations for iamges:  
* ground truth bounding box vs predicted bounding box (e.g., draw box around stop sign)
  - usually good enough for low stakes purposes
  - not fine enough for some medical purposes (though ok for some)

50% of medical images are chest X-rays.  These can be re-purposed to do all kinds of things, e.g.,
someone might come in for a fractured rib and leave knowing unexpected critical findings about something
unrelated.

Data:  MIT published 350k chest x-rays to help develop AI models.  Downside: no labels for all things
of interest.  The images do come w/ radiology reports.  (Ref: TieNet: Text-Image Embedding Network for
Commoon Thorax Disease Classification and Reporting in Chest x-rays.") . Can extract text and associate
with image...  This can lead to a global labeling of the image, which the speaker considers
"weak annotation" -- stronger annotation would be localizing in the image the label's associated region...

Ref: tCheXNet: Detecting Pneumothorax on Chest X-Ray Images using Deep Transfer Learning

Develop nets that classify and localize simultaneously.  For example, vanilla convnets will just classify,
but adding saliency maps allows one to localize as well.

Ref: diagnose likea radiologist: attention guidenedd conv nueral networks for thorax disease classifcaition...

Sometimes the saliency maps are too coarse-grained though...this is b/c original images are often hugely
downsampled during classification process, and saliency map usually generated from that then upsampled...

Multi-instance learning.  Weakly-labeled key example:  3 sets of keys, only 2 of the sets have the key to get 
in the secret room; audience able to identify that it's the green key.  Now generalize this to chest
X-rays: train w/ 3 images at once -- 1 healthy individual, 2 unhealthy...  This helps localize where
the label is coming from in an image.

Ultimately, they looked at CNN (just label prediction; 0.96 AUC), U-Net (label prediction + segmentation; 0.92), and MIL network (label prediction + bounding box; 0.93 AUC).

Applied network to other data sets as well (e.g., Pneumonia data set from Kaggle competition).

Summary: use methods that jointly classify and localize, which does not require local annontations; this
can provide an interpretable end-to-end deep net... these techniques are transferrable to many problems,
and can even exted to multi-class cases.

evan.schwab@philips.com


## 1:35, Hub2:  What AI Will Bring to Medicine, and Why Human Experts are Here to Stay (Hakima Ibaroudene, Southwest Research Institute)

Lots of potential for AI in supporting doctors and ERs, not nececssarily replacing them.

SwRI basically had a lot of experience in computer vision appications related to autonomous
vehicles.  They decided to make a pivot: can we use this same machinery for medical imaging
applications?

BreastPathQ Challenge: a case study for AI and human experitise. SwRI & UT Health San Antonio place
1st among 100 submissions.
* 2400 images


The machine is good at never getting board and getting things mostly right (millions of parameters),
however doctors are better at the edge cases.  Developing a better model was a cyclic process, 
involving experts routinely... 

Questioner:  A problem w/ an algorithm is that a mistake can hurt many, whereas if a clinician makes
a mistake, only one is harmed...  He said it more eloquently.  




## 2:10, Hub2:  Best Practices in Applied Deep Learning for Digital Pathology Multiplex Image Analysis (Moshe Safran, RSIP Vision)
Mutliplex Image Analysis Tasks:
* Nuclear detection and segmentation
* Tumor segmentaton
* Key marker segmentation
* Cellular Colocalization (cell classification)
* COnfiguration and merging of all results into single cohesive output
  - Output: image that has nuclei segmentation, cell classification, tumor segmentation, and key marker segmentation
  - Very cool

### Nuclear Detection using DL
Segmentation challenges for nuclear morphology
* variation in shape, size, intensity
* merging and overlapping nuclei

Classical tehcniques requires works
* thresholding (local, adaptive)
* gen purp seg - e.g., meanshift, watershed, etc
* feature engineering
* etc...

Approach: detect, propose region, instance segmentation.  

Input image -> Cell center detection -> Nuclie instance segmentation -> Integration

Mask-RCNN like instance segmentation using U-Net Encoder-Decoder architeecture 
w/ skip connections at four compression and procesing levels.

Detection to Instance Segmentation: Image -> cell center probability map -> regional proposal -> instance segmentation.
* annotation - cell centers
* represenation - guassian around cell center
* loss fcn - mse
* spatial weighting is rec'd to improve separation
* ...

Every step of development was human-in-the-loop development.  Speaker kind of laughed at all
the "auto ML" stuff going on...  

This project was insanely good
* Recall (sensitiivty): 0.91
* precision (PPV): 0.97
* F1: 0.94
* NOTE: this wasn't just a simple Chest X-ray thing; this guy was detecting and annotating 
  100's of cells per image
* Error on an image:  classical (18%) vs RSIP DL Sol'n (4%)
* NOTE: this is just ONE component of the full pipeline (there is also tumor seg, key marker seg, 
  and cell classification components)
  
  
 ### Other components
 Similar approaches for tumor seg and key marker seg.  (Key marker seg used data from
 tumor seg, I think, which is cool.)
 
 ### Problem Mitigation
 He talked a lot about looking at what the model is getting wrong and using that to better 
 inform how to build the model (data aug strategy, class balance strategy, etc).


# Day 2

## The Puzzling Tango between Life Sciences and Algorithms (Dennis Shasha, NYU)

Adrian Stoica had a problem: how to get circuits to work in space?!  The cosmic 
rays were destroying electronics on spacecraft in space -- something had to be done!

This brings us to genetic algorithms: design the spacecraft with GAs.  <<Description of genetic algorithms>>
  
Another example of GAs, this time in finance.  <<Look at slides>>.  They unleashed the
  algorithm at 9am on a MOnday and w/in a few mins, the system bought and sold $10M of assets.
  
Can we design a biological virus that grows slowly?  Strategy: use unpopular codons: make
the 3 nucleotide seqs that code for each amino acid the least popular ones for that species.  Ex: for alanine, maybe
GCC encodes faster than GCA, so use GCA in the virus seq.  This is a biological engineering
problem, but it's also an algorithmic one!

Another example:  a plant biologist wants to use less fertilizer (less product, less run-off, less materials, etc).  For
this, we can use a discipline sampling technique called [combinatorial design]()...  

Combinatorial design example:  Say you ave 10 switches w/ 3 settings each (over 59k possible configs).  This
would be a hard brute force way to do things...but using a combinatorial design, you could figure this out in
15 tries...

CD def: for each set of input factors and combo of values from those factors, some experiment
has that combo...  (Note2self:  this stuff is very similar to latin square and random block design
stuff I've done.)

This guy then goes on to show RFs being awesome (NOTE: look @ slides; I had to book an AirBnB and didn't hear all)...

As a computer scientist, he joked that "you have to look at the data!"  The data is noisy and hard
to understand, but the good new is, often only qualitative information is required (e.g., Ibuprofen helps
fight inflammation).


## Beyond AutoML:  Does AutoML replace your data scientist?  (Ryohei Fujimaki, dotData)
Basic concept:  
Feature Processing --> Machine Learning

Motivation:  most companies and data scientists say that most of their time and
effort goes to making data ready for machine learning projects (cleaning, processing,
feature engineering, feature selection, etc).  

He shows a nice graph showing what he means by feature engineering:  a bunch of
complexly connected tables in a relational database --> a single, tabular grid.  This
is a fairly mechanical, beginner-level version of feature engineering...but he probably
doesn't mean it's only this...

Overall, this is a salespitch talk that covers basics and provides the same motivations
you hear again and again (data is difficult to deal with and messy, so let us do it for you!).

I personally distrust these AutoML services... There is something skeevy about going through
every possible data combo and ML model to get the best results.  I've seen grid searches go bad,
even when hedging against overfitting using CV mechanisms while doing it...  I don't know... It 
could be a useful experimentation tool though.  Like, it might point you to a model you
might not have considered, or to a particular data product that leads to new insights...

But, still, you have to have some concerns in your mind, like violating multiple testing, and
stuff... Well, then again, that's why having a holdout set is so important and useful.  So,
I don't know, what this might add for me would be a grid search of feature combinations and 
transformations... But then again, I could do that myself...  




## Convergent AI in Reducing Overdiagnosis, Overtreatment and Misdiagnosis (Stephen Wong, Houston Methodist)
We live in exciting times for investigating our health and bodies: we have access to multi-scale, multi-dimensional, 
multi-modal data.  

Healthcare in the U.S. has a problem:  high costs and low quality.  A lot of the cost is wasteful:  it 
is estimated to be ~700-900B annually. Some estimates
* Admin complexity: $256M
* pricing failure $235M
* failure of care: $135M
* I missed the other ones...

iBRISK
Uses mammography images, ultrasound images, and EMR data...  

Data set advertisement:
* > 14k BI-RADS 4 patient case data
* they use ~11.5k for training, ~2.25k for validation

For their model, they report a 100% sensitivity (recall) and 79% specificity, resulting in
an AUC of 93%.  (These are great results.)

Also working on misdiagnosis of acute ischemic stroke...  Diffusion MRI can detect stroke early on and
is the gold standard...but expensive.  They use CT-to-MRI image mapping using cross-modality deep learning...  If
I understand correctly, this is one of those cool projects where you basically figure out how to use
a cheaper instrument to estimate what the more expensive instrument would have shown...  How do you transform
a CT image into a MRI image?

Another stroke project:  facial recognition and voice interpretation via deep learning.  Currently
doing a clinical trial.  

Speaker diatribed a bit at the end about disliking how many of his colleagues waste time on 
useless, easy problems...that they should focus on more important, impactful problems.



## AI and The Intersection of Robotics and Surgery (Prakash Gatta, MultiCare Health System)

Surgery: "the treatment of injuries or disorders of the body by incision or manipulation," or "basically,
it's legal trauma," the speaker joked.

Robotics in Surgery: "the surgery is done w/ precision, miniaturization, smaller incisions; decreased blood 
loss, less pain, and quicker healing time."  But, generally NOT autonomous.

He shows an amazing video of him doing surgery w/ some robotic enhancements...sewing together some
organ...

In surgical robotics today, the biggest players are 
* [Intuitive](https://www.intuitive.com/en-us), which came about after a DARPA project, 
* [VERB Surgical](https://www.verbsurgical.com/), which arose from JHU and Google

However, newer fledglings have come about that are making their mark:
* Medtronic
* CMR
* Titan
* Senhance


What is the need of robotics in surgery today?  Speaker's personal prefs
* precision
* supererior viz
* magnification
* stability
* tremor eliination
* reduced cost (early discharge, reduce operative time)
* reduced opioid use 
* reduction in complications

Does surgery need AI?
* surgery is highly complex
* also highly litigineous
* avoiding complications saves money (and is better for the patient!)
* despite years of training, mistakes can still be made
* ageing population --> shortage of surgeons

What can AI offer?
* predictive analytics / decision support, e.g., predict complications
* integration of imaging into live anatomy, e.g., prevents injury
* anticipation of surgeon's next move, e.g., improve efficiency
* prevent the surgeon from making a mistake
* surgical (partial) automation


Can we generate value from intraoperative data?
The "robot" generates data with each movement.  Can this data be used for predictive
analytics?  Or even for training new students?  

Democratization of surgery:  universal availability of surgical know-how;  surgical delivery on
a standard platform;  ....

Future of Surgery:  
* image integration & VR/AR --> intraoperative "telemetry" --> superior interactivity/vision ("feel tissue") --> protective measures 
  - am I squeezing to tight?
  


## 11:05, Hub2: Preventing Readmissions Due to Sepsis With Wearable Monitoring and Deep Learning (Wei-Jien Tan, CTO & Co-Founder of Patchd Medical)

Other co-founder of Patchd had a liver transplant and 19 episodes of sepsis.  In one of his
episodes, he showed up and they told him to go home -- vitals seemed normal.  But several hours
later, he was almost on his way out, saying his goodbyes to his family.

Goal:  automate the early detection of sepsis in at-home patients.  

### Part 1:  What problem are we trying to solve?
Sepsis kills -- last year, it kills more Americans than breast, lung, and prostate cancer 
combined.  

Sepsis is a little tricky to define, but we can say it's a "life trheating organ dysfunction caused
by a dysregulated host response to infection."  (Varous definitions have proliferated and evolved over
the years.)

Fact: 80% of sepsis cases are already present when a patient arrives at an ER for treatment.  This 
is an obvious gap in healthcare:  can we help detect sepsis in at-risk patients earlier?  We can
prevent an expensive hospital admission (up to $40k).


```

[Mobile App] --> [Analtyics Platform] --> [External Telephone API]
   |
   V
[something... I missed it ] . -------<>---otherarrows----
```


### Part 2:  Building PoC w/ EMR data

They first looked into the literature:  how have people been identifying sepsis with 
machine learning techniques?  They found relevant papers going back to 2002, but 
it was in 2015-2016 where prediction of septic shock really came into play.  

General Approach
* Use readily available EMR data as a starting point
* Treat the problem as a time series classifcation problem
* Use only physilogical measurements that could be collected in at-home setting
* Label the data according to the latest Sepsis-3 defintion
* Split the data in training, dev, test...

Looking at the data
* found slight decrease in blood pressure (population level)
* slight increase in HR and resp rate (pop level)
* slight increased temperatur variability (pop level)

Inclusion/Exclusion Criteria
* Adults only
* Excluded if microbiology cultures were underreported
* A stay was excluded if it had too few vital sign observations
* Patients who were labeld w/ the ICD code for severe sepsis but
did not meet Sepsis3 were considered ambiguous and discarded
* Total paitents 47,847
  - Sepsis patients:  13,703 (28.6%)

Labelling the Data
1. Defin suspicion of infection
2. Look for organ dysfunction

Building the training set
* Split into sepsis-positive and sepsis-negative
* Discarded data after sepsis onset in sep-pos patients
* augmented sepsis-pos patients to balance 
  - NOTE: why? they had like 30% sep pos... 
* Sampled seqs randomly from a sepsis-neg stay...to account for sep-pos being cut short (as a rule)
* something else.....
* A signle sep-pos stay was matched to 4 sep-neg stays; from each sep-neg patient, a sequence equal in length to assoc'd sep-pos stay was selected

Building the Model
* used LSTM network on TensorFlow
* dockerized the system to run multiple experiments at once
* trained model using GPU p2 instances on AWS

```
Output.... 
  ^
  |
Dense Layers...
  ^
  |
LSTMss
  ^
  |
Normalized Input Vector [HR, Systolic BP, Diastolic BP, reps rate, temp, spo2, age, gender]
```

NOTE: I can probably improve their model w/ a few tricks :-p

Optimizing the Model
* Coarse Grid Search to establish a starting set of hyper params
* Then a random search of the starting set of hyper params
* Mult perf metrics (AUROC, AUPRC) for more recent and previous hyperparams looked at to 
  figure out what to discard...

Paper Refs
* Septic shock diagnosis by nn and rule baesd sysstms
* prediction of sepsis in the ICU w/ minimal EHR data: a ml approach
* a targeted real-time early warning score (TREWScore) for septic shock
* an interptable machine learning....spetic, something something....


## Part3 : Transitioning model to Outside the Hospital
The aforementioned model was done on all EMR data...  What can we do with patients
out in the wild?

Out in the Wild 
* lower incidence rates of sepsis
* paitent compliance results in missingnes
* motion artefacts

General Approach
* ICU PoC (complete)
* Ward Validation Study
* Prospective Post-Discharge Study

Wearable Devices
* Consumer grade:  Apple Watch, Fitbit, Samsung Smart-Watch
* Medical grade devices
  - FDA cleared medical devices that are validated to performance standards (e.g., ISO 81060-2)
* They went w/ several FDA-cleared devices (validated for accuracy and safety)
  - combined, they measure SBP, DBP, resp rate, HR, skin temp, spO2



## 12:05, Hub2:  Supporting Patient’s Use of Bioelectronic Medicines with AI (Jai Yu, Cala Health)
Jai was a post-doc at UCSF, where he identified memory coding mechanisms using neural recordins.  Prior
to that, he mapped the biological neural networks in a fruit fly's brain.

Focus: novel wearable bioelectronic therapies modulating the nervous system.

Product:  Cala Trio
* wrist-worn device
* FDA cleared
* first presciprtion, on-demand, wearable bioelectronic therapy for Essential Tremor
* stimulated perfpheral nerves, targeting the central tremor network


Wants to: leverage digital technology to imporove patient therapeutic experience.

The device has an accelerometer, which measures tremor among other things.  Other types
of data they can collect and monintor are device usage metrics (when is the device being used,
for how long, etc).  One thing we can use this data for is device support: 
* Which customers/patients are seeming to have trouble using their device?  
* When do they need the help?
* What kind of support is needed?

Strategy: detect unusual usage patterns -> classify usage patterns -> recommend action

PoC Study Using Clinical Trial Data
* Cala Health held a clinical trial (n=263) for Essential Tremor
* patients used the device at home for up to 90 days

Patient/Device Interaction
```
  [Tremor Measurement (20 min)] 
               | 
               V
 [Therapeutic Stimulation (40min)] 
               | 
               V
      [Tremor measurement] 
               | 
               V
        [Patient Rating]
```

Uses a continuous time Markov model of device interaction... Think of cube, where the axes are
current event, next event, and time elapsed, and transition probabilities stored in each cell.  This
way, anomalies can be defined (low transition probabilities).  Developed a "daily anomaly index" for
each patient.  Showed a heat map (y: patient, x: days into therapy, col: daily anomaly index).  It
was found that almost all patients start out with up to a 2-3 days of higher daily anomaly index, but
that they settle into normal levels...  However, there exist patients that remain highly anomalous
throughout entire course of therapy, or others that suddenly become anomalous at some point.  To
group the patients by anomalous activity type, Jai used self-organizing maps (SOMs).  This outputs
a topographic map of traing event sequences...  Basically, another heat map (x: Neuron ID, y: Neuron ID, 
col: distance to neighbors).  For a given (neuron x, neuron y) pair, he then superimposes the average anomaly score 
for each associated sequence.......  Alternatively, look at patient-specific heat map thresholded to some
high anomaly score...  Then identifies which (neuron, neuron) regions commonly crop up....  He found 2 major
regions, then identified that the two regions stem from two distinct causes in general:  device-driven anomaly 
(therapy terminated by device) vs patient-driven anomly (therapy terminated by patient).






## 1:35, Hub1:  Implementing Clinical Analytics Predictive Engine (CAPE): From Concept to Workflows (Daniel Chertok, Northshore)

Northshore is a system of hosptials/clinics in the Chicago area.
* 45k admissions
* 126k ED visits

Key Initiatives
* Care model redesign
  - advances in clincal outcomes, patient exp, and provider engagemnetn
* Smart growth and access
* Payment model effectiveness
* Efficiency and productivity

CAPE: The Journey
* descriptive analytics -> predictive analytics -> prescriptive analytics

How do we get from A (the dream) to B (something tangible)
```
The Dream: improve patient care, reduce expenses, increases patient loyalty
```

Define outcomes.  Not so simple.  For example, a death occurs in the hospital.  Who records
this?  When is it registered?  He shared a funny anecdote: oftentimes people die in the hospital,
but still have appointments to show up to, which they dutifully do (according to the data 
sometimes).  What is demographic data?  Can we augment it with socioeconomic data?  What about
patients that have 7 different birth dates listed?  

Clinical history: do we have all of it?  Is it accurate?  Is it available in real time?

He talked about wonky predictive models that were used in practice:  when certain data
wasn't available, it was imputed as 0, leaving other factors to strongly influence
the prediction in unnatural ways -- thus, e.g., a 52yo entered the hospital and
was flagged for at-risk of dying in hospital...  

Not all data available in historical records is available at time of real-time
prediction.  This is what I usually refer to as unavailability leakage/illegitimacy.  For 
example, billing data is not available at admission, and lab data isn't available right
away.  

Why did they impute it as 0?  The physicians wanted them to.  There were many arguments
about this... Ultimately, it was not a great imputation.

EHR Flow Sheets --> Model:
```
Install EHR's cognitive computing platform

Export data from R to PMML

Map EHR flowchart vars in to PMML vars

Decide how to store the resulting scores

Enjoy you freshly computed CAPE score -- responsibily!!!
```
PMML:  predictive model mark-up language
* https://www.kdnuggets.com/faq/pmml.html

He said PMML was hard to use for NNs...looks like they have worked on it though:
* http://dmg.org/pmml/v4-0-1/NeuralNetwork.html

<<look at slides for model dev/deployment cyclic flow chart>>


Talks about precision-vs-recall tradeoff.  You can only get perfect recall if you
accept really low (costly!) precision.  

Victim of Success
* team had successful model
* suddenly everyone wanted to know if it could bbe used for "off label" purposes
* expectations were overly optimistic about new domains and projects
* pressure to expand into all these new domains
* analysis overload / team burnout / etc

Retrospective vs Prospective Validation

"Quite often when you try to build an airplane that is also a boat, you get neither!"  Great quote :-p

No more scope creep! 
* Define and freeze outcomes
* analytics-related requests need to be vetted by the anlytics team
* ... more points
* "Otherwise you stay in grad school forever working on your thesis." (Lol, I can relate.)

Deployment
* ED physician workflow based on an EHR alert firing if CAPE score threshodl is met
* scores are stored no less frequenty than every hour for further analysis
* ...more points

Ongoing Deployment
* always a work in progress
* need to analyze performance as we go along and after any tweaks
* need to see how interventions affect outcomes
* capacity and workflow adjustments as track record is acquired
* mointor operational efficiency and make suggestions
* keep stakeholders happy
  - regularly analyze and report on model performance
  - implement updates quickly
  - fast turnaround on analytical requests
  - set clear expectations, explain limitations of analysis
  - data quality: develop ratings on the data used (bronze - use at own risk, silver - we can
    get some trust and mileage, gold - great!)

Always remember: data science is a luxury -- you're doing the cool stuff, but you'll go first
in any economic downturn...so you need to stay relevant.  Find the pain points.  An ok model
is better than no model at all...  

<<good graphic on data sci project lifecycle, from inception to deployment>>


## 2:10, Forum A:  Callisto - Machine Learning Targeting in Johns Hopkins' Care Management Programs


## 2:45, Hub2:  Multi-Modal Data Aggregation for AI and Causal Inference: Applications to Cancer (Ganapati Srinivasa, Omics Data Automation)



# Connections / Folks I met
* Emma Wypkema
* Iryna Skrypnyk (Pfizer)
* George (Dessa)
* Siddharth Dani (Medtronic)
