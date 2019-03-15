---
title: Accessing CloudWatch with Boto3
layout: post
tags: aws python easi
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

### Credentials
The plan is, this app is going in a Docker file so that I can easily distribute it to my team mates.  So I
do not want the app to rely on my AWS credentials...  But maybe it should rely on there being an AWS 
configuration file: I don't want the team members to have to annoyingly type in their credentials every time
they spin it up.  Hmm... Will have to figure that out in due time.  For now?  Let's just look at
some ways of getting CloudWatch logs!

This will work whether or not there is a configuration file set up:
```python
import boto3
boto3.setup_default_session(region_name = YOUR_REGION) # e.g., 'us-east-1'
client = boto3.client(
  'logs',
  aws_access_key_id = YOUR_AWS_ACCESS_KEY,
  aws_secret_access_key = YOUR_SECRET_ACCESS_KEY
)
```

### Log Groups 
To sift through logs, you will need to know the Log Group's name that you are interested in.  One way 
to do this is to go to the CloudWatch portion of the AWS Console, click on "Logs" and look at the various group names.

Another way to look through the log group names is through Boto3:
```python
response = client.describe_log_groups()
[item['logGroupName' for item in response['logGroups']]
```


### Look at Some Logs
With the group name in hand, we can request a bunch of logs.

```python
logs = client.filter_log_events(logGroupName = YOUR_LOG_GROUP_NAME)
logs.keys()
  dict_keys(['events', 'searchedLogStreams', 'nextToken', 'ResponseMetadata'])
```

* the  `events` key gives access to all the actual data your request generated
* the `searchedLogStreams` key houses the names of which particular log streams were searched, and if they were searched completely (True or False) 
* if there are too many logs (looks like the limit is ~1MB), you will have to paginate by using the value of the `nextToken` key in a subsequent request
* 'ResponseMetadata' lists some basic things like RequestId, HTTPStatusCode, HTTPHeaders, and RetryAttempts

If you need to paginate, try something like this:
```
# pagination code here
```

One can filter the group's logs by `startTime` and `endTime` (in Unix time), by specified log stream names in an 
array `logStreamNames`, by `logStreamPrefix`, and `filterPattern`.

The `filterPattern` parameter was not straightforward to me, and I had to [scrutinze the documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html). Honestly, reading through
that can make your eyes cross... Seems like some fouled up Bash-JSON-crazy hybrid syntax...  
