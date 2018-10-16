---
title: Accessing CloudWatch with Boto3
layout: post
---

At my last job, my main cloud was a Linux EC2 instance -- there, I used Python and Cron to automated almost everything
(data ETL, error logs, email notifications, etc).  The company at large commanded many other cloud features, like Redshift and S3, 
but I was not directly responsible for setting up and maintaining those things: they were merely drop-off and pick-up locations
my scripts traversed in their pursuit of data collection, transformation, and analysis.  So, while it was all cloud heavy, 
it felt fairly grounded and classical (e.g., use a server to execute scripts on a schedule).

At my new job, the cloud heaviness is not indirect: I need a more thorough understanding of full, explicit cloud 
architecture and how everyting pieces together.  Though much of it serves similar functionality (script scheduling, 
event triggering, error logs, email notifactions, etc), most of it is in a serverless form.  We have AWS Lambdas doing
a whole bunch of work for us and CloudWatch keeping tabs on it.  I've been asked to develop some software, such as
a Flask app, to ... well, watch CloudWatch, I guess!  To develop ways to easily slice and dice through the logs and provide
illuminating visualizations so that problems can be identified and dealt with promptly.

So here we are: how does one use Boto3 to connect to CloudWatch?

