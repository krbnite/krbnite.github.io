---
title: Attaching an EBS Volume to a Pre-Existing EC2 Instance
layout: post
tags: aws easi
---

Anyway, point is, despite having used AWS products for several years, I'm no expert -- and always
learning something new.  

This whole intro is really about my recent experience with EBS.  I am working on a certain
human activity recognition (HAR) model using convolutional and recurrent neural networks trained
on high-frequency sensor data.  This is all on a p2.2xlarge, by the way (I think the g2.2xlarges are
now considered to be from the stone age -- and the p2 might not be far off itself).  

Well, truth be told, even before the model got to doing anything, the various pre-processing steps
came to a crash.  My Python script presented the following error:

```python
```

I googled a bit and found my system was somehow probably out of storage.  I went to check
things out at the command line and -- hooo boy! -- things were worse than I thought.  When I tried
using command line completion, I was notified that the machine did not even have enough room to
write the temporary file that is required for command line completion.  

```
ls /t-bash: cannot create temp file for here-document: No space left on device
-bash: cannot create temp file for here-document: No space left on device
```

I was able to run some disk usage commands though, which confirmed that my storage was depleted:

```
du -h
  Filesystem      Size  Used Avail Use% Mounted on
  udev             30G     0   30G   0% /dev
  tmpfs           6.0G  425M  5.6G   7% /run
  /dev/xvda1       78G   78G     0 100% /
  tmpfs            30G   12K   30G   1% /dev/shm
  tmpfs           5.0M     0  5.0M   0% /run/lock
  tmpfs            30G     0   30G   0% /sys/fs/cgroup
  /dev/loop0       90M   90M     0 100% /snap/core/7713
  /dev/loop1       18M   18M     0 100% /snap/amazon-ssm-agent/1480
  /dev/loop3       90M   90M     0 100% /snap/core/7917
  tmpfs           6.0G     0  6.0G   0% /run/user/1001

df -h /usr/bin
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/xvda1       78G   78G     0 100% /
```

