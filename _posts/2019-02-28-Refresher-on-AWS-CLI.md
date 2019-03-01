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

### Look at your buckets in S3
```
aws s3 ls
```

### Look at the contents of a bucket in S3
```
aws s3 ls bucketName
```

### Copy the contents of a bucket to your local working directory
```
aws s3 cp s3://bucket-name . --recursive
```

### Synchronize contents of a bucket w/ a local directory
Note that the `aws s3 sync` command only creates a 1-way sync.  If you want the sync going
in both directions, you'll have to issue two sync commands (shown below).

Also note that it seems that "sync" is just shorthand for "copy everything this one time" (i.e.,
`cp --recursive`).  The word "sync" to me suggests a DropBox-like functionality, but I haven't 
found this to be true... And a look at [sync's documentation](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html)
seems to confirm that it is a sophisticated take on this behavior (e.g., only copies new and/or updated
files instead of everything).

```
# Local -> S3 (1-Way Sync)
aws s3 sync local/path/to/folder s3://bucket-name/path/to/folder

# S3 -> Local (1-Way Sync)
aws s3 sync s3://bucket-name/path/to/folder local/path/to/folder 
```

Note that when 1-way syncing an S3 bucket to a local directory, if you are
in the desired directory, then you can leave the 2nd argument blank (i.e., defaults to current
working directory).

Pro Tip: You don't have to sync everything:
```
# 1-Way Sync: Local -> S3 (exclude .tmp files)
aws s3 sync local/path/to/folder s3://bucket-name/path/to/folder --exclude *.tmp

```

### Remove some S3 files

First of all, most of the time you'll probably want to do a dryrun (you know, to make sure you 
don't lose your mind or job).
```
aws s3 rm s3://bucket/prefix/ --dryrun --recursive 
```

To be a ninja, you'll want to learn how to use the `--include` and `--exclude` commands well.
```
# Dry run on "erase everything, but exclude everything" (should do nothing)
aws s3 rm s3://bucket/prefix/ --dryrun --recursive --exclude "*" 

# Dry run on "do not erase anything, but those pesky .jpg files"
aws s3 rm s3://bucket/prefix/ --dryrun --recursive --exclude "*" --include "*.jpg"

# Dry run on "do not erase anything, but those pesky .jpg files -- except the cute ones!"
aws s3 rm s3://bucket/prefix/ --dryrun --recursive --exclude "*" --include "*.jpg" --exclude "cute/*"
```

There is some regex you can do for complicated removals, but I found that stringing together a sequence
of includes and excludes can sometimes get the job done faster (you know, instead of Googling the 
idiosyncracies of regex and ultimately going down a bunch of interesting-but-not-high-priority 
rabbit holes!).

Remember, when the dryrun list finally matches what you want removed, then remove the dryrun flag
(and cross your fingers).


### Look at your EC2 instance IDs
```
aws ec2 describe-instances | grep InstanceId
```

### List CloudWatch metrics
```
aws cloudwatch list-metrics
```
