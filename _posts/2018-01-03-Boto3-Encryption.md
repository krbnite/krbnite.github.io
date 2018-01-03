So you have a personal AWS account, do ya?! And you've used the boto3 python package to transfer files 
from your laptop to S3, you say?!

That's all good and all, but... Is it "corporate" good?

Short Answer: No.  (Slightly longer answer: Nope.) The mighty corporate gods require security, 
and security almost always means whatever you learned on your laptop at home needs some tweaking.

If you run into this issue, the following might help (assuming AES-256 encryption).

```python
import boto3
s3r = boto3.resource('s3')
s3r.meta.client.upload_file(
    'myCsvFile.csv', 
    'bucketName', 
    'path2file/myCsvFile.csv',
    ExtraArgs={'ServerSideEncryption': 'AES256'})
```

You did it!
