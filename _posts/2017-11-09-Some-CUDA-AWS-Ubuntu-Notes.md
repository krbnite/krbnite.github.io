---
title: Some CUDA AWS/Ubuntu Notes
layout: post
---

Been cleaning up my work email and found these notes on installing CUDA on an EC2 instance 
from back in April.  Figured some of it could potentially come in handy one day.

[CUDA GPU Info](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4VZnqTJ2A)

These commands should work on Ubuntu, but no promises for a Mac.

```
# Verify machine has CUDA-capable GPU
#   -- check!
lspci | grep -i nvidia

  00:03.0 VGA compatible controller: NVIDIA Corporation GK104GL [GRID K520] (rev a1)
```
 
```
# Verify that we have supported version of Linux  (should see x86_64 for 64-bit)
#  -- check!
uname -m && cat /etc/*release

  x86_64
  DISTRIB_ID=Ubuntu
  DISTRIB_RELEASE=16.04
  DISTRIB_CODENAME=xenial
  DISTRIB_DESCRIPTION="Ubuntu 16.04.2 LTS"
  NAME="Ubuntu"
  VERSION="16.04.2 LTS (Xenial Xerus)"
  ID=ubuntu
  ID_LIKE=debian
  PRETTY_NAME="Ubuntu 16.04.2 LTS"
  VERSION_ID="16.04"
  HOME_URL="http://www.ubuntu.com/"
  SUPPORT_URL="http://help.ubuntu.com/"
  BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
  VERSION_CODENAME=xenial
  UBUNTU_CODENAME=xenial
```
 
```
# Verify system has gcc installed
#  -- check!
gcc --version

  gcc (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609
  Copyright (C) 2015 Free Software Foundation, Inc.
  This is free software; see the source for copying conditions.  There is NO
  warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

``` 
# System must have development packages consistent w/ the kernel headers
uname -r    #  show kernel version on system

  4.4.0-75-generic
```

```
# Maybe this will help
sudo apt-get install linux-headers-$(uname -r)    # latest kernel headers & dvlpmnt pkgs
```

### Environment Variables
I could not import TensorFlow into Python without error: 
> ImportError: libcudart.so.8.0: cannot open shared object file: No such file or directory
 
This basically says that Python/TensorFlow could not detect any the relevant GPU software.  Fortunately, 
googling an error message gets one on the right path pretty quickly.  In particular, [this page](https://github.com/tensorflow/tensorflow/issues/5343)
indicated that we might be missing a necessary environment variable. 
 
I put the following line into the .bashrc start-up file so that this environment variable is available to the OS every time we log in:
```
export LD_LIBRARY_PATH=/usr/local/cuda/lib64
```
 
This fixed the issue.
