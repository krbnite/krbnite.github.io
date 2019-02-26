---
title: Arguments with Python
layout: post
tags: python automation etl wwe
---

So you use crontab to automate some data collection for one of some big media company's many 
YouTube channels.  They think it's awesome: can you do it for all the channels?

Yes, you can -- and the best way is to have your python script take some command line arguments. Here 
is an example of how you do that (bonus: working with Redshift and S3).

```python
import argparse
if __name__ == '__main__':
    #-------------------------------------------
    # Command Line Arguments
    #-------------------------------------------    
    parser = argparse.ArgumentParser()
    parser.add_argument("name", choices = ['flashy_channel', 'dramatic_channel', 'funny_channel'], 
            help="name of YouTube channel")
    parser.add_argument('-e', '--etl', choices = ['redshift', 's3'], default='redshift',
        help="data storage location"
    )
    parser.add_argument("--not-a-test", action="store_true",
        help="Set this flag to insert data into production table. "+\
             "Default: Data is inserted into a test table."
    )
    args = parser.parse_args()
    #-------------------------------------------    
    # User Choices
    #-------------------------------------------    
    channelId = name_to_id[args.name]
    etl = args.etl

    #-------------------------------------------
    # Data Collection (Extract, Transform)
    #-------------------------------------------    
    print("\nUsing Data API to get current metrics...")
    data = get_channel_uploads_video_metrics(channelId)
    
    #-------------------------------------------
    # Load Data to SomeWhere 
    #------------------------------------------- 
    if args.etl[0].lower() == 'r':
        # Redshift
        if args.not_a_test:
            print("This is NOT a test: inserting data into production table")
            table_name = 'krbn_youtube_daily_video_metrics'
        else:
            print("Testing: inserting data into "+\
                "busgrp.krbn_youtube_daily_video_metrics_test")
            table_name = 'krbn_youtube_daily_video_metrics_test'
        try:
            print('\nInserting into Redshift')
            dataframe_to_redshift (data, table_name, con)
        except:
            print('Redshift Fail')
    else:
        # S3
        data.to_csv(channelId+'.csv', index=False)
        s3r = boto3.resource('s3')
        try:
            with open(fileName,'rb') as FILE:
                s3r.Bucket('<default-bucket-name>').put_object(
                    Key='<default-key-path>'+fileName,
                    Body = FILE)
        except:
            print('S3 Fail')

```
