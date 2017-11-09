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
```
 
```
# Verify system has gcc installed
#  -- check!
gcc --version
```

``` 
# System must have development packages consistent w/ the kernel headers
uname -r    #  show kernel version on system
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
