---
date: 2017-05-11T23:59:59+00:00
draft: false
author: "caicai"
title: "tensorflow--激活函数"
categories: ["深度学习"]
tags: ["DL, sigmoid"] 
---

来源：[tensorflow学习笔记（四）：激活函数](http://blog.csdn.net/u012436149/article/details/52874912)
```
tf.nn.relu()
tf.nn.sigmoid()
tf.nn.tanh()
tf.nn.elu()
tf.nn.bias_add()
tf.nn.crelu()
tf.nn.relu6()
tf.nn.softplus()
tf.nn.softsign()
tf.nn.dropout()
tf.nn.relu_layer(x, weights, biases,name=None)
def relu_layer(x, weights, biases, name=None):
  """Computes Relu(x * weight + biases).
  Args:
    x: a 2D tensor.  Dimensions typically: batch, in_units
    weights: a 2D tensor.  Dimensions typically: in_units, out_units
    biases: a 1D tensor.  Dimensions: out_units
    name: A name for the operation (optional).  If not specified
      "nn_relu_layer" is used.
  Returns:
    A 2-D Tensor computing relu(matmul(x, weights) + biases).
    Dimensions typically: batch, out_units.
  """
```
