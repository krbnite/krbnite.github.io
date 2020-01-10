---
title: Intel at the Edge (The Model Optimizer)
layout: post
tags: edge ai easi
---


This lesson starts off describing what the Model Optimizer is, which feels
redundant at this point, but here goes: the model optimizer is used to (i)
convert deep learning models from various frameworks (TensorFlow, Caffe, MXNet, Kaldi,
and ONNX, which can support PyTorch and Apple ML models) into a standarard 
vernacular called the Intermediate Representation (IR), and
(ii) optimize various aspects of the model, such as size and computational
efficiency by using lower precision, discarding layers only needed during
training (e.g., a DropOut layer), and merging layers that can be computed
as a single layer (e.g., a multiplication, convolution, and addition can
all be merged).  The OpenVINO suite actually performs hardware optimizations
as well, but this aspect is owed to the Inference Engine.

Before using the Model Optimizer, head over to 
`/opt/intel/openvino/deployment_tools/model_optimizer/install_prerequisites` and run
`./install_prerequisites.sh`.

# Model Optimization Techniques

**Quantization**: You might train your model with FP32 (32-bit floating precision), but
it's possible to lower the precision to FP16 for inference without significant loss
in accuracy, while reducing the storage size and increasing the computational 
efficiency.  The pre-trained models also come available with INT8 precision (
8-bit integer), but at the time of writing, the Model Optimizer does not yet support
converting your own models to INT8.

**Freezing**: This is not something the Model Optimizer does, but a recommendation
for TensorFlow models: freeze them!  Freezing a TF model discards various artifacts
only necessary during training time.

**Fusion**:  Fusion is when you merge (or fuse) multiple layer into one.  Above, I gave
an example of fusing a convolution layer with a multiplication and addition layer.  Another example 
is fusing a batch normalization layer, activation layer, and convolutional layer.

**More Reading**
* Nervana Systems: [Quantization](https://nervanasystems.github.io/distiller/quantization.html)
* [OpenVINO Model Optimization Techniques](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Model_Optimization_Techniques.html)

# The Intermediate Representation
Each of the different deep learning frameworks has different design choices and naming
conventions, e.g., a convolution layer in TensorFlow is called `Conv2D`, while it's referred
to as a `Conv` layer in ONNX and a `Convolution` layer in Caffe.  The IR standardizes
all of this -- a convolution layers is converted into a [Convolution Layer](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_IRLayersCatalogSpec.html#Convolution)
in the IR, independent
of framework.  The standardized architectural layout (and other metadata) get stored in
a `.xml` file, while the weight and biases get stored in binary form in a `.bin` file.

Other examples of standardization:
* FullyConnected
  - Caffe: InnerProduct
  - TensorFlow: MatMul
  - Kaldi: AffineComponent or AffineTransform
  - MXNet: FullyConnected
* ScaleShift
  - Caffe: Scale or BN
  - MXNet: ScaleShift or broadcast_mul
  - TensorFlow: FusedBatchNorm, Add, BiasAdd, 
  - Kaldi: AddShift, NormalizeComponent, or Rescale

A full mapping of framework layers to [IR standardized layers](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_IRLayersCatalogSpec.html#Convolution) can be found
[here](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Supported_Frameworks_Layers.html).

# Converting a Model to the IR
The gist is simple:
```
mo="/opt/intel/openvino/deployment_tools/model_optimizer"
python $mo/mo.py --input_model PATH_TO_INPUT_MODEL
```

If a model uses a framework's standard extension, then the Model Optimizer will
automatically know what to do (if not, use the `--framework` flag to specificy):
* Caffe: .caffemodel
* TensorFlow: .pb
* MXNet: .params
* ONNX: .onnx
* Kaldi: .nnet

There are a horde of framework-agnostic command line flags that you should be aware of:
* [Converting a Model Using General Conversion Parameters](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model_General.html)

You can see the list of all flags at the command line:
```
mo="/opt/intel/openvino/deployment_tools/model_optimizer"
python $mo/mo.py -h
```

For example, sometimes you might have to specify bach size, `-b`, or you might be required
to use the `--mean_values` and `--scale` flags.  Or maybe something more specialized, like
`--disable_resnet_optimization`.

I've copied the framework-agnostic flags at the end of this post for your (my) convenience. 


# Using the Model Optimizer with TensorFlow Models
Now that you know the gist, know this: there are quite a few [model-agnostic](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model_General.html)
and model-specific command line flags you will likely need to use with the Model Optimizer, e.g., 
[TensorFlow has an assortment of idiosyncracies that you must be aware of](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Convert_Model_From_TensorFlow.html).

For a TF pre-trained model, one of the first things you must do is determine whether you have
a frozen or unfrozen model.  It is recommended you freeze the model in TensorFlow before using the
Model Optimizer, however the Model Optimizer provides a way of dealing with unfrozen models as well.  If
a pre-trained model you have in hand is already frozen, then great!

There are various flags to be aware of, e.g.:
* for an unfrozen TF model, you will likely have to use the `--mean_values` and `--scale` flags
* for a frozen models from the [TF detection model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md), you will likely need the 
  `--tensorflow_use_custom_operations_config` and `--tensorflow_object_detection_api_pipeline_config`
  flags 
* for most (if not all) TF models, you'll likely have to flag `--reverse_input_channels`

I've copied the list of TF-specific command line flags at the end of this post for you (my)
convenience!
  
Flags aren't the only things to worry about for TF models: often you have to cut off layers, or
specify input/output placeholders.  See the docs for examples specific to various popular pre-trained
models.  Just a few examples:
* [Converting YOLO* Models to the Intermediate Representation (IR)](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_tf_specific_Convert_YOLO_From_Tensorflow.html)
* [Convert TensorFlow* DeepSpeech Model to the Intermediate Representation](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_tf_specific_Convert_DeepSpeech_From_Tensorflow.html)
* [Converting TensorFlow* Object Detection API Models](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_tf_specific_Convert_Object_Detection_API_Models.html)
* [Convert TensorFlow* BERT Model to the Intermediate Representation](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_tf_specific_Convert_BERT_From_Tensorflow.html)

## Freeze a TF Model
Shameless copy-and-paste from the Intel OpenVINO website:
```python
import tensorflow as tf
from tensorflow.python.framework import graph_io
frozen = tf.graph_util.convert_variables_to_constants(sess, sess.graph_def, ["name_of_the_output_node"])
graph_io.write_graph(frozen, './', 'inference_graph.pb', as_text=False)
```

# Exercise: Convert a Frozen TensorFlow Model to the OpenVINO Intermediate Representation
In this exercise, we download a frozen, pre-trained TensorFlow model (namely, 
[SSD MobileNet V2 COCO](http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz)),
un-tar it, and point the Model Optimizer at its protobuf file (`.pb`) to convert it to IR.

First, to download the model:
```
wget http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz
```

Then, to unpack it:
```
tar -xvf ssd_mobilenet_v2_coco_2018_03_29.tar.gz
```

Finall, point the Model Optimizer at it.  Since this is a TensorFlow model, 
we already know it's likely necessary to set the
`--reverse_input_channels` flag.  Since it's a model from the TensorFlow Object Detection 
Model Zoo, we know from the course and OpenVINO docs that we will also might need the 
`--tensorflow_use_custom_operations_config` and `--tensorflow_object_detection_api_pipeline_config`
flags.

Looking into the model directory, it's clear that a pipeline config file exists:
```
ls ssd_mobilenet_v2_coco_2018_03_29
  checkpoint                      model.ckpt.index  saved_model
  frozen_inference_graph.pb       model.ckpt.meta
  model.ckpt.data-00000-of-00001  pipeline.config
```

But what about a custom operations config file exists?  According to
[OpenVINO's TF Conversion page](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_tf_specific_Convert_Object_Detection_API_Models.html), these cofig files are actually housed in
the OpenVINO model optimizer directory:  the `--tensorflow_use_custom_operations_config` flag takes
the argument `<path_to_subgraph_replacement_configuration_file.json>`.  This is a
> subgraph replacement configuration file that 
> describes rules to convert specific TensorFlow* topologies. For the models downloaded from the TensorFlow 
> Object Detection API zoo, you can find the configuration files in the 
> <INSTALL_DIR>/deployment_tools/model_optimizer/extensions/front/tf directory. 

Let's check it out:

```
ls /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf | grep ssd
  ssd_support.json
  ssd_support_api_v1.14.json
  ssd_toolbox_detection_output.json
  ssd_toolbox_multihead_detection_output.json
  ssd_v2_support.json
```

Since we are working with `ssd_mobilenet_v2_coco_2018_03_29`, a good guess is that we
migth need `ssd_v2_support.json`.  The is also what the docs page recommends:  
use `ssd_v2_support.json` for frozen SSD topologies from the models zoo.

Great!  So we probably have figured out everything we need to do.   Let's try it.

```
mo="/opt/intel/openvino/deployment_tools/model_optimizer"
ssd="/home/workspace/ssd_mobilenet_v2_coco_2018_03_29"
python $mo/mo.py \
  --input_model $ssd/frozen_inference_graph.pb \
  --reverse_input_channels \
  --tensorflow_object_detection_api_pipeline_config $ssd/pipeline.config \
  --tensorflow_use_custom_operations_config $mo/extensions/front/tf/ssd_v2_support.json
```

The outputs some really useful info:
```
Model Optimizer arguments:
Common parameters:
        - Path to the Input Model:      /home/workspace/ssd_mobilenet_v2_coco_2018_03_29/frozen_inference_graph.pb
        - Path for generated IR:        /home/workspace/.
        - IR output name:       frozen_inference_graph
        - Log level:    ERROR
        - Batch:        Not specified, inherited from the model
        - Input layers:         Not specified, inherited from the model
        - Output layers:        Not specified, inherited from the model
        - Input shapes:         Not specified, inherited from the model
        - Mean values:  Not specified
        - Scale values:         Not specified
        - Scale factor:         Not specified
        - Precision of IR:      FP32
        - Enable fusing:        True
        - Enable grouped convolutions fusing:   True
        - Move mean values to preprocess section:       False
        - Reverse input channels:       True
TensorFlow specific parameters:
        - Input model in text protobuf format:  False
        - Path to model dump for TensorBoard:   None
        - List of shared libraries with TensorFlow custom layers implementation:     None
        - Update the configuration file with input/output node names:   None
        - Use configuration file used to generate the model with Object Detection API:        /home/workspace/ssd_mobilenet_v2_coco_2018_03_29/pipeline.config
        - Operations to offload:        None
        - Patterns to offload:  None
        - Use the config file:  /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json
Model Optimizer version:        2019.3.0-408-gac8584cb7

# ...followed by lots of Numpy-specific verbose output logs
# ...and finally:

[ SUCCESS ] Generated IR model.
[ SUCCESS ] XML file: /home/workspace/./frozen_inference_graph.xml
[ SUCCESS ] BIN file: /home/workspace/./frozen_inference_graph.bin
[ SUCCESS ] Total execution time: 68.36 seconds. 
```

Btw, out of curiosity, I tried running this command without the config commands set:  the model
optimizer crashes.

-----------------------------------------

# Framework-Agnostic Command Line Flags for the Model Optimizer

```
optional arguments:
  -h, --help            show this help message and exit
  --framework {tf,caffe,mxnet,kaldi,onnx}
                        Name of the framework used to train the input model.
Framework-agnostic parameters:
  --input_model INPUT_MODEL, -w INPUT_MODEL, -m INPUT_MODEL
                        Tensorflow*: a file with a pre-trained model (binary
                        or text .pb file after freezing). Caffe*: a model
                        proto file with model weights
  --model_name MODEL_NAME, -n MODEL_NAME
                        Model_name parameter passed to the final create_ir
                        transform. This parameter is used to name a network in
                        a generated IR and output .xml/.bin files.
  --output_dir OUTPUT_DIR, -o OUTPUT_DIR
                        Directory that stores the generated IR. By default, it
                        is the directory from where the Model Optimizer is
                        launched.
  --input_shape INPUT_SHAPE
                        Input shape(s) that should be fed to an input node(s)
                        of the model. Shape is defined as a comma-separated
                        list of integer numbers enclosed in parentheses or
                        square brackets, for example [1,3,227,227] or
                        (1,227,227,3), where the order of dimensions depends
                        on the framework input layout of the model. For
                        example, [N,C,H,W] is used for Caffe* models and
                        [N,H,W,C] for TensorFlow* models. Model Optimizer
                        performs necessary transformations to convert the
                        shape to the layout required by Inference Engine
                        (N,C,H,W). The shape should not contain undefined
                        dimensions (? or -1) and should fit the dimensions
                        defined in the input operation of the graph. If there
                        are multiple inputs in the model, --input_shape should
                        contain definition of shape for each input separated
                        by a comma, for example: [1,3,227,227],[2,4] for a
                        model with two inputs with 4D and 2D shapes.
                        Alternatively, you can specify shapes with the
                        --input option.
  --scale SCALE, -s SCALE
                        All input values coming from original network inputs
                        will be divided by this value. When a list of inputs
                        is overridden by the --input parameter, this scale is
                        not applied for any input that does not match with the
                        original input of the model.
  --reverse_input_channels
                        Switch the input channels order from RGB to BGR (or
                        vice versa). Applied to original inputs of the model
                        if and only if a number of channels equals 3. Applied
                        after application of --mean_values and --scale_values
                        options, so numbers in --mean_values and
                        --scale_values go in the order of channels used in the
                        original model.
  --log_level {CRITICAL,ERROR,WARN,WARNING,INFO,DEBUG,NOTSET}
                        Logger level
  --input INPUT         Quoted list of comma-separated input nodes names with
                        shapes and values for freezing. The shape and value are specified
                        as space-separated lists.
                        For example, use the following format to set input port 0
                        of the node node_name1 with the shape [3 4] as an input node
                        and freeze output port 1 of the node node_name2 with
                        the value [20 15] and the shape [2]:
                        "0:node_name1[3 4],node_name2:1[2]->[20 15]".
  --output OUTPUT       The name of the output operation of the model. For
                        TensorFlow*, do not add :0 to this name.
  --mean_values MEAN_VALUES, -ms MEAN_VALUES
                        Mean values to be used for the input image per
                        channel. Values to be provided in the (R,G,B) or
                        [R,G,B] format. Can be defined for desired input of
                        the model, for example: "--mean_values
                        data[255,255,255],info[255,255,255]". The exact
                        meaning and order of channels depend on how the
                        original model was trained.
  --scale_values SCALE_VALUES
                        Scale values to be used for the input image per
                        channel. Values are provided in the (R,G,B) or [R,G,B]
                        format. Can be defined for desired input of the model,
                        for example: "--scale_values
                        data[255,255,255],info[255,255,255]". The exact
                        meaning and order of channels depend on how the
                        original model was trained.
  --data_type {FP16,FP32,half,float}
                        Data type for all intermediate tensors and weights. If
                        original model is in FP32 and --data_type=FP16 is
                        specified, all model weights and biases are quantized
                        to FP16.
  --disable_fusing      Turn off fusing of linear operations to Convolution
  --disable_resnet_optimization
                        Turn off resnet optimization
  --finegrain_fusing FINEGRAIN_FUSING
                        Regex for layers/operations that won't be fused.
                        Example: --finegrain_fusing Convolution1,.*Scale.*
  --disable_gfusing     Turn off fusing of grouped convolutions
  --move_to_preprocess  Move mean values to IR preprocess section
  --extensions EXTENSIONS
                        Directory or a comma separated list of directories
                        with extensions. To disable all extensions including
                        those that are placed at the default location, pass an
                        empty string.
  --batch BATCH, -b BATCH
                        Input batch size
  --version             Version of Model Optimizer
  --silent              Prevent any output messages except those that
                        correspond to log level equals ERROR, that can be set
                        with the following option: --log_level. By default,
                        log level is already ERROR.
  --freeze_placeholder_with_value FREEZE_PLACEHOLDER_WITH_VALUE
                        Replaces input layer with constant node with provided
                        value, for example: "node_name->True". It will be DEPRECATED
                        in future releases. Use --input option to specify
                        values for freezing.
  --generate_deprecated_IR_V2
                        Force to generate legacy/deprecated IR V2 to work with
                        previous versions of the Inference Engine. The
                        resulting IR may or may not be correctly loaded by
                        Inference Engine API (including the most recent and
                        old versions of Inference Engine) and provided as a
                        partially-validated backup option for specific
                        deployment scenarios. Use it at your own discretion.
                        By default, without this option, the Model Optimizer
                        generates IR V3.
  --keep_shape_ops      [ Experimental feature ] Enables `Shape` operation
                        with all children keeping. This feature makes model
                        reshapable in Inference Engine
  --steps
                        Enables model conversion steps display
```

# TensorFlow-Specific Command Line Flags for the Model Optimizer

```
TensorFlow*-specific parameters:
  --input_model_is_text
                        TensorFlow*: treat the input model file as a text
                        protobuf format. If not specified, the Model Optimizer
                        treats it as a binary file by default.
  --input_checkpoint INPUT_CHECKPOINT
                        TensorFlow*: variables file to load.
  --input_meta_graph INPUT_META_GRAPH
                        Tensorflow*: a file with a meta-graph of the model
                        before freezing
  --saved_model_dir SAVED_MODEL_DIR
                        TensorFlow*: directory representing non frozen model
  --saved_model_tags SAVED_MODEL_TAGS
                        Group of tag(s) of the MetaGraphDef to load, in string
                        format, separated by ','. For tag-set contains
                        multiple tags, all tags must be passed in.
  --offload_unsupported_operations_to_tf
                        TensorFlow*: automatically offload unsupported
                        operations to TensorFlow*
  --tensorflow_subgraph_patterns TENSORFLOW_SUBGRAPH_PATTERNS
                        TensorFlow*: a list of comma separated patterns that
                        will be applied to TensorFlow* node names to infer a
                        part of the graph using TensorFlow*.
  --tensorflow_operation_patterns TENSORFLOW_OPERATION_PATTERNS
                        TensorFlow*: a list of comma separated patterns that
                        will be applied to TensorFlow* node type (ops) to
                        infer these operations using TensorFlow*.
  --tensorflow_custom_operations_config_update TENSORFLOW_CUSTOM_OPERATIONS_CONFIG_UPDATE
                        TensorFlow*: update the configuration file with node
                        name patterns with input/output nodes information.
  --tensorflow_use_custom_operations_config TENSORFLOW_USE_CUSTOM_OPERATIONS_CONFIG
                        TensorFlow*: use the configuration file with custom
                        operation description.
  --tensorflow_object_detection_api_pipeline_config TENSORFLOW_OBJECT_DETECTION_API_PIPELINE_CONFIG
                        TensorFlow*: path to the pipeline configuration file
                        used to generate model created with help of Object
                        Detection API.
  --tensorboard_logdir TENSORBOARD_LOGDIR
                        TensorFlow*: dump the input graph to a given directory
                        that should be used with TensorBoard.
  --tensorflow_custom_layer_libraries TENSORFLOW_CUSTOM_LAYER_LIBRARIES
                        TensorFlow*: comma separated list of shared libraries
                        with TensorFlow* custom operations implementation.
  --disable_nhwc_to_nchw
                        Disables default translation from NHWC to NCHW
```

--------------------------------------------


# References & Further Reading
* [Preparing and Optimizing Your Trained Model](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Prepare_Trained_Model.html)
* [Configuring the Model Optimizer](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Config_Model_Optimizer.html)
* [Converting a Model to Intermediate Representation (IR)](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model.html)
* [Converting a Model Using General Conversion Parameters](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model_General.html)
* [Converting a TensorFlow Model](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Convert_Model_From_TensorFlow.html)
* [Offloading Sub-Graph Inference to TensorFlow](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_customize_model_optimizer_Offloading_Sub_Graph_Inference.html)
* [OpenVINO: Converting a TensorFlow* Model](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Convert_Model_From_TensorFlow.html)
* [TensorFlow Detect Model Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md)


