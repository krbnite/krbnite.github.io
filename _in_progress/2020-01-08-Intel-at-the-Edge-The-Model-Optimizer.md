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

Now that you know the gist, know this: there are quite a few [model-agnostic](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model_General.html)
and model-specific command line flags you will likely need to use with the Model Optimizer, e.g., 
[TensorFlow has an assortment of idiosyncracies that you must be aware of](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Convert_Model_From_TensorFlow.html).

....left off on part 6....



# References & Further Reading
* [Preparing and Optimizing Your Trained Model](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Prepare_Trained_Model.html)
* [Configuring the Model Optimizer](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Config_Model_Optimizer.html)
* [Converting a Model to Intermediate Representation (IR)](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model.html)
* [Converting a Model Using General Conversion Parameters](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Converting_Model_General.html)
* [Converting a TensorFlow Model](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_convert_model_Convert_Model_From_TensorFlow.html)
* [Offloading Sub-Graph Inference to TensorFlow](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_customize_model_optimizer_Offloading_Sub_Graph_Inference.html)

