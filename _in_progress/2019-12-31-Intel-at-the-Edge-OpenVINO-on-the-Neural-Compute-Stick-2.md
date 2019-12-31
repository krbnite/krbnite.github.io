---
title: Intel at the Edge (OpenVINO on the Neural Compute Stick 2)
layout: post
tags: edge ai easi
---

This took a while -- most of that while getting the Linux Docker working right on my 
Mac (from actually building without error, to not throwing errors during the OpenVINO
test demos, to connecting a GUI).  

Anyway...  Here we are.

# LSUSB
To get things working on MacOS, all the installation guide for MacOS says is that you
will need to install `libusb`.  This didn't seem to do anything for me...  Then I accidentally
brew installed `lsusb` when I was tinkering around.  This at least provided a sanity check:
the NCS2 was indeed being recognized by my Mac.

```
brew install lsusb
```

You can then see if you NCS2 is recognized:
```
lsusb | grep Movidius
  Bus 020 Device 010: ID xxxx:xxxx xxxx Movidius MyriadX  Serial: xxxxxxxx
```

I then tried to run a demo to see what happens:
```
cd /opt/intel/openvino/deployment_tools/demos/
sudo ./demo_squeezenet_download_convert_run.sh -d MYRIAD
```

However, as expected, this crashes... Here's the last few lines of output:
```
Run ./classification_sample_async \
  -d MYRIAD \
  -i /opt/intel/openvino_2019.3.376/deployment_tools/demo/car.png \
  -m /Users/kevinurban/openvino_models/ir/public/squeezenet1.1/FP16/squeezenet1.1.xml

[ INFO ] InferenceEngine: 
	API version ............ 2.1
	Build .................. 32974
	Description ....... API
[ INFO ] Parsing input parameters
[ INFO ] Parsing input parameters
[ INFO ] Files were added: 1
[ INFO ]     /opt/intel/openvino_2019.3.376/deployment_tools/demo/car.png
[ INFO ] Creating Inference Engine
	MYRIAD
	myriadPlugin version ......... 2.1
	Build ........... 32974

[ INFO ] Loading network files
[ INFO ] Preparing input blobs
[ WARNING ] Image is resized from (787, 259) to (227, 227)
[ INFO ] Batch size is 1
[ INFO ] Loading model to the device
E: [ncAPI] [    950632] [] ncDeviceOpen:859	Device doesn't appear after boot
[ ERROR ] Can not init Myriad device: NC_ERROR
Error on or near line 217; exiting with status 1
```

Oddly, once you do this, the NCS2 info changes in the `lsusb` output:

```
Bus 020 Device 011: ID zzz:zzzz zzzz VSC Loopback Device  Serial: zzzzzzzzzzzzzzzz
```

You can guess that this is true by the process of elimination (the other USB devices listed
remain the same), and further by googline "VSC Loopback Device".  


----------------------------------------

# LIBUSB


------------------------------------------------------

Basically, as far as Intel's U-Net docker is concerned, adding in NCS2 is as simple
as including this in the Dockerfile (where the Myriad USB Boot rules file is included in
their Dockerfile's directory):
For NCS2, we add the following....

```docker
# USB rules for Myriad
RUN cp ${APP_DIR}/97-myriad-usbboot.rules /etc/udev/rules.d/
RUN echo "udevadm control --reload-rules" >> ~/.bashrc
RUN echo "udevadm trigger" >> ~/.bashrc

# Cleanup
RUN rm -f ${APP_DIR}/97-myriad-usbboot.rules
```

According to the official installation guide though, these rules are included
in the installation directory -- just have to run another bash script.


----------------------

# References & Further Reading
* [OpenVINO MacOS Installation Guide](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_macos.html)
  - at the time of this writing, this page refers to using NCS2 w/ MacOS at the bottom
    of the page, but provides almost no details other than noting that you need to `brew install libusb`
* [NCS2 Getting Started Guide](https://software.intel.com/en-us/articles/get-started-with-neural-compute-stick)
  - at the time of writing, this is another Intel page that implicitly refers to MacOS as having 
    the capability of working with NCS2 (listed as a supported OS), but with not a further mention of it
    anywhere else (there are only guides for Linux, Windows, and Rasberry Pi); my assumption is that
    they intend for a MacOS user to use something like VirtualBox or Docker (which is why I spent so
    much time finagling with Linux Dockers in my previous post)
* JWRR blog: Good posts on trials and errors of getting NCS2 to work
  - http://www.jwrr.com/ncs2-1/
  - http://www.jwrr.com/ncs2-2/
  
  
