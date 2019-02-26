---
title: Messing Around with Boto3&#58; S3 Clients and Resources
layout: post
tags: aws python automation etl wwe
---
So you've pip-installed boto3 and want to connect to S3.  Should you create an S3 resource or an S3 client?  

Googling some code examples you will find both being used.  In this post, let's look at the difference between
these two basic approaches of interacting with your AWS assets from boto3, and show a few examples of each.

## First and Foremost: What's What?
In short, a Boto3 resource is a high-level abstraction, whereas a client is more granular.

From the [documentation](http://boto3.readthedocs.io/en/latest/guide/resources.html) on resources, we find
> Resources represent an object-oriented interface to Amazon Web Services (AWS). They provide 
> a higher-level abstraction than the raw, low-level calls made by service clients. 

The [docs](http://boto3.readthedocs.io/en/latest/guide/clients.html) on clients tell us:
> Clients provide a low-level interface to AWS whose methods > map close to 1:1 with 
> service APIs. All service operations are supported by clients. Clients are generated 
> from a JSON service definition file.

## Create an S3 Resource and Client
```python
import boto3
s3r = boto3.resource('s3')
s3c = boto3.client('s3')

```

## Print out all bucket names
If you play around with the resource_buckets list, you will see that each item is a Bucket
object. On the other hand, the client_buckets list contains dictionary representations of 
the S3 buckets.


### Resource
```python
resource_buckets = list(s3r.buckets.all())
for bucket in resource_buckets:
    print(bucket.name)
```

### Client
```python
client_buckets = s3c.list_buckets()['Buckets']
for item in client_buckets: 
    print(item['Name'])
```


## Upload a file to a bucket
### Resource
```python
fileName = 'someFile.txt'
bucketName = 'some-bucket-name'
with open(fileName, 'rb') as file:
    s3.Bucket(bucketName).put_object(Key=fileName, Body=file)
```

### Client
```python
# Uploads the given file using a managed uploader, which will split up large
# files automatically and upload parts in parallel.
fileName = 'someFile.txt'
bucketName = 'some-bucket-name'
s3.upload_file(fileName, bucketName, fileName)
```

## Read a file from a bucket
### Client
```python
csv_file = s3.get_object(Bucket='some-bucket-name', Key='some-filepath-name.csv')
csv_str = file['Body'].read().decode('utf-8')
```

## Looking Forward
Something that I tinkered with today, but could not get to work is 
[direct access to S3 using Pandas](http://pandas.pydata.org/pandas-docs/version/0.20/io.html). This is
definitely something I want to come back to.

This article looks promising:  
* [Accessing S3 Data in Python with Boto3](https://dluo.me/s3databoto3)


## References
* Krbnite: [Boto3: the AWS API](https://krbnite.github.io/Boto3-the-AWS-API/)
* StackOverflow: [Are Boto3 Resources and Clients Equivalent](https://stackoverflow.com/questions/38670372/are-boto3-resources-and-clients-equivalent-when-use-one-or-other)
* OzNetNerd: [Dymystifying AWS Boto3](http://www.oznetnerd.com/python-demystifying-aws-boto3/)
* Slsmk: [Getting Starting with Amazon AWS and Boto3](http://www.slsmk.com/getting-started-with-amazon-aws-and-boto3/)
* StackOverflow: [Open S3 Object as a String with Boto3](https://stackoverflow.com/questions/31976273/open-s3-object-as-a-string-with-boto3)
