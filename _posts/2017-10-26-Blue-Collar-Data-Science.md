---
title: Blue Collar Data Science
layout: post
---

Automation: it's what I do best.  

From webscraping and API calls, to statistical analyses, report generation, and automated emails: a
large part of being a data scientist is all about automation. Of course the role varies company to company, 
but I've found many other data scientists to discuss similar issues (e.g., "80% of my time is spent cleaning data!"). 

## But what about the fancy stuff?
Yes, there are also the fancy-sounding things that data scientists get to work on and apply, 
like machine learning models, deep neural networks, recommendation systems, and so on. These things
get all the press in magazine articles and newspapers (if and where those things exist).  But, from
a project perspective, that stuff is like that last mile in a marathon!

In the beginning of this year, I was enrolled in Udacity's Deep Learning nanodegree.  Each morning,
I'd learn all this cool stuff and have a ton of ideas to play with for the day.  But going from the
classroom to the workplace, as always, was much more involved than this.  

## The dirty stuff
It often seems to me that the uglier parts of a data-centric job
are glossed over by the unlimited numbers of online courses and schools dedicated to data science and/or
machine learning.  People pay data collection, cleansing, and preprocessing some lip service, but little else.

That's one of the reasons I write these posts: in the data real world, your employer will expect SO MUCH MORE 
from you.  

For example, 
* I required access to a GPU, which means 
  - pitching the idea to your direct manager and selling it, 
  - and it might require further negotiation with IT, the data engineering team, etc.  
* I was provided with a Linux g2.2xlarge EC2 instance on AWS, but a corporate asset requires much more secruity than spinning up an EC2 instance for the course
  - this required dealing with SSH keys and .pem files just to get on
  - I had to learn about and set up using SSH tunnels to use Jupyter notebooks
* Also, I had to set up and maintain my python environment, which involved some intracacies 
  - e.g., getting TensorFlow installed properly
  
## More on the Mud

Things to consider: 
* are you working at a modern, tech/data-driven company?
* where is the data? what generates it? do you have to go out and get it? is it all in one place? is there a data dictionary? a map of the company's data assets?
* will you be requiring data from a different department that is stingy, touchy, and/or apathetic about your access? 
* do your superiors really want an elegant solution? 
* do your superiors value your expertise or fear it?
* if you think a neural network is the best way to go, can you explain why and what business value that approach addes to the one it might replace?
* do you have the right data to answer the questions your stakeholders are interested in? if not, do you know where to get it, and how?
* does your stakeholder understand the data? that is, do they interpret it properly? do you interpret it properly? how was it measured? 
* are you breaking any laws?
* when you have a solution, is it ready to be put in production? do you need to work with the IT team to scale it? does the data engineering team need to get involved to capture the data at right scale and cadence? etc
* can you manage expectations? 
* do you tend to get caught up in self-imposed scope creep?  
* 
