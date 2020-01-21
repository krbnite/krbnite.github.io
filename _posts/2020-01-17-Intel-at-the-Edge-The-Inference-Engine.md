---
title: Intel at the Edge (The Inference Engine)
layout: post
tags: edge ai easi
---

In this lesson, we go over the basics of the Inference Engine (IE), what devices
are supported, how to feed an Intermediate Representation (IR) to the IE, how
make inference requests, how to handle IE output, and how to integrate this all
into an app.

The [Inference Engine](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Deep_Learning_Inference_Engine_DevGuide.html) 
is the component of OpenVINO that actually runs inference 
on your (pre-trained, IR-converted) model (i.e., it is the thing you feed an input
to, hoping for some type of classification, object detection, prediction, etc).  Following
the tradition of the Model Optimizer, the Inference Engine also further optimizes
the model's performance -- though instead of reducing size and complexity, the IE
focuses on hardware-based optimizations specific to an array of [supported devices
(CPUs, GPUs, FPGAs, VPUs)](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_supported_plugins_Supported_Devices.html).  

Note that the use of the IE varies over its supported
devices, e.g., when using IE on a CPU, there exist CPU extensions to support
additional layer types.  Or, for example, OpenVINO models are often
targeted at using the Neural Compute Stick 2 on the Rasberry Pi, and though
the IE works for this setup, using the Model Optimizer on the Rasberry Pi is not directly 
supported (you must create the IR on another system first).  However, there
are workarounds on the [Intel OpenVINO website](https://software.intel.com/en-us/articles/model-downloader-optimizer-for-openvino-on-raspberry-pi).

To use the IE, there is a C++ API you can integrate into your app, however you
can use the Python wrapper to call upon this API as well, which is what we will 
be doing.  The two main OpenVINO classes that we will use are [IECore](https://docs.openvinotoolkit.org/latest/classie__api_1_1IECore.html) and [IENetwork](https://docs.openvinotoolkit.org/latest/classie__api_1_1IENetwork.html).  For a list of
all Python classes, check [here](https://docs.openvinotoolkit.org/latest/ie_python_api.html).  

The `IECore` represents an Inference Engine entity.  It has no mandatory parameters to initialize an instance,
though there is an optional parameter, `xml_config_file`, which accepts an absolute path to a
`.xml` file that contains plugin information (if not set, a default configuration is assumed).  The
`IENetwork` directly represents the model, read in from the model's two IR files (e.g., `model.xml` and
`model.bin`), which are mandatory parameters. 

By the end of the lesson, we use everything we've learned about pre-trained models, the Model
Optimizer, and the Inference Engine, as well as some bacis of OpenCV, to develop an app that
can be used on a video stream to detect cars and overlay bounding boxes.

# Some Example Code


```python
from openvino.inference_engine import IECore, IENetwork
```

If you get an error here, you likely have to add the OpenVINO library to your Python path before being
able to import anything from it, like so:

```python
import sys
sys.append('/opt/intel/openvino/python/python3')
from openvino.inference_engine import IECore, IENetwork
```

Once successful, initialize an instance of the Inference Engine Core and your network:
```python
core = IECore()
nework = IENetwork(model='path/to/model.xml', weights='path/to/model.bin')
```


Now let's say you want to see what layers are supported on a device:
```python
supported_layers = core.query_network(network, 'CPU')  # potential dev names: CPU, GPU, FPGA, MYRIAD
```

This will output a dictionary whose keys are the network's supported layers and values are
the device name, if supported.  For `squeezenet1.1` on my MacOS, this elicits the following
response for `CPU`:
```python
supported_layers
{'ScaleShift/Add_': 'CPU',
 'conv1': 'CPU',
 'conv10': 'CPU',
 'data': 'CPU',
 'fire2/concat': 'CPU',
 'fire2/expand1x1': 'CPU',
 'fire2/expand3x3': 'CPU',
 'fire2/relu_expand1x1': 'CPU',
 'fire2/relu_expand3x3': 'CPU',
 'fire2/relu_squeeze1x1': 'CPU',
 'fire2/squeeze1x1': 'CPU',
 'fire3/concat': 'CPU',
 'fire3/expand1x1': 'CPU',
 'fire3/expand3x3': 'CPU',
 'fire3/relu_expand1x1': 'CPU',
 'fire3/relu_expand3x3': 'CPU',
 'fire3/relu_squeeze1x1': 'CPU',
 'fire3/squeeze1x1': 'CPU',
 'fire4/concat': 'CPU',
 'fire4/expand1x1': 'CPU',
 'fire4/expand3x3': 'CPU',
 'fire4/relu_expand1x1': 'CPU',
 'fire4/relu_expand3x3': 'CPU',
 'fire4/relu_squeeze1x1': 'CPU',
 'fire4/squeeze1x1': 'CPU',
 'fire5/concat': 'CPU',
 'fire5/expand1x1': 'CPU',
 'fire5/expand3x3': 'CPU',
 'fire5/relu_expand1x1': 'CPU',
 'fire5/relu_expand3x3': 'CPU',
 'fire5/relu_squeeze1x1': 'CPU',
 'fire5/squeeze1x1': 'CPU',
 'fire6/concat': 'CPU',
 'fire6/expand1x1': 'CPU',
 'fire6/expand3x3': 'CPU',
 'fire6/relu_expand1x1': 'CPU',
 'fire6/relu_expand3x3': 'CPU',
 'fire6/relu_squeeze1x1': 'CPU',
 'fire6/squeeze1x1': 'CPU',
 'fire7/concat': 'CPU',
 'fire7/expand1x1': 'CPU',
 'fire7/expand3x3': 'CPU',
 'fire7/relu_expand1x1': 'CPU',
 'fire7/relu_expand3x3': 'CPU',
 'fire7/relu_squeeze1x1': 'CPU',
 'fire7/squeeze1x1': 'CPU',
 'fire8/concat': 'CPU',
 'fire8/expand1x1': 'CPU',
 'fire8/expand3x3': 'CPU',
 'fire8/relu_expand1x1': 'CPU',
 'fire8/relu_expand3x3': 'CPU',
 'fire8/relu_squeeze1x1': 'CPU',
 'fire8/squeeze1x1': 'CPU',
 'fire9/concat': 'CPU',
 'fire9/expand1x1': 'CPU',
 'fire9/expand3x3': 'CPU',
 'fire9/relu_expand1x1': 'CPU',
 'fire9/relu_expand3x3': 'CPU',
 'fire9/relu_squeeze1x1': 'CPU',
 'fire9/squeeze1x1': 'CPU',
 'pool1': 'CPU',
 'pool10': 'CPU',
 'pool3': 'CPU',
 'pool5': 'CPU',
 'prob': 'CPU',
 'relu_conv1': 'CPU',
 'relu_conv10': 'CPU'}
```

To see if there are any layers in your network not supported, you have to compare
your network layer to this list of supported layers.



If you were to find a layer not supported on CPU, then you might be able to 
add a CPU extension that will enable it.  CPU extensions can be found in
`opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/`.  On a Mac,
there is a single CPU extension file, while on Linux you will see several files
for AVX and SSE. 

This is how to add an extension:
```python
core.add_extension(path_to_cpu_extension)
```

When your ready to go live with your model:
```python
core.load_network(network, 'CPU')
```


# Exercise:  Feed an IR to the Inference Engine¶
In this exercise, we basically have three models in IR form that we must ensure
will work in the cloud CPU environment.  To do so, we must be able to import the appropriate
IE classes, register a CPU plugin if necessary, and ensure all network layers are
supported on the CPU for each model.

We are given the following starter code:

```python
import argparse
### TODO: Load the necessary libraries

CPU_EXTENSION = "/opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so"

def get_args():
    '''
    Gets the arguments from the command line.
    '''
    parser = argparse.ArgumentParser("Load an IR into the Inference Engine")
    # -- Create the descriptions for the commands
    m_desc = "The location of the model XML file"

    # -- Create the arguments
    parser.add_argument("-m", help=m_desc)
    args = parser.parse_args()

    return args


def load_to_IE(model_xml):
    ### TODO: Load the Inference Engine API

    ### TODO: Load IR files into their related class

    ### TODO: Add a CPU extension, if applicable. It's suggested to check
    ###       your code for unsupported layers for practice before 
    ###       implementing this. Not all of the models may need it.

    ### TODO: Get the supported layers of the network

    ### TODO: Check for any unsupported layers, and let the user
    ###       know if anything is missing. Exit the program, if so.

    ### TODO: Load the network into the Inference Engine

    print("IR successfully loaded into Inference Engine.")

    return


def main():
    args = get_args()
    load_to_IE(args.m)


if __name__ == "__main__":
    main()
```

Given I already played around with this stuff before I started this exericse, the 
solution came quick and painless:

```python
import argparse
### TODO: Load the necessary libraries
import sys; sys.path.append('/opt/intel/openvino/python/python3')
from openvino.inference_engine import IECore, IENetwork

CPU_EXTENSION = "/opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so"

def get_args():
    '''
    Gets the arguments from the command line.
    '''
    parser = argparse.ArgumentParser("Load an IR into the Inference Engine")
    # -- Create the descriptions for the commands
    m_desc = "The location of the model XML file"
    add_extension_dec = "Flag to add CPU extension."

    # -- Create the arguments
    parser.add_argument("-m", help=m_desc)
    parser.add_argument("--add-extension", action='store_true')
    args = parser.parse_args()

    return args


def load_to_IE(model_xml, add_extension = False):
    ### TODO: Load the Inference Engine API
    core = IECore()
    
    ### TODO: Load IR files into their related class
    network = IENetwork(
        model = model_xml,
        weights = model_xml.split('.')[0]+'.bin',
    )
    
    ### TODO: Add a CPU extension, if applicable. It's suggested to check
    ###       your code for unsupported layers for practice before 
    ###       implementing this. Not all of the models may need it.
    if add_extension:
        core.add_extension(CPU_EXTENSION, 'CPU')

    ### TODO: Get the supported layers of the network
    supported_layers = core.query_network(network, 'CPU').keys()

    ### TODO: Check for any unsupported layers, and let the user
    ###       know if anything is missing. Exit the program, if so.
    unsupported_layers = [
        l for l in network.layers.keys() if l not in supported_layers
    ]
    if unsupported_layers:
        return unsupported_layers
    
    ### TODO: Load the network into the Inference Engine
    core.load_network(network, 'CPU')
    
    print("IR successfully loaded into Inference Engine.")

    return


def main():
    args = get_args()
    load_to_IE(args.m, args.add_extension)


if __name__ == "__main__":
    main()
```

# Inference Requests
In the previous code, we ensured the model was fully supported by the Inference Engine.  The 
last thing we did was load the network into the IE core (`core.load_network(network, 'CPU')`): this
has the effect of creating an [`ExecutableNetwork`](https://docs.openvinotoolkit.org/latest/classie__api_1_1ExecutableNetwork.html)
, which is the OpenVINO runtime representation
of your model.   This is what you will employ for inference requests.

An `ExecutableNetwork` can handle both synchronous (`ExecutableNetwork.infer`) and asynchronous 
(`ExecutableNetwork.async_infer`) inference requests, where these terms take on their usual meanings:
* synchronous requests block the main thread until a response is returned
* asynchrounous requests do not block the main thread, and generally allow your app to do other
  things while a response is being delivered
  - Check out: [Object Detection SSD C++ Demo, Async API Performance Showcase](https://github.com/opencv/open_model_zoo/blob/master/demos/object_detection_demo_ssd_async/README.md)

Inference requests become 
[`InferenceRequest`](https://docs.openvinotoolkit.org/latest/classie__api_1_1InferRequest.html)
objects, which handle and hold both the input request and its output.  For a further look into
sync and async function calls, and how to integrate the Inference Engine into your app, check
out:  [Integrate the Inference Engine with Your Application](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Integrate_with_customer_application_new_API.html)


# Exercise:

Basically, here we just had to fill out a function that would handle sync requests and
another that would handle async requests.  It was as easy as copying and pasting from the 
[`ExecutableNetwork Class Reference`](https://docs.openvinotoolkit.org/latest/classie__api_1_1ExecutableNetwork.html).

In the code below, you'll see us loading `load_to_IE` and `preprocessing` from a module
called `helpers`.  These are essentially the functions we've built in earlier sections of
this course.  From a bird's eye perspective, we are learning to piece together an entire app,
slowly but surely.


```python
import argparse
import cv2
from helpers import load_to_IE, preprocessing

CPU_EXTENSION = "/opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so"

def get_args():
    '''
    Gets the arguments from the command line.
    '''
    parser = argparse.ArgumentParser("Load an IR into the Inference Engine")
    # -- Create the descriptions for the commands
    m_desc = "The location of the model XML file"
    i_desc = "The location of the image input"
    r_desc = "The type of inference request: Async ('A') or Sync ('S')"

    # -- Create the arguments
    parser.add_argument("-m", help=m_desc)
    parser.add_argument("-i", help=i_desc)
    parser.add_argument("-r", help=i_desc)
    args = parser.parse_args()

    return args


def async_inference(exec_net, input_blob, image):
    ### TODO: Add code to perform asynchronous inference
    ### Note: Return the exec_net
    infer_request_handle = exec_net.start_async(
        request_id=0, 
        inputs={input_blob: image}
    )
    infer_request_handle.wait()

    return exec_net


def sync_inference(exec_net, input_blob, image):
    ### TODO: Add code to perform synchronous inference
    ### Note: Return the result of inference
    res = exec_net.infer({input_blob: image})
    return res


def perform_inference(exec_net, request_type, input_image, input_shape):
    '''
    Performs inference on an input image, given an ExecutableNetwork
    '''
    # Get input image
    image = cv2.imread(input_image)
    # Extract the input shape
    n, c, h, w = input_shape
    # Preprocess it (applies for the IRs from the Pre-Trained Models lesson)
    preprocessed_image = preprocessing(image, h, w)

    # Get the input blob for the inference request
    input_blob = next(iter(exec_net.inputs))

    # Perform either synchronous or asynchronous inference
    request_type = request_type.lower()
    if request_type == 'a':
        output = async_inference(exec_net, input_blob, preprocessed_image)
    elif request_type == 's':
        output = sync_inference(exec_net, input_blob, preprocessed_image)
    else:
        print("Unknown inference request type, should be 'A' or 'S'.")
        exit(1)

    # Return the exec_net for testing purposes
    return output


def main():
    args = get_args()
    exec_net, input_shape = load_to_IE(args.m, CPU_EXTENSION)
    perform_inference(exec_net, args.r, args.i, input_shape)


if __name__ == "__main__":
    main()
```

The instructor wrapped the `.wait` section of the async code in a `while True` loop, where
he called on `.wait(-1)` then checked the return status, where he would then cause the
execution loop to pause for a second and try again if the response wasn't ready.  This is
over-engineering!  At least as far as my understanding of the docs go:
* the default of `.wait()` is `.wait(-1)`, so `.wait(-1)` wasn't explicitly necessary
* the meaning of `.wait(-1)` is to wait indefinitely until a response is returned, so the
  `while True` loop is unnecessary

# Exercise:  Putting Together an App


Found this bounding box model: https://docs.openvinotoolkit.org/2019_R1/_vehicle_detection_adas_0002_description_vehicle_detection_adas_0002.html

```
DL="/opt/intel/openvino/deployment_tools/open_model_zoo/tools/downloader"
$DL/downloader.py --name vehicle-detection-adas-0002 --precisions FP16 -o /home/workspace
```

```
bbox="intel/vehicle-detection-adas-0002/FP16/vehicle-detection-adas-0002.xml"
```

The output for this network is like:
* [1, 1, N, 7], where N is the number of detected bounding boxes
* for each detection, the 7 features are: [image_id, label, conf, x_min, y_min, x_max, y_max]
  - image_id - ID of the image in the batch
  - label - predicted class ID
  - conf - confidence for the predicted class
  - (x_min, y_min) - coordinates of the top left bounding box corner
  - (x_max, y_max) - coordinates of the bottom right bounding box corner
 

Here is my final code.  I added a few additional command line arguments than
required.

First, the `inference.py` helper module we create:
```python
'''
Contains code for working with the Inference Engine.
You'll learn how to implement this code and more in
the related lesson on the topic.
'''

import os
import sys
import logging as log
from openvino.inference_engine import IENetwork, IECore

class Network:
    '''
    Load and store information for working with the Inference Engine,
    and any loaded models.
    '''

    def __init__(self):
        self.plugin = None
        self.network = None
        self.input_blob = None
        self.output_blob = None
        self.exec_network = None
        self.infer_request = None


    def load_model(self, model, device="CPU", cpu_extension=None):
        '''
        Load the model given IR files.
        Defaults to CPU as device for use in the workspace.
        Synchronous requests made within.
        '''
        model_xml = model
        model_bin = os.path.splitext(model_xml)[0] + ".bin"

        # Initialize the plugin
        self.plugin = IECore()

        # Add a CPU extension, if applicable
        if cpu_extension and "CPU" in device:
            self.plugin.add_extension(cpu_extension, device)

        # Read the IR as a IENetwork
        self.network = IENetwork(model=model_xml, weights=model_bin)

        # Load the IENetwork into the plugin
        self.exec_network = self.plugin.load_network(self.network, device)

        # Get the input layer
        self.input_blob = next(iter(self.network.inputs))
        self.output_blob = next(iter(self.network.outputs))

        return


    def get_input_shape(self):
        '''
        Gets the input shape of the network
        '''
        return self.network.inputs[self.input_blob].shape


    def async_inference(self, image):
        '''
        Makes an asynchronous inference request, given an input image.
        '''
        ### TODO: Start asynchronous inference
        self.infer_request = self.exec_network.start_async(
            request_id = 0,
            inputs = {self.input_blob: image},
        )
        return


    def wait(self):
        '''
        Checks the status of the inference request.
        '''
        ### TODO: Wait for the async request to be complete
        return self.infer_request.wait()


    def extract_output(self):
        '''
        Returns a list of the results for the output layer of the network.
        '''
        ### TODO: Return the outputs of the network from the output_blob
        return self.exec_network.requests[0].outputs[self.output_blob]
```

Then, the main `app.py`:
```python
import argparse
import cv2
import os
import numpy as np
from inference import Network

INPUT_STREAM = "test_video.mp4"
CPU_EXTENSION = "/opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so"

def get_args():
    '''
    Gets the arguments from the command line.
    '''
    parser = argparse.ArgumentParser("Run inference on an input video")
    # -- Create the descriptions for the commands
    m_desc = "The location of the model XML file"
    i_desc = "The location of the input file"
    d_desc = "The device name, if not 'CPU'"
    ### TODO: Add additional arguments and descriptions for:
    ###       1) Different confidence thresholds used to draw bounding boxes
    ###       2) The user choosing the color of the bounding boxes
    conf_desc = "Confidence threshold"
    color_desc = "Color of bounding box (B,G,R)"
    ### Extracurricular:  Add an argument to turn cpu_extension
    # -- not sure why they don't have this since it's a parameter
    #    for one of the functions from the Network class
    cpu_ext_desc = "Use CPU extension (only if device is CPU)"
    o_desc = "Path/Name of output file (e.g., video.mp4)"


    # -- Add required and optional groups
    parser._action_groups.pop()
    required = parser.add_argument_group('required arguments')
    optional = parser.add_argument_group('optional arguments')

    # -- Create the arguments
    required.add_argument("-m", help=m_desc, required=True)
    optional.add_argument("-i", help=i_desc, default=INPUT_STREAM)
    optional.add_argument("-d", help=d_desc, default='CPU')
    ### TODO: Add the additional arguments from above
    optional.add_argument("--conf", help=conf_desc, type=float, default=0.7)
    optional.add_argument("--color", help=color_desc, type=int, nargs=3, default=(0,0,255))
    ### Extracurricular from above:  add cpu ext
    optional.add_argument("--cpu_ext", help=cpu_ext_desc, default=CPU_EXTENSION)
    optional.add_argument("-o", help=o_desc, default='out.mp4')
    args = parser.parse_args()

    return args


def infer_on_video(args):
    ### TODO: Initialize the Inference Engine
    network = Network()

    ### TODO: Load the network model into the IE
    network.load_model(
        model = args.m,
        device = args.d,
        cpu_extension = args.cpu_ext,
    )

    # Get and open video capture
    cap = cv2.VideoCapture(args.i)
    cap.open(args.i)

    # Grab the shape of the input 
    width = int(cap.get(3))
    height = int(cap.get(4))

    # Create a video writer for the output video
    # The second argument should be `cv2.VideoWriter_fourcc('M','J','P','G')`
    # on Mac, and `0x00000021` on Linux
    #-------------------------
    # Extracurricular Add-in
    #--------------------------
    # Seemed like this belonged here given the above notes
    # about Mac vs Linux
    opsys = os.uname().sysname.lower()
    if opsys == 'linux':
        vw_arg2 = 0x00000021
    elif opsys == 'darwin':
        vw_arg2 = cv2.VideoWriter_fourcc('M','J','P','G')
    else:
        raise OSError("Not sure what OS this is.")
    #----------------------------
    out = cv2.VideoWriter(args.o, vw_arg2, 30, (width,height))
    
    # Process frames until the video ends, or process is exited
    while cap.isOpened():
        # Read the next frame
        flag, frame = cap.read()
        if not flag:
            break
        key_pressed = cv2.waitKey(60)

        ### TODO: Pre-process the frame
        b, c, h, w = network.get_input_shape()
        input_image = cv2.resize(
            np.copy(frame),
            (w,h)
        ).transpose((2,0,1)).\
        reshape(1,3,h,w) 

        ### TODO: Perform inference on the frame
        network.async_inference(input_image)
        network.wait()

        ### TODO: Get the output of inference
        # Output is a 1x1xNx7 Numpy array, where N is
        #   the number of potential bounding boxes and
        #   7 is the number of features associated with each
        #   (img_id, label, confidence, xmin, ymin, xmax, ymax)
        # For first frame, via print statement we find that
        #   output is 1x1x200x7, which means there are 200
        #   potential bounding boxes to draw on frame (depending
        #   on what confidence we choose)
        # Output[0][0] is the 200x7 frame we need
        output = network.extract_output()  
        
        
        ### TODO: Update the frame to include detected bounding boxes
        for box in output[0][0]:
            confidence = box[2]
            xmin, ymin = int(box[3] * width), int(box[4] * height)
            xmax, ymax = int(box[5] * width), int(box[6] * height)
            if confidence >= args.conf:
                cv2.rectangle(
                    frame,
                    (xmin, ymin),
                    (xmax, ymax),
                    args.color,
                    1
                )

        # Write out the frame
        out.write(frame)
        # Break if escape key pressed
        if key_pressed == 27:
            break

    # Release the out writer, capture, and destroy any OpenCV windows
    out.release()
    cap.release()
    cv2.destroyAllWindows()


def main():
    args = get_args()
    infer_on_video(args)


if __name__ == "__main__":
    main()
```

Finally, here's how to run it.

Defaut:
```
python app.py -m intel/vehicle-detection-adas-0002/FP16/vehicle-detection-adas-0002.xml
```

Since I only use the one model, I probably should have made that `-m` flag default to it. Anyway...

Some customization:
```
python app.py -m intel/vehicle-detection-adas-0002/FP16/vehicle-detection-adas-0002.xml -o out_conf60_col0-255-0.mp4 --conf "0.6" --color 0 255 0
```





------------------------

# References & Further Reading
* [Inference Engine Developer Guide](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Deep_Learning_Inference_Engine_DevGuide.html)
* [Supported Devices for Intel's OpenVINO](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_supported_plugins_Supported_Devices.html)
* [How To: Use the Model Downloader and Model Optimizer for the Intel Distribution of OpenVINO Toolkit on Raspberry Pi](https://software.intel.com/en-us/articles/model-downloader-optimizer-for-openvino-on-raspberry-pi)
* [IECore](https://docs.openvinotoolkit.org/latest/classie__api_1_1IECore.html)
* [IENetwork](https://docs.openvinotoolkit.org/latest/classie__api_1_1IENetwork.html)
* [Full Python API](https://docs.openvinotoolkit.org/latest/ie_python_api.html))
* [ie_api.ExecutableNetwork Class Reference](https://docs.openvinotoolkit.org/latest/classie__api_1_1ExecutableNetwork.html)
* [ie_api.InferRequest Class Reference](https://docs.openvinotoolkit.org/latest/classie__api_1_1InferRequest.html)
* [Integrate the Inference Engine with Your Application](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Integrate_with_customer_application_new_API.html)
* [Object Detection SSD C++ Demo, Async API Performance Showcase](https://github.com/opencv/open_model_zoo/blob/master/demos/object_detection_demo_ssd_async/README.md)
