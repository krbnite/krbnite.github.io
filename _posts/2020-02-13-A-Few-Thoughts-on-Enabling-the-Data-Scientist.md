---
title: A Few Thoughts on Enabling the Data Scientist
layout: post
tags: easi cvb data-science aws cloud
---

Just some thoughts on what a DS-enabled environment should look like, 
at least from the perspective of needs I've had and projects I've worked on. The
needs are staged in orders of approximation of what my ideal would be:
* 1st Order: have access to a higher-powered machine than a laptop
* 2nd Order: having access to multiple higher-powered machines that fit different needs
* 3rd Order: having access to multiple machines from the same central location

I'm no expert on architecting these things on the backend, but I do know
some frustrations of practicing data science with limited resources.
 
----

# 1st-Order Approximation 
As a data scientist, the most basic set-up I need access to is the ability to
spin a powerful-enough EC2 instance up and down. This EC2 instance should have permissions to access other 
relevant areas of the cloud environment, e.g., it should definitely be able to pluck files and data sets 
from S3. 

The most basic set-up allows the necessary work to get done, however it is not 
economical. For example, to train some models and explore their parameter and hyperparameter
spaces, there is no escaping the need for a GPU-powered machine (lest ye want to be 
inefficient and unproductive); however, doing almost any other development on this type
of machine is a waste of company money (e.g., cleaning up a dataset, or doing some exploratory
analysis in a jupyter notebook).
 
# 2nd-Order Approximation
Ideally, a data scientist should have the permission to determine the optimal 
resources they need for the day's tasks (you know, within reason).

For light computational workloads, a data scientist should be able and encouraged 
to spin up a cheap general purpose machine (e.g., a t2.medium). Light workloads typically
include some data exploration, basic analysis, plotting, file clean-up, etc. If the dataset is small 
enough, then it's possible to stick to the cheapest resource for the entire project lifecycle -- even
for model training. A data scientist should definitely try to stick to the cheapest resource (or
even their laptop's local resources) for anything like paper/report writing or preparing presentations.

For heavy computational workloads, a data scientist should definitely have access to higher-powered
machines. For example, at work, we develop models for very large sensor, imaging, and/or genomic sets -- and at 
one point the t2.medium doesn’t cut it (e.g., it could take hours per epoch and days just to try a new
hyperparmater tuning).  

A cloud provider like AWS provides an extraordinary wide range of computational resources, so though
data scientists should be able to determine what resources their
project needs and/or what optimizes their workflow and productivity, there also needs to be 
policies in place to avoid waste (e.g., a policy that restricts the options to only those the company is comfortable
paying, or a process to verify and validate a project's needs, etc). Choosing the most high-powered, multi-GPU machine 
to train a model that only requires a single GPU shouldn't be the norm!  But forcing the data scientist
to use extremely underpowered machinery shouldn't be tolerated either.

If you're not sure what to allow or disallow, or what policies to put in place, then take it from me:
a good "2nd approximation" includes allowing your data scientist access to 
* one or two low-to-medium power GPU machines 
* one or two higher-end, many-core CPU machines

For a long time at my job, I've had access to a p2.2xlarge and a c4.4xlarge, for GPU and CPU needs, 
respectively (relatively low end, but they have suited most needs within reason). However, other 
than my laptop, for most of that time I didn't have access to a cheaper, lower performance 
machine to just diddle around with during more exploratory phases. This would often bother me 
since it amounts to needlessly wasting company money (thankfully, we've been trying out better systems
and practices). However, one should definitely put the costs into perspective so no one 
freaks out: having a data scientist use a more expensive, high-powered cloud machine all day, even when they
don't need to, is better than the data scientist not having access to the high-powered machine
at all. The value add here is saving the data scientist's time and sanity (~$50-$85/hour), which is much 
more expensive than these "expensive" machines (say $1-$2/hour for decent power).  

For the deep nets I've developed for human activity recogntion using large, multi-sensor datasets, a single
hyperparameter set could take up to 6-10 hours to complete training on my laptop or under-powered cloud
machine (depending on temporal resolution, window size, number of subjects, etc).  On the p2.2large (low-end GPU
offering), this only took  10-20 minutes.  

Similarly, for grid searches over large hyperparameter spaces during the optimization of a random forest, we are 
talking about 50%-75% time savings on the c4.4xlarge compared to my MacBook Pro.   
 
# 3rd-Order Approximation
Being able to access the resources you need is great, but not automatically simple or 
efficient. 

Something that would make things smoother is providing a data scientist with a persistent storage
volume that can somehow be attached to the one of several computational resources at will.

Having dedicated t2.medium EC2 instance per user is ok, but without a persistent storage volume (check out EBS), 
there is a complexity that remains in transferring data from one location to another – and an additional git pull/push 
complexity that can end in merge nightmares.  Background:  on an older project, we kept the rawest form of the data 
in the data lake (some dedicated S3 bucket), which served as an untouchable single source of truth.  However, to use the 
data, it had to be synced into a computational environment.  Let’s say the data of interest from S3 is synced with the 
data scientist’s dedicated t2.medium:  they make a few Jupyter notebooks, create a few python scripts, extract some 
features, and have laid out an deep neural net architecture.  They go to run the model... 30 minutes later it's still on 
the first epoch – and there's likely 15-30 epochs to go.  So they decide to spin up a p2.2xlarge (GPU instance).  Without 
some kind of persistent storage, they have to do a few things here: 
* at minimum, they have to sync the raw data from S3 to the new EC2 instance, then re-run their data processing and 
 feature extraction pipelines (alternatively, they can also sync that data from the original EC2); 
* they must git push their git repo from the t2.medium, then git clone their git repo into the p2.2xlarge. 

Ok, so they do all that, run the model and generate lots of output data;  they no longer require the high performance machine, 
so they decide to work on the t2.medium again... Only now, they have to do the whole git push/pull and data syncing steps 
again.  Hell, I almost forgot: without persistent storage, they'll even have to recreate their Python environments from 
scratch unless they are using something like Docker...  POINT IS: with a dedicated storage volume, they can should be able
to just attach it to whatever EC2 they are currently interested in working on. 
 
SIDE NOTE:  Generally, there should be a policy to only work in the provided cloud environment.  The data syncing issues 
referenced above are 1000x worse when it's necessary to repeatedly sync data to and from S3 and a local machine.  Transfer 
rates are painfully slow for S3-to-local syncing when you have any datasets that are even remotely large (say, 10-15GB), 
e.g., about 30mins/GB, whereas syncing/transfer rates are VERY FAST between S3 and EC2 in an organization's cloud environment. 
 
# Additional Tidbits
For security's sake, a VPN is very helpful – this way, EC2 instances, EBS volumes, and so on can only be accessed 
after a user signs into a VPN (which should be username/password protected, as well as multi-factor authenticated, e.g., 
using Duo or Google Authenticator). 
 
Other things worth considering:
* Spark cluster:  very large sensor, image, or genomics data sets are easily dealt with when they can be worked with 
  in memory
* Databases:  
  - each data scientist should have unique login credentials to 
  - each should have reasonable permissions for (e.g., read/write on their own tables, read on shared tables, read on production tables, etc). 
  - there should be various policies in place about "sandbox" and "production" tables (or even separate, partially mirrored databases)
* SageMaker 
  - haven't used it, but it looks useful
* AutoML tools
  - almost scared to try these (b/c it seems to really change the nature of the job), but probably will be considered an 
    essential tool in coming years

I have not used SageMaker, but I get a sense that it automatically includes the various features I spoke about above (persistent 
storage underneath the hood with a seamless ability to switch between computational environments – kind of like Google CoLab).
 
---

Anyway, just some thoughts for now.
