---
layout: post
title: Boto3 -- the AWS API
tags: aws python wwe
---

Holy Foley.  APIs, right? 

Answer: right.

Do this:
```
pip install --upgrade boto3
ipython
```
* https://boto3.readthedocs.io/en/latest/reference/services/index.html
* http://boto3.readthedocs.io/en/latest/guide/quickstart.html

Now this:
```python
import boto3
s3 = boto3.resource('s3')
iam = boto3.resource('iam')
```


You will need credentials...

During a session...
```python
# The session token is optional
session = boto3.Session(
  aws_access_key_id='nuMBErsaNDL3tt3RS', 
  aws_secret_access_key='50mENUmb3rzANdLEtTeRz'
  aws_session_token=SESSION_TOKEN
)
```

For a client...
```python
# The session token is optional
client = boto3.client(
    's3',
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY,
    aws_session_token=SESSION_TOKEN,
)
```

Putting in your credential like this is ok for an interactive session, but
you probably do not want to write code that others will use with your credentials
hard-coded in.  

If you want to set default parameters for these secrets and keys on your own computer, create a 
[config file](http://boto3.readthedocs.io/en/latest/guide/configuration.html#guide-configuration):
```
mkdir -p ~/.aws
echo [default] > ~/.aws/config
echo aws_access_key_id=yourAccessKey >> ~/.aws/config
echo aws_secret_access_key=yourSecret >> ~/.aws/config
```

I don't know about you, but at my work we have other credentials as well.



```python
s3 = session.resource('s3') 
bucket = s3.Bucket('path/to/your/bucket')
```
