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
