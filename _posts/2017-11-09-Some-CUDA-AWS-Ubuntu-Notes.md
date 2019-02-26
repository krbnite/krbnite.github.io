---
title: Some CUDA AWS/Ubuntu Notes
layout: post
tags: unix-tools tensor-flow wwe
---

Been cleaning up my work email and found these notes on installing CUDA on an EC2 instance 
from back in April.  Figured some of it could potentially come in handy one day.

These commands should work on Ubuntu, but no promises for a Mac.

### Verify machine has CUDA-capable GPU
```
lspci | grep -i nvidia

  00:03.0 VGA compatible controller: NVIDIA Corporation GK104GL [GRID K520] (rev a1)
```
Check!

### Verify that we have supported version of Linux  (should see x86_64 for 64-bit)
```
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
Check!
 
### Verify system has gcc installed
```
gcc --version

  gcc (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609
  Copyright (C) 2015 Free Software Foundation, Inc.
  This is free software; see the source for copying conditions.  There is NO
  warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
Check!

## System must have development packages consistent w/ the kernel headers
``` 
uname -r    #  show kernel version on system

  4.4.0-75-generic
```

### Latest kernel headers and development packages
```
# Maybe this will help
sudo apt-get install linux-headers-$(uname -r)    
```

## Environment Variables
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

## More about our installation of CUDA
### GOAL: check to see if CUDA samples properly installed and if GPU is found
```
cd /usr/local/cuda/samples/1_Utilities/deviceQuery
sudo make       # compiles sample code w/in directory  (sudo gives superuser permissions)
# -- Run deviceQuery (should see info and Result=PASS)
./deviceQuery     # below is my output  (OUTCOME:  Cuda Samples installed and GPU found)
```

<figure>
<img src="./images/cuda-deviceQuery.png"
</figure>

### More checks on CUDA installation:  http://xcat-docs.readthedocs.io/en/stable/advanced/gpu/nvidia/verify_cuda_install.html
```
cd /usr/local/cuda/samples/1_Utilities/bandwidthTest
sudo make
./bandwidthTest   # OUTPUT
```
<figure>
<img src="./images/cuda-bandwidthTest.png"
</figure>

## Some Resources
* [CUDA GPU Info](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4VZnqTJ2A)
* http://expressionflow.com/2016/10/09/installing-tensorflow-on-an-aws-ec2-p2-gpu-instance/
* https://github.com/fluxcapacitor/pipeline/wiki/AWS-GPU-Tensorflow-Docker
* http://xcat-docs.readthedocs.io/en/stable/advanced/gpu/nvidia/verify_cuda_install.html
* https://livingthing.danmackinlay.name/how_is_amazon_cloud_number_crunching_awful.html

### Some Software Specs of our Linux Instance
* [Python 3.5.2](https://www.python.org/download/releases/3.5.2/)
* [TensorFlow 1.0.0](https://pypi.python.org/pypi/tensorflow/1.0.0)
* [Ubuntu 16.04 (xenial) x86_64](http://releases.ubuntu.com/16.04/)
* [gcc 5.4.0](https://gcc.gnu.org/gcc-5/)
* [NVIDIA GK104GL (GRID K520)](http://www.nvidia.com/object/cloud-gaming-gpu-boards.html)
* [CUDA 8.0](https://developer.nvidia.com/cuda-80-ga2-download-archive)
