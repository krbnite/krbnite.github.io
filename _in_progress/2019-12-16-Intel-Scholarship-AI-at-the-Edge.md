---
title: AI at the Edge (Intel Scholarship)
layout: post
tags: ai edge easi
---

The term "edge" is funny in a way because it's literally defined as "local computing", or
"computing done nearby, not in the cloud."  10 years ago this was just called "doing computer
stuff."  

Jokes aside, despite the above having some truth to it, "edge computing" is usually 
reserved for "doing computer stuff" on
devices that aren't exactly mainstream computers, e.g., on a smarthome security camera,  a
smart refrigerator, or a wearable device -- platforms that typically have constraints 
that laptops or tablets don't have (low storage, limited processing power, low memory,
limited energy, lack of a screen, etc).  

For example, whenever you ask Siri or Alexa a question on your smartphone, TV remote, 
or speaker next to your bed, that question gets catapulted
into the cloud and the answer hurdles back down to your device.  

The gist is that a lot of these things (i) didn't exist before the cloud and (ii) relied
on cloud computing resources when they came into existence.  The cloud is great, but
it might not always be available (network issues) or the back-and-forth between the
device and the cloud might too laggy (latency issues).  

For example, let's say
you put a video camera on the top of Mount Everest that can only hold up to 24 hours
of video.  The objective is to identify and store the footage of a bar-headed 
goose whenever one flies within the camera's field of view.  However, the video camera is not
connected to a WiFi and doesn't have enough power to transmit data to overhead spacecraft,
so you must collect it in person once a month.  What do you do?  Well, for one,
 use the simplest, most efficient CNN you can onboard (remember, we are low on storage,
memory, energy, and compute power).  This is edge computing!  Doing this, we need only store 
those intervals of time that positively classify as a bar-headed goose sigting.  No 
cloud needed.

Another example: wearables data.  Wouldn't it be nice if you could have a digital twin
of yourself to periodically inspect?  A virtual emodiment of your biological state: your
heart rate, respiration rate, anxiety levels, fitness level, and so on, there to 
investigate and lend a quantitative understanding.  With the sensors onboard wearables, this
is possible, but the current trend is in cloud computing: the devices measure this
data, stream it to your mobile phone via Bluetooth, which streams it to the cloud for
analysis and storage.  But what if you don't want all this information about you
shared with some corporate entity, or stored on some server waiting to be hacked?  And
how about when wearables start gaining more sensitive insights into your health, and your
physical and psychological state?  The issue of sharing this data becomes more
troublesome.  Edge computing is the answer!

Finally, how about self-driving cars: are you comfortable with the increased latency involved
with sending data back and forth, to and from the cloud?  If an autonomous vehicle is about to hit
a person in the middle of the road, wouldn't it be nice to know it can make a split-second
decision on the fly -- even out in the middle of nowhere, where there is no cloud in sight?!

You get the idea!  For low-resource devices, the
cloud is often the only option -- but things are changing.  

# Topics Covered
In this course, we focus primarily on Intel's distribution of OpenVINO (described below).  

We will
first explore the Open Model Zoo, which hosts a bunch of [pre-trained models](https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models) optimized for Intel processors (these models include
things like age and gender detection, facial landmarks detection, human pose estimation, license plate
detection, and so on).  These various models can be chained together to form highly optimized
computer vision pipelines (e.g., a face detector followed by an age and gender estimator).  Moreover,
since they are pre-trained, you do not need a ton of data to train your model for these activities.

Next, we go over the Model Optimizer, which takes in models you've trained in some popular
deep learning framework, such as TensorFlow or PyTorch, and optimizes these models for 
inference on Intel processors; the optimized models are saved in what's called the Intermediate
Representation (IR).  More on all this below.

We will then cover the Inference Engine, which uses the IR and model input to efficiently perform inference.

Finally, we cover various edge deployment topics, e.g., something called the MQTT architecture, which
is used to publish data from an edge model to the web.

A major takeaway is that Intel's OpenVINO is not for training models: it's for deploying pre-trained
models as efficiently as possible on Intel processors (whether it's one of your own pre-trained models,
or one that comes stock).

# Work Environment
The course actually provides notebook environments hooked up with all the hardware and software
you need.  There is also an option to install things locally (below, I find out that my 
Macbook Pro has a 4th generation i7 Core, which is not sufficient for Intel's OpenVINO; however,
I ordered a neural compute stick 2).  Finally, we can sign up for the Intel DevCloud....



# Intel's OpenVINO
OpenVINO stands for Open Visual Inference and Neural Network Optimization.  It is an acceleration library, optimized
for deep learning-oriented computer vision applications running Intel hardware, such as their CPUs, GPUs,
FPGAs (field-programmable gate array), and VPUs (visual processing units).  You can power up
Rasberry Pi's with Intel's Neural Compute Stick 2 (NCS2).

What is great is that OpenVINO isn't some alternative or replacement with a deep learning library
that you're already familiar with, like Tensorflow.  Instead, it is a 
[model optimizer](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_Deep_Learning_Model_Optimizer_DevGuide.html) for 
optimizing the models
outputted by your favorite deep learning library (TF, Caffe, MXNet, and more).  For example, though
dropout layers are important during training, they are not used during inference.  When deploying the
model on a low-resource device, any optimization helps -- so in this case, OpenVINO will strip away
the dropout layers for the deployed model.  It will also figure out ways to combine layers or discard
layers that are unnecessary.  The optimized model is returned as an Intermediate Representation (IR),
which is a standardized 2-file representation of your trained model: a model.xml file that describes
the network topology, and a model.bin file that contains the weights and biases.  The IR 
can then be read by the [Inference Engine](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_inference_engine_intro.html),
which is a C++ library used for inference (e.g., image classification, bounding box inference, etc); this
can be deployed on your edge device.

Note that, at least as far as its marketing is concerned, OpenVINO is for computer vision devices, e.g.,
smartphone camera applications, security camera applications, autonomous driving / robotic vision, etc (it basically
expands the OpenCV universe).  However,
at every step I'm going to be thinking about how to use what I'm learning for sensors typically onboard
wearable devices, which sometimes include a camera, but more often includes sensors like accelerometers,
gyroscopes, magnetometers, photoplethysmographs (PPGs), and Galvanic Skin Response (GSR) sensors (aka
electrodermal activitiy, or EDA, sensors).  


# Installing OpenVINO on MacOS
In the Udacity course, we are provided with a notebook environment that houses all the software
we will need.  That is great for playing around the first few times, but I would feel remiss if I 
didn't directly download Intel's OpenVINO software directly to my laptop for a more realistic, intimate
learning setting.

These notes follow along with Intel's on [installation guide](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_macos.html).

One of the first things to do is check what kind of CPU you have. 
* Click on the Apple Icon at top left of you screen
* Click on About this Mac

Hint: if it isn't Intel,
you're temporarily f^&#3d!  On the website, the following Intel CPUs are listed:
* 6th-10th Generation Intel® Core™
* Intel® Xeon® v5 family
* Intel® Xeon® v6 family

If you do not have one of these Intel CPUs, at the least you can buy
a [Neural Compute Stick 2 (NCS2)](https://software.intel.com/en-us/neural-compute-stick).  


Turns out I have the 2.8 GHz Intel Core i7.

But how does this map to the 5ht-10th generation requirement?  When I googled this question,
I came to the following [Intel page listing and an outstanding amount of Core i7 models], where 
it show i7 Cores going from 10th generation down to 5th generation, so I might be in luck.  At any rate, 
it looks like I have to dig a little deeper to figure out exactly what processer I have.

I looked a little more into the hardware specs on "About this Mac," but couldn't find further 
specification easily, so I googled again and found this helpful page on 
[everymac.com](https://everymac.com/systems/by_processor/intel-core-i7-macs.html)
that lists the Core i7 CPUs used on my Macbook model.  Looks like it's this one:
* MacBook Pro 15-Inch "Core i7" 2.8 Mid-2015 (IG)2.8 GHz Core i7 (I7-4980HQ)

Bad news: it wasn't on the page that went down to 5th generation processors, which I
suspected would happen when I saw the model number "4980HQ", so I google this processor... Yep!
It's 4th generation.  

**I officially need the NCS2.**
* And it's bought! (Yay impulsivity.)

Do I have the other requirements?
* CMake 3.4+:  No, I had CMake 3.13
 - just ran `brew upgrade cmake`, but it only upgraded it to 3.16
 - wow, I just realized that `13 > 4`
 - I guess I was reading `3.13` as `3.1.3` in my head
 - anyway, problem solved! :-p
* Python 3.5+:  Yes.
* Apple XCode Command Line Tools: Yes.
* (Optional) Apple XCode IDE (maybe, but haven't used it much)
* MacOS 10.14.4: Yes.

Ok, to be continued...  
* [Intel's OpenVINO MacOS Installation Guide](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_macos.html)
* [Getting started with the NCS2](https://software.intel.com/en-us/articles/get-started-with-neural-compute-stick)

-----------------------------------------

# References & Further Reading

### Some Online Courses
* [OpenVINO Toolkit YouTube Course](https://www.youtube.com/playlist?list=PLDKCjIU5YH6jMzcTV5_cxX9aPHsborbXQ)
* [Intel's own Edge AI Course](https://software.seek.intel.com/DataCenter_to_Edge_REG)

### Some Intel Installation/Developer Guides
*[Install OpenVINO Toolkit on Mac](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_macos.html)
* [Intel's OpenVINO Model Optimizer Developer Guide](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_Deep_Learning_Model_Optimizer_DevGuide.html)
* [Intel's OpenVINO Inference Engine Developer Guide](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_inference_engine_intro.html)
* [Getting started with the NCS2](https://software.intel.com/en-us/articles/get-started-with-neural-compute-stick)

### Some Blogs
* Towards Data Science: [Introduction to OpenVINO](https://towardsdatascience.com/introduction-to-openvino-897e705a1f0a)

### Some Demos on GitHub
* [OpenVINO Face Detection in Python](https://github.com/BenedictusAryo/OpenVino_face-detection_python)
* [OpenVINO Driver Behavior](https://github.com/incluit/OpenVino-Driver-Behaviour)
* [OpenVINO for SmartCity](https://github.com/incluit/OpenVino-For-SmartCity)


### Some Rasberry Pi Stuff
* [OpenVINO and Movidius NCS on the Rasberry Pi](https://www.pyimagesearch.com/2019/04/08/openvino-opencv-and-movidius-ncs-on-the-raspberry-pi/)
