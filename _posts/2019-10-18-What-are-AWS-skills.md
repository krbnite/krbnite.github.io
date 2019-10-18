---
title: What are AWS Skills?
layout: post
tags: aws easi
---

When someone says they have AWS skills, what do they mean?

At WWE, I worked with Redshift and S3 constantly.  Towards the end of my tenure there, I was playing
around with Hive and Presto on top of S3.  When I began developing deep learning models, I 
requested access to a g2.2xlarge (GPU) EC2 instance. I was asked if I'd need an additional
EBS volume?  

Uh, what?  They rephrased it: how much storage beyond the default would I need
on the machine?  Looked into EBS volumes a bit and ultimately went with an addtional 500GB.  My 
wonderful associate on the data engineering team hooked it all up.  I simply ssh'd in 
thereafter, downloaded and installed NVIDIA and CUDA tools, then got to work.  

Note that in all of this, I did not particularly deep dive into AWS.  I didn't design user
and group IAM roles for S3 access -- just simply used S3.  I didn't spec out what
we needed for our Redshift cluster and optimize speed and storage against cost, and whether
the cluster would grow and shrink as needed within certain limits: I simply created and
queried data via SQLAlchemy in a Python script or shell, as if it were a regular ol'
Postgres database.  For the EC2 instance and EBS volume -- same deal!  I could have been
SSH'ing into anything; it just happened to be an EC2 instance.  The fact that it had
additional storage attached to it was great, but I had nothing to do with that.

Now, don't get me wrong: during this time, I had a personal AWS account too, which I was using
while taking Udacity's Deep Learning Nanodegree.  So, I did have experience setting up
EC2 instances, choosing the amount of storage, selecting an AMI, etc.  I was also watching
and learning from various AWS courses online, getting a feel for some of the tools I wasn't
using everyday.  User and group roles?  This isn't all much different than setting up user 
and group permissions on a Linux machine.  So, all in all, I wasn't completely ignorant.  Not 
"cloud guru" level experience either, but I was familiar with the AWS Console and 
some AWS command line stuff, e.g., listing S3 buckets and cheesy stuff like that.  

When I got into my current position, suddenly I was exposed to CloudWatch, Lambda, 
RDS, API Gateway, SNS, and more.  I wasn't necessarily responsible for learning the
ins and outs of all these things, or using them on a daily basis.  But some members on
my team were and did. Also, we were using a lot more AWS CLI -- and, interestingly, my
coworker had made CLI access require 2-factor authentication, and he designed a script
that took in a token from the Google Authenticator app on your phone.  Cool stuff!  Point is,
here was a whole other level -- nay, dimension -- of AWS and its use cases.  

And this brings us to an important question.

Given all these different ways of "knowing" and interacting with AWS products, what does it 
mean to put "AWS skills" on your resume?  That's
so vague!  Yet, I see it all the time in my current role, where I am in the process of hiring data 
scientist.

On these data science resumes, more often than not, listing "AWS"  just means that
someone has SSH'd into an EC2 instance, connected a Jupyter notebook, and used S3 at
one point in the process.  Sometimes it means they've only used S3, period, which I'm not sure
really counts!  Then there are the
folks who tersely list "AWS," but mean to say that they've designed Lambdas to respond to events 
from API Gateway, which pipe the payload data into an S3 bucket, which triggers another Lambda, which 
writes the data to Redshift, and sends summary emails using SNS every 1000 times this happens. For others, 
"AWS" means they've set up, optimized, and managed Hadoop clusters on AWS EMR.  

Point is, please be clearly communicate your skills next time you apply for a job!

My advice for data scientists, machine learning engineers, and similar-buzzword-titles is this:
* If you have a general skills section, break it down into subsections; specifically, include a "cloud" section:
  - Cloud
* For each subsection, then list the top-line ingredients, e.g., continuing our above example:
  - Cloud: AWS, GCP
* Then, for each top-line ingredient, use parentheses to expand upon it a bit, e.g.:
  - Cloud: AWS (Redshift, S3, EC2), GCP (BigQuery)
* Finally, in other parts of the resume where you lists jobs, you should list projects -- and for each project,
  you should parenthetically list a few of the skills used, followed by some bullet points about what you did
  - Data Scientist @ Data Company
    * CoolProjectName1 (python, redshift, S3, keras, deep learning, CNNs, Tableau)
      - Designed python scripts to automatically query any new data landing in Redshift and identify 
        corresponding video files in S3
      - Built CNN model in keras and trained it to classify x/y/z in the Redshift data and S3 video files
      - Storage classification results in separate Redshift table
      - Designed Tabluea dashboard for stakeholders to keep current on incoming classification results
 
If you follow some kind of practice like this, it will be easy for a hiring manager to look at and 
understand.  It makes it clear that you are familiar with using the cloud environment, but likely not
familiar with designing, optimizing, and managing the cloud environment.  This ensures that the hiring 
manager knows whether or not you have the right type of AWS experience for the job.

Of course, you might want to purposely obfuscate because you are unsure about your level
of AWS experience, which I get...but why?  For a data scientist position, it's either not expected
that you are an AWS guru, or it is. In either case, just listing "AWS" doesn't add any points to
the resume.  In the former case, I don't totally care because I'm weighting other skills more highly,
but spelling it out more clearly can probably help you gain a few points.  In the latter case, where
the AWS skills are weighted highly, I actually need to see it listed out in more detail, lest you 
lose points (and probably the job).  

In summary: "AWS" in the skills section is meaningless.  Be more specific!


