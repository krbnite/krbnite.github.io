---
title: Intel at the Edge (Deploying an Edge App)
layout: post
tags: edge ai easi
---

In this lesson, we focus more on the operational aspects of deploying a
edge app: we dive deeper into analying video streams, partcicularly those
streaming from a web cam; we dig into the pre-trained landscape, learning
to stack models into complex, beautiful pipelines;  and we learn about
a low-resource telemetry transport protocol, MQTT, which we employ
to send image and video statistics to a server.


# OpenCV Basics
OpenCV is a highly-optimized computer vision library built in C++, but callable
from Python environments using the `cv2` API. 

For an Edge app, one of the most useful shorthands that OpenCV provides is the 
ability to capture video streams, particularly those coming from a web cam.  It
has many convenient image processing functions as well, including various
computer vision techniques (e.g., Canny edge detction) as well as machine learning
techniques (e.g., it has its own neural network capabilities, though this is
not its recommended use case).

Some useful functions for Edge apps include:
* `cv2.VideoCapture`
* `cv2.resize`
* `cv2.cvtColor`
* `cv2.rectangle`
* `cv2.imwrite`



* [Official OpenCV-Python Tutorials](https://docs.opencv.org/master/d6/d00/tutorial_py_root.html)
* [Video Streaming in the Jupyter Notebook](https://towardsdatascience.com/video-streaming-in-the-jupyter-notebook-635bc5809e85)
* Maarten Breddels: [ipywebrtc examples](https://hub.gke.mybinder.org/user/maartenbreddels-ipywebrtc-pv0g5riu/tree/docs/source)
  - e.g., [CameraStream](https://hub.gke.mybinder.org/user/maartenbreddels-ipywebrtc-pv0g5riu/notebooks/docs/source/CameraStream.ipynb)
  


Things to get back to:
* https://software.intel.com/en-us/articles/get-started-with-neural-compute-stick
* http://www.jwrr.com/ncs2-2/
* https://docs.openvinotoolkit.org/2019_R3.1/_docs_install_guides_installing_openvino_docker_linux.html
