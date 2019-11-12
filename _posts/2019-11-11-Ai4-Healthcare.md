---
title: Ai4 Healthcare
layout: post
tags: healthcare machine-learning deep-learning easi
---

Some notes and anecdotes about my experience at the Ai4 Healthcare conference in NYC, Nov 11-12.

# 9:00: AI and The Art of Human Care (Hassan Tetteh, DoD Joint Artificial Intelligence Center)

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


# 9:25: Solving Genomic Data Privacy in the Age of AI (Kyle Knack, Pure Storage)
Kyle wore a bright orange shirt, like the cones and caution signs you might find on
a construction site.  His demeanor fit the look: 

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


# 1:35, Hub2:  What AI Will Bring to Medicine, and Why Human Experts are Here to Stay (Hakima Ibaroudene, Southwest Research Institute)

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




# 2:10, Hub2:  Best Practices in Applied Deep Learning for Digital Pathology Multiplex Image Analysis (Moshe Safran, RSIP Vision)
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









# Connections / Folks I met
* Emma Wypkema
