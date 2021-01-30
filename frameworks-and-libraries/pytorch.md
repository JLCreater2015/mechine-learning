# Pytorch

## ✏ PyTorch

### 🖋 发展

Torch深度学习框架最初的开发可以追溯到2002年。采用C语言作为底层，`lua`语言为前端接口，2016年，Facebook在`Torch7`的基础上开发了PyTorch，它使用`THNN`作为后端。并于2016年10月发表了PyTorch最初的版本0.1.1（alpha-1版本）。在最初的版本中，PyTorch和Torch共享的是底层的C语言API库，现在还可以在PyTorch源代码里看到`TH、THC、THNN、THCUNN`这些目录，即来源于Torch的代码。

在最初的版本中，PyTorch支持的深度学习计算操作很少，而且在并行计算和异构计算（CPU/GPU混合）方面的支持不是很完善。在后续版本中，PyTorch逐渐引入了多进程计算的功能，而且逐渐集成了`CuDNN`的GPU深度学习计算库，引入了越来越多的张量运算操作和深度学习模块。在PyTorch 0.2.0中已经实现了高阶导数、分布式计算、张量广播等功能。同时，在重要的并行计算的支持方面，也得到了很大的完善。到了PyTorch 0.3.0，PyTorch支持更多的损失函数和优化器，同时在框架的计算性能的表现上有了长足的进步。另外，PyTorch开始支持导出神经网络为开放神经网络交换格式（Open Neural Network Exchange Format，`ONNX`），这个格式用于存储神经网络的连接方式和权重，也用于和其他深度学习框架（如`Caffe2`和`MXNet`等）交换构建神经网络的权重。

PyTorch 0.4.0是在PyTorch 1.0之前的最后一个大版本，在PyTorch 0.4.0中，相对前一个版本而言，对分布式计算的支持更加完善，方便了用户的使用。同时，`ONNX`也增加了对循环神经网络（Recurrent Neural Network，`RNN`）的支持。另外，在这个版本中，也增加了对Windows操作系统的支持，实现了张量（Tensor）和变量（Variable）的合并，而在之前的版本中，这两个概念是相互独立的，变量是可以构建计算图且能够进行自动求导的张量。在PyTorch 0.4.0中，通过指定张量支持导数的选项，就不再需要用到变量。因此，这两个概念合并为张量。

2018年12月，发布PyTorch 1.0正式版，它使用`Caffe2`作为后端，以弥补PyTorch在工业部署上的不足。PyTorch 1.0又有了几个重大的变化。首先是分布式训练方式的更改，PyTorch在分布式计算方面开始对不同的后端有了完善的支持，包括`MPI`和`NCCL`等。在即时编译器（Just-In-Time Compiler，`JIT`）方面，PyTorch 1.0新增了许多功能，使得之前的深度学习模型追踪（trace）的功能有了很大提高。通过使用改进的`JIT`，可以把PyTorch的动态计算图编译成静态计算图，方便模型的部署。为了减少Python前端在运行深度学习模型的时间损耗，PyTorch也加强了对C++前端的支持，这样一个Python训练和保存的模型就能通过C++前端运行，有效地提高了模型的运行效率。

PyTorch Hub的开发也是1.0版本的亮点之一，通过PyTorch Hub，用户可以获得一系列预训练的深度学习模型，主要包括计算机视觉、自然语言处理、生成式模型和音频模型等，这些预训练模型的出现能有效地加快用户开发新型的深度学习模型，方便用户构建基线模型和复现深度学习模型的效果。到了PyTorch 1.1，PyTorch开始支持`TensorBoard`对于张量的可视化，并且加强了`JIT`的功能。PyTorch 1.2增强了`TorchScript`的功能，同时增加了`Transformer`模块，也增加了对视频、文本和音频的训练数据载入的支持。到了PyTorch 1.3，增加了移动端的处理，而且增加了对模型的量化功能（Quantization）的支持。

### 🖋 PyTorch生态

对于计算机视觉，PyTorch有`TorchVision`这样应用广泛的库；对于自然语言处理，PyTorch有`PyTorch NLP`，`AllenNLP`这样的库；对于图卷积这类新型图网络，有PyTorch Geometric这样的库；对于上层API，基于PyTorch的`Fastai`框架相当简洁；对于工业部署，`ONN`协议保证模型的部署。

### 🖋 `torch & torchvision`对应版本

![](../.gitbook/assets/image%20%284%29.png)

目前，主要的深度学习框架如下图，主要分为Google、Facebook、微软及亚马孙三大阵营。尽管有着如此多的深度学习框架，但是到目前这个阶段来看，主流的深度学习框架就只有两个TensorFlow和PyTorch。在`TensorFlow2`发布之前，**TensorFlow和PyTorch的主要区别是使用动态图还是静态图进行运算**。简单理解，静态图的运行是一个不可分割的整体，很不适合研究者的调试，而动态图则是类似Python语言的运行机制，可以方便的暂停调试。

![](../.gitbook/assets/image%20%285%29.png)

## ✏ PyTorch与CUDA版本

### 🖋 显卡驱动与CUDA版本对应关系

![](../.gitbook/assets/image%20%2863%29.png)

### 🖋 Pytorch与CUDA版本对应关系

参考：[https://pytorch.org/get-started/previous-versions/](https://pytorch.org/get-started/previous-versions/)

| CUDA | PyTorch |
| :---: | :---: |
| 7.5 | 0.1.6~0.4.1 |
| 8.0 | 0.1.10~0.4.1/1.0.0 |
| 9.0 | 0.3.0~0.4.1/1.0.0/1.0.1/1.1.0 |
| 9.2 | 0.4.1/1.2.0/1.4.0/1.5.0/1.5.1/1.6.0 |
| 10.0 | 1.0.0/1.0.1/1.1.0/1.2.0 |
| 10.1 | 1.3.0/1.4.0/1.5.0/1.5.1/1.6.0/1.7.0 |
| 10.2 | 1.5.0/1.5.1/1.6.0/1.7.0 |
| 11.0 | 1.7.0 |

