---
title: Refresher on AWS CLI 
layout: post
tags: aws easi
---

Straight to the point: working on a new project and needed to start using AWS CLI again.  Figured
I'd write down a few useful commands as I go.


# Installation
First of all, to get AWS CLI:
```
pip install --upgrade pip
pip install awscli --upgrade --user
```

To ensure that things are working:
```
aws --version
```

Bozo Tip:  If you do this then upgrade your python installation (e.g., `conda update python`), you might 
find that the `aws` command no longer works. To remedy this, you'll have to the `pip install awscli` command
again.

# Configuration
Ok, it's installed -- time to configure. The most basic way to do this:
```
aws config
# then input key, secret key, and region id (e.g., us-east-1)
```

If you have more security required, then the process might be a bit more complicated, e.g., at work,
we require 2-factor authentication (this can get in the way of scheduled scripts, but there is a way
around that too).

# Some Basic Commands

Look at your buckets in S3
```
aws s3 ls
```

Look at the contents of a bucket in S3
```
aws s3 ls bucketName
```

Copy the contents of a bucket to your local working directory
```
aws s3 cp s3://bucket-name . --recursive
```

Synchronize contents of a bucket w/ a local directory
```
aws s3 sync local/path/to/folder s3://bucket-name/path/to/folder --exclude *.tmp
```

Look at your EC2 instance IDs
```
aws ec2 describe-instances | grep InstanceId
```

List CloudWatch metrics
```
aws cloudwatch list-metrics
```