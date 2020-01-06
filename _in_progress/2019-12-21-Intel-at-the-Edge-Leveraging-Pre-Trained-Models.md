---
title: Intel at the Edge (Leveraging Pre-Trained Models)
layout: post
tags: edge ai easi
---


In this part of the course, we go over various computer vision models, specifically
focusing on pre-trained models from the Open Model Zoo available for use with 
OpenVINO.  We think about how a pre-trained model, or a pipeline of them, can
aid in designing an app -- and how we might deploy that app.

Recap: OpenVINO stands for Open Visual Inferencing and Neural Network Optimization.  It is
not a deep learning framework, but a set of tools for optimizing and deploying neural 
networks, which is especially useful on low-resource "edge" devices.  The pre-trained model
can be any lineup from the Open Model Zoo, as well as any of your own pre-trained model
(e.g., some fantastic model you developed in TensorFlow).  To optimize, a pre-trained model
is run through the Model Optimizer, which spits out an Intermediate Representation (IR) -- an XML
file representing the network topology and a .bin file that records the weights and biases.  This
IR of the model can then be used by the Inference Engine in the deployment environment (e.g., 
some IoT device).


## The Open Model Zoo!
The Model Zoo contains a ton of pre-trained models, many already in the Intermediate Representation
(IR), ready to go.  If there is a Zoo model you don't yet have, then you the Model
Downloader to get it.

In this course, we focus on 3 computer vision models types from the Model Zoo:  
* Classification
* Detection
* Segmentation
    
What are these things and how are they different?  In the class, we cover
this briefly and some references are provided...which also discuss localization, adding
in more computer vision jargon.  I found an external reference
pretty helpful: [Detection and Segmentation through ConvNets](https://towardsdatascience.com/detection-and-segmentation-through-convnets-47aa42de27ea)

* Classification
  - this is your typical cat-or-dog, hotdog-or-no-hotdog model, where we train
    a network to simply specify whether or not an images contains a specific class
  - this type of computer vision is a "low resolution" technique giving no information concerning
    where in the image the class is located, whether more than one class instance is in the image,
    which pixes correspond to the class, etc
  - the most simple form of this is labeling an image with the most probable class from a softmax output layer 
  - multi-label classification expands on this idea by using a vectorial sigmoid output layer (basically, it
    does a separate binary classification for each potential class) 
* Localization
  - this is classification, combined with localizing where in the image is most
    determining the classification 
  - these models draw a bounding box around the class locale, but are not necessarily sophisticated enough to
    parse two instances of the same class that are right next each other
  - in the segmentation parlance below, this might be called "semantic localization"
* Detection
  - this goes one step beyond basic localization, attempting to isolate each instance of a class
    in its own bounding box
  - usually you see this doing multi-label clasification with localization of each detected class
  - the bounding box is found by setting up a regression problem: determining (x,y,h,w)
  - in the segmentation parlance below, this might be called "instance localization"
* Semantic Segmentation
  - this is a fairly "high resolution" computer vision technique, which classifies each pixel as
    belonging to a class of interest or not, ultimately creating an class mask 
  - this goes beyond bounding boxes, e.g., if
    you are looking for cars, car pixels will identified in an image (not just a box
    around the car)
  - this technique will treat any pixel of class C as the same object, e.g., if 3 cats are
    next to each other, a "cat mask" will be created that covers all 3 cats
  - From [Intel's OpenVINO Models page](https://docs.openvinotoolkit.org/latest/_models_intel_index.html): 
    "Semantic segmentation is an extension of object detection problem. Instead of returning bounding boxes, semantic segmentation models return a "painted" version of the input image, where the "color" of each pixel represents a certain class. These networks are much bigger than respective object detection networks, but they provide a better (pixel-level) localization of objects and they can detect areas with complex shape (for example, free space on the road)."
* Instance Segmentation
  - like semantic segmentation, but urther labels each pixel of class C with which object it belongs to, 
    e.g., O1, O2, or O3 if there are 3 cats sitting next to each other
  - From [Intel's OpenVINO Models page](https://docs.openvinotoolkit.org/latest/_models_intel_index.html): 
    "Instance segmentation is an extension of object detection and semantic segmentation problems. Instead of predicting a bounding box around each object instance instance segmentation model outputs pixel-wise masks for all instances."

Note that there exist other model types that do not perfectly conform to these categories, e.g., 
pose estimation and text recognition.

Anyway, the above categories are generalized descriptions of the activities we might
want to do in computer vision.  Next, we cover a few specific neural architectures that are
implemented to perform one of these tasks -- all of which are available in the Open Model
Zoo.

There is the **Single Shot Multi-Box Detector** (called **SSD** for short).  This network looked
a default set of bounding boxes to make classifications on at various layers in the CNN.  That is,
a classification might be made at input resolution, but then again at a downsampled resolution (where
the downsampling results from strided convolutions).

The **ResNet** idea is to provide shortcut connection (or skip connection) between various layers, so that the
same basic architectures and schemes can be used for a computer vision problem, but with
much deeper networks (that is, besides the shortcut connection, this technique doesn't require
any further creativity -- just go deeper!).  You can think of any group of layers that are
wrapped by a shortcut connection as a ResNet cell.  Basically, if in a plain CNN, that cell of
equi-shaped layers was supposed to approximate a function H(x) = x + F(x), the the ResNet cell just
needs to learn F(x)...which helps fight the vanishing gradient problem in very deep networks.  For
smaller networks, the shortcut allows for more efficient learning (faster training).  

**MobileNet** focuses on optimizing pragmatic endpoints, as opposed to just accuracy.  The idea is
to provide a parameterized tradeoff between accuracy and things like network size and latency.  This
architecture allows for a less dramatic loss in accuracy when biasing in favor of smaller size and
faster inference.  A central idea in this architecture is in using factorized convolutions.  Basically,
a decomposition of a convolution into two sub-activities: depthwise and 1x1 convolutions.

Here's a deeper look at these architectures and more:

* 2014: Szegedy et al: [Going deeper with convolutions](https://arxiv.org/pdf/1409.4842.pdf)
  - this is the "Inception paper"
* 2015 (Jun): Ren et al: [Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks](https://arxiv.org/abs/1506.01497)
  - here are previous incarnations of R-CNN
    * 2014 (original R-CNN paper): Girshick et al: [Rich Feature Hierarchies for Accurate Object Detection and Semantic Segmentation](http://openaccess.thecvf.com/content_cvpr_2014/html/Girshick_Rich_Feature_Hierarchies_2014_CVPR_paper.html)
    * 2015: Girshick:  [Fast R-CNN](http://openaccess.thecvf.com/content_iccv_2015/html/Girshick_Fast_R-CNN_ICCV_2015_paper.html)
* 2015 (Jun): Redmon et al: [You Only Look Once: Unified, Real-Time Object Detection](https://arxiv.org/abs/1506.02640)
  - the "YOLO paper"
* 2015 (Dec): He et al: [Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385)
  - this is the "ResNet paper"
* 2015 (Dec): Liu et al:  [SSD: Single Shot MultiBox Detector](https://arxiv.org/abs/1512.02325)
* 2017: Howard et al:  [MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications](https://arxiv.org/abs/1704.04861)


You will find that the Open Model Zoo has two major branches:
* the Public Model Set
* the Free Model Set

In terms of distributing models, the terms "public" and "free" almost seem synonymous.  For Intel's
OpenVINO distrubtion they mean slightly different things:
* the models found in the Public Model Set have not been run through the Model Optimizer, so you
  must do this yourself (the benefit being that you can customize these models before creating 
  an Intermediate Representation for the Inference Engine)
* the models from the Free Model Set have already been run through the Model Optimizer, thus come
  to you in IR format
  
The various models can be obtained by using Intel's OpenVINO Model Downloader, found the 
OpenVINO installation directory:
```bash
<OPENVINO_INSTALL_DIR>/deployment_tools/open_model_zoo/tools/downloader
```

You can take a closer
look at all the pre-trained models at Intel's [pre-trained model] page.  When you click on a model,
you will find more info about it, e.g., how fast it is, how big it is, etc.


# Exercise: Loading Pre-Trained Models
In this exercise, we look over the [pre-trained model list](https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models) to identify models of interest, which we can then download
using the OpenVINO's Model Downloader.  To do so, we must use the model's exact name, which can be
found on a [more detailed pre-trained model list](https://docs.openvinotoolkit.org/latest/_models_intel_index.html).


### Identify the Desired Models
Using the [pre-trained model list](https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models),
we seek to identify three models with the following capabilities, respectively:
* Human Pose Estimation
* Text Detection
* Determining Car Type & Color

This is as simple as finding these terms on the page:
* [Human Pose Estimation](https://docs.openvinotoolkit.org/latest/_models_intel_human_pose_estimation_0001_description_human_pose_estimation_0001.html)
  - "This is a multi-person 2D pose estimation network (based on the OpenPose approach) with tuned MobileNet v1 as a feature extractor. It finds a human pose: body skeleton, which consists of keypoints and connections between them, for every person inside image. The pose may contain up to 18 keypoints: ears, eyes, nose, neck, shoulders, elbows, wrists, hips, knees and ankles."
  - INPUT: image array of shape [BxCxHxW] = [1x3x256x456], where:
    * B - batch size
    * C - number of channels
    * H - image height
    * W - image width
    * Expected color order is BGR
  - OUTPUT: "The net outputs two blobs with shapes: [1, 38, 32, 57] and [1, 19, 32, 57]. The first blob contains keypoint pairwise relations (part affinity fields), the second one contains keypoint heatmaps."
  - REFERENCE: Cao et al (2017): [Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields](https://arxiv.org/pdf/1611.08050.pdf)
* [Text Detection](http://docs.openvinotoolkit.org/latest/_models_intel_text_detection_0004_description_text_detection_0004.html)
  - "Text detector based on [PixelLink](https://arxiv.org/pdf/1801.01315.pdf) architecture with [MobileNetV2](https://arxiv.org/pdf/1801.04381.pdf), depth_multiplier=1.4 as a backbone for indoor/outdoor scenes."
  - INPUT:  input image of shape [BxCxHxW] = [1x3x768x1280], where B, C, H, W are described above (expected color order - BGR)
  - OUTPUT:
    * [1x2x192x320] - logits related to text/no-text classification for each pixel
    * [1x16x192x320] - logits related to linkage between pixels and their neighbors
  - REFERENCE: Deng et al (2018): [PixelLink: Detecting Scene Text via Instance Segmentation](https://arxiv.org/pdf/1801.01315.pdf)
* [Determining Car Type & Color](https://docs.openvinotoolkit.org/latest/_models_intel_vehicle_attributes_recognition_barrier_0039_description_vehicle_attributes_recognition_barrier_0039.html)
  - "This model presents a vehicle attributes classification algorithm for a traffic analysis scenario."
  - "Conduct an initial analysis and present back-key metadata for faster sorting and searching in the future. The average color accuracy for the model is over 82 percent for red, white, black, green, yellow, gray, and blue. Its average vehicle-type attribution is over 87 percent for cars, vans, trucks, and buses."
  - INPUT: input image of shape [BxCxHxW] = [1x3x72x72], where B, C, H, W are described above (expected color order - BGR)
  - OUTPUT: 
    * color: shape: [1, 7, 1, 1] - Softmax output across seven color classes [white, gray, yellow, red, green, blue, black]
    * type:  shape: [1, 4, 1, 1] - Softmax output across four type classes [car, bus, truck, van]
  - NOTES:
    * the car must be forward facing
    * less than 50% occlusion
  - In a ML Pipeline, you might first employ a [vehicle detection model](https://docs.openvinotoolkit.org/latest/_models_intel_vehicle_detection_adas_0002_description_vehicle_detection_adas_0002.html) -- and only deploy the vehicle attribute recognition model on images where a vehicle is detected (in this case, 
  and second binary classication "filter model" might be used to determine whether the vehicle is forward facing, and
  so on)
  
### Download the Desired Models
It's helpful to look at the [Model Downloader](https://docs.openvinotoolkit.org/latest/_tools_downloader_README.html) documentation.

```
cd /opt/intel/openvino/deployment_tools/open_model_zoo/tools/downloader
python3 -mpip install --user -r ./requirements.in
```

```
# Human Pose Estimation (all precisions)
sudo ./downloader.py --name human-pose-estimation-0001

# Text Detection (FP16 precision)
sudo ./downloader.py --name text-detection-0004 --precisions FP16

# Vehicle Attribute Recognition
sudo ./downloader.py --name vehicle-attributes-recognition-barrier-0039 --precisions INT8
```

Note that if you're in root mode, then the `sudo` isn't necessary.  For example, this is the case
if you are doing this in Udacity's provided notebook environment, where you also have to explicitly specify
the output directory like so:

```
# Udacity Workspace
DL="/opt/intel/openvino/deployment_tools/open_model_zoo/tools/downloader"
$DL/downloader.py --name human-pose-estimation-0001 -o /home/workspace
$DL/downloader.py --name text-detection-0004 --precisions FP16
$DL/downloader.py --name vehicle-attributes-recognition-barrier-0039 --precisions INT8

```

On my local machine, the models get saved in:
```
models="/opt/intel/openvino/deployment_tools/open_model_zoo/tools/downloader/intel/"
ls $models
human-pose-estimation-0001                  vehicle-attributes-recognition-barrier-0039
text-detection-0004
```

Specific models get saved in model/precision subdirectories, e.g.:
```
# FP16 Human Pose Estimation
/opt/intel/openvino/deployment_tools/open_model_zoo/tools/downloader/intel/human-pose-estimation-0001/FP16
```

In the Udacity workspace, the models are saved elsewhere since we explicitly provided an output directory:
```
models="/home/workspace/intel"
ls $models
human-pose-estimation-0001                  vehicle-attributes-recognition-barrier-0039
text-detection-0004
```


# Exercise

```python
import cv2
import numpy as np

def process_image(input_image, h, w):
  '''
  Udacity has make 3 functions, but they all turn out
  to be exactly the same except for the function name, so
  here I simply define the function once.
  '''
  preprocessed_image = cv2.resize(
        np.copy(input_image), 
        (w,h)
    ).transpose((2,0,1)).reshape(1,3,h,w)  
  return preprocessed_image

def pose_estimation(input_image):
    '''
    Given some input image, preprocess the image so that
    it can be used with the related pose estimation model
    you downloaded previously. You can use cv2.resize()
    to resize the image.
    '''
    # TODO: Preprocess the image for the pose estimation model
    w, h = 456, 256
    processed_image = process_image(input_image, h, w)
    return preprocessed_image

def text_detection(input_image):
    '''
    Given some input image, preprocess the image so that
    it can be used with the related text detection model
    you downloaded previously. You can use cv2.resize()
    to resize the image.
    '''
    # TODO: Preprocess the image for the text detection model
    w, h = 1280, 768
    processed_image = process_image(input_image, h, w) 
    return preprocessed_image

def car_meta(input_image):
    '''
    Given some input image, preprocess the image so that
    it can be used with the related car metadata model
    you downloaded previously. You can use cv2.resize()
    to resize the image.
    '''
    # TODO: Preprocess the image for the car metadata model
    w, h = 72, 72
    processed_image = process_image(input_image, h, w)
    return preprocessed_image
```

----------------

# Ugh
I wrote a bunch of notes here...but never committed changes and my computer died. 

There were some links to "further reading."  Maybe I'll come back to this one day and fill
it in.

-----------------

# Exercise: Handling Network Outputs

This exercise is a little trickier, not because it's technically hard but because
there is some reverse engineering going on.  Also, there are TODO's littered around
to Python scripts.

The simplest part is setting out input and output handling in `app.py`.  In the
`perform_inference` function, you basically just have to fill in something like:
```python
def perform_inference(args):
    '''
    Performs inference on an input image, given a model.
    '''
    # ...A BUNCH OF READY-MADE CODE...
    # ...h & w come from up here...
    
    ### TODO: Preprocess the input image
    preprocessed_image = preprocessing(image, h, w)

    # Perform synchronous inference on the image
    inference_network.sync_inference(preprocessed_image)
    
    # Obtain the output of the inference request
    output = inference_network.extract_output()

    ### TODO: Handle the output of the network, based on args.t
    ### Note: This will require using `handle_output` to get the correct
    ###       function, and then feeding the output to that function.
    processed_output = handle_output(args.t)(output, image.shape)
    
    # ...MORE READY-MADE CODE...

```

The preprocessing code in the first `TODO` is basically what we built in the last exercise.  Here, we just
have to make it work in their app code; we know what arguments it takes because it is given to us in the
other script file, `handle_models.py`.  In the second `TODO`, the `handle_output` function is
basically a "factory function" -- it outputs the right output handler based on the args.t parameter (which
can be "POSE", "TEXT", or "CAR_META").  This function is also given in `handle_models.py`, so
our only job here is to make it work in `app.py`.  Once `handle_output` is evaluated, it acts as
one of the model handlers we must build in `handle_models.py`, which all take in the arguments `output` and
`input_shape`.  Honestly, writing all this out makes it seem more complex than it is.  

The trickier part is building out the model handlers.  This takes some experimentation!  

We are told something like: "With
output from Model X, do Y."  But what does the output from Model X look like?  Well, we
can read a description from the model's description page on Intel.  But this is not
quite enough to go on... 

For example, the pose estimation model.  Here is the function we are given to fill out:

```python
import cv2
import numpy as np

def handle_pose(output, input_shape):
    '''
    Handles the output of the Pose Estimation model.
    Returns ONLY the keypoint heatmaps, and not the Part Affinity Fields.
    '''
    # TODO 1: Extract only the second blob output (keypoint heatmaps)
    
    # TODO 2: Resize the heatmap back to the size of the input

    return None
```

From the model's description page, we know the following:
* Outputs:  "The net outputs two blobs with shapes: [1, 38, 32, 57] and [1, 19, 32, 57]. The first blob contains keypoint pairwise relations (part affinity fields), the second one contains keypoint heatmaps."

So we do know we want to keep only the second blob...but how do we access it?  Is the blob held in
a list or a dictionary?  Great question, we can find this out by returning its
type.

```python
def handle_pose(output, input_shape):
    '''
    Handles the output of the Pose Estimation model.
    Returns ONLY the keypoint heatmaps, and not the Part Affinity Fields.
    '''
    # TODO 1: Extract only the second blob output (keypoint heatmaps)
    
    # TODO 2: Resize the heatmap back to the size of the input
    
    print('\n-------------------------------------')
    print('TYPE:',type(output))
    print('-------------------------------------\n')

    return None
```
Then at the command line:
```
python app.py -i "images/sitting-on-car.jpg" -t "POSE" -m "/home/workspace/models/human-pose-estimation-0001.xml"

-------------------------------------
TYPE: <class 'dict'>
-------------------------------------

# ...followed by a bunch of error messages...
```

Ok, let's look at the keys:

```python
def handle_pose(output, input_shape):
  
    print('\n-------------------------------------')
    print(output.keys())
    print('-------------------------------------\n')

    return None
```
Then at the command line:
```
python app.py -i "images/sitting-on-car.jpg" -t "POSE" -m "/home/workspace/models/human-pose-estimation-0001.xml"

-------------------------------------
dict_keys(['Mconv7_stage2_L1', 'Mconv7_stage2_L2'])
-------------------------------------

# ...followed by a bunch of error messages...
```

Great! It looks like the blob we want is from a dictionary with key 'Mconv7_stage2_L2'.

We can confirm by looking at its shape:

```python
def handle_pose(output, input_shape):
  
    # TODO 1: Extract only the second blob output (keypoint heatmaps)
    heatmap = output['Mconv7_stage2_L2']
    
    print('\n----------------------------')
    print(heatmaps.shape)
    print('----------------------------\n')
    
    return None
```
Then at the command line:
```
python app.py -i "images/sitting-on-car.jpg" -t "POSE" -m "/home/workspace/models/human-pose-estimation-0001.xml"

----------------------------
(1, 19, 32, 57)
----------------------------

# ...followed by a bunch of error messages...
```

Ok, yes -- it is the blob we seek!

This array represents a single batch, 19 "channel" 32x57 image.  We need to resize each of
the 19 channels.  We want to return a CxHxW image, where `C=19`, `H=input_H`, and `W=input_W`.  The
pseudocode looks something like:

```
for each channel in heatmap
    resize channel shape to input shape
```

The python code will look something like this:

```python
def handle_pose(output, input_shape):
    '''
    Handles the output of the Pose Estimation model.
    Returns ONLY the keypoint heatmaps, and not the Part Affinity Fields.
    
    NOTE: Input Shape is HxWxC = 750x1000x3
    '''
    # TODO 1: Extract only the second blob output (keypoint heatmaps)
    heatmap = output['Mconv7_stage2_L2'] # BxCxHxW=1x19x32x57 array
    heatmap = heatmap[0] # CxHxW=19x32x57 array

    # TODO 2: Resize the heatmap back to the size of the input
    resized_heatmap = np.zeros([heatmap.shape[0], input_shape[0], input_shape[1]])    
    for channel in range(heatmap.shape[0]):
        resized_heatmap[channel] = cv2.resize(heatmap[channel], input_shape[:2][::-1])

    return resized_heatmap
```

Figuring out the TEXT model handler is the same basic process, and the function itself
is nearly identical:

```python
def handle_text(output, input_shape):
    '''
    Handles the output of the Text Detection model.
    Returns ONLY the text/no text classification of each pixel,
        and not the linkage between pixels and their neighbors.
    '''
    # TODO 1: Extract only the first blob output (text/no text classification)
    textflag = output['model/segm_logits/add'] # BxCxHxW=1x19x32x57 array
    textflag = textflag[0] # CxHxW=19x32x57 array
    
    # TODO 2: Resize this output back to the size of the input
    resized_textflag = np.zeros([textflag.shape[0], input_shape[0], input_shape[1]])    
    for channel in range(textflag.shape[0]):
        resized_textflag[channel] = cv2.resize(textflag[channel], input_shape[:2][::-1])
    return resized_textflag
```

The CAR_META model handler was actually very simple -- no experimentation needed really.

```python
def handle_car(output, input_shape):
    '''
    Handles the output of the Car Metadata model.
    Returns two integers: the argmax of each softmax output.
    The first is for color, and the second for type.
    '''
    # TODO 1: Get the argmax of the "color" output
    argmax_c = np.argmax(output['color'])
    
    # TODO 2: Get the argmax of the "type" output
    argmax_t = np.argmax(output['type'])
    return argmax_c, argmax_t
```

In case you were interested, here is what the model handler "factory function" looks like:

```python
def handle_output(model_type):
    '''
    Returns the related function to handle an output,
        based on the model_type being used.
    '''
    if model_type == "POSE":
        return handle_pose
    elif model_type == "TEXT":
        return handle_text
    elif model_type == "CAR_META":
        return handle_car
    else:
        return None
```

This could basically just be a dictionary, if you wanted.

If you want to see your images, open a Jupyter Notebook.  Note that I'm using
cv2 here to read in BGR images, but plotting using MatPlotLib's `imshow`, which
assumes an image is in RGB format.  To handle this, we use cv2' color conversion
function, `cv2.cvtColor`.

```python
import cv2
import numpy as np
from matplotlib import pyplot as plt
from handle_models import handle_output, preprocessing
%matplotlib inline

car_ = cv2.imread('outputs/CAR_META-output.png')
pose_ = cv2.imread('outputs/POSE-output.png')
text_ = cv2.imread('outputs/TEXT-output.png')

plt.imshow(cv2.cvtColor(pose_, cv2.COLOR_BGR2RGB))

plt.imshow(cv2.cvtColor(text_, cv2.COLOR_BGR2RGB))

plt.imshow(cv2.cvtColor(car_, cv2.COLOR_BGR2RGB))
```
