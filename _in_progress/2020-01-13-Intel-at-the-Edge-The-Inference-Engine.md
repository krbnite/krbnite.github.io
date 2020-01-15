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

# 


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
