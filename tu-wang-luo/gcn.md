# 图卷积网络（GCN）

## 🖌 1、GCN（Graph Convalutional Network）

### ✏ **1.1、为什么要研究GCN?**

CNN处理的图像或视频数据中的像素点是排列成很整齐的矩阵，也就是很多论文中提到的Euclidean Structure，而很多时候我们需要处理的是Non Euclidean Structure的数据，比如社交网络。

![&#x56FE; 1 &#x56FE;&#x50CF;&#x77E9;&#x9635;&#x793A;&#x610F;&#x56FE;&#xFF08;Euclidean Structure&#xFF09;](../.gitbook/assets/image%20%2848%29.png)

![&#x56FE; 2 &#x793E;&#x4EA4;&#x7F51;&#x7EDC;&#x62D3;&#x6251;&#x793A;&#x610F;&#xFF08;Non Euclidean Structure&#xFF09;](../.gitbook/assets/image%20%2847%29.png)

这种网络结构在图论中被抽象成图谱图。

{% hint style="success" %}
**CNN的【平移不变性】在【非矩阵结构】数据上不适用**
{% endhint %}

* **平移不变性（translation invariance）**：比较好理解，在用基础的分类结构比如ResNet、Inception给一只猫分类时，无论猫怎么扭曲、平移，最终识别出来的都是猫，输入怎么变形输出都不变这就是平移不变性，网络的层次越深这个特性会越明显。
* **平移可变性（translation variance）**：针对目标检测的，比如一只猫从图片左侧移到了右侧，检测出的猫的坐标会发生变化就称为平移可变性。当卷积网络变深后最后一层卷积输出的feature map变小，物体在输入上的小偏移，经过N多层pooling后在最后的小feature map上会感知不到，这就是为什么[R-FCN](https://papers.nips.cc/paper/2016/file/577ef1154f3240ad5b9b413aa7346a1e-Paper.pdf)原文会说网络变深平移可变性变差。

离散卷积本质就是一种加权求和。CNN中的卷积就是一种离散卷积，本质上就是利用一个共享参数的过滤器（kernel），通过计算中心像素点以及相邻像素点的加权和来构成feature map实现空间特征的提取，当然加权系数就是卷积核的权重系数\(W\)。

那么卷积核的系数如何确定的呢？是随机化初值，然后根据误差函数通过反向传播梯度下降进行迭代优化。这是一个关键点，卷积核的参数通过优化求出才能实现特征提取的作用，**GCN的理论很大一部分工作就是为了引入可以优化的卷积参数。**

图数据中的空间特征具有以下特点：

1. 节点特征：每个节点有自己的特征；（体现在点上）
2. 结构特征：图数据中的每个节点具有结构特征，即节点与节点存在一定的联系。（体现在边上）

总地来说，图数据既要考虑节点信息，也要考虑结构信息，图卷积神经网络就可以自动化地既学习节点特征，又能学习节点与节点之间的关联信息。**GCN是要为除CV、NLP之外的任务提供一种处理、研究的模型。图卷积的核心思想是利用『边的信息』对『节点信息』进行『聚合』从而生成新的『节点表示』。**

### ✏ ****1.2、提取拓扑图中空间特征的两种方式

**GCN的本质目的就是用来提取拓扑图的空间特征。** 而图卷积神经网络主要有两类，一类是基于**空间域或顶点域vertex domain\(spatial domain\)的**，另一类则是基于**频域或谱域spectral domain的**。通俗点解释，空域可以类比到直接在图片的像素点上进行卷积，而频域可以类比到对图片进行傅里叶变换后，再进行卷积。

**１、Vertex domain\(spatial domain\)**：本质是提取拓扑图上每个顶点相邻的neighbors。 解决两个问题：

* 根据什么条件去找中心vertex的neighbors，也就是如何确定receptive field？ 
* 给定了receptive field，按照什么方式处理包含不同数目neighbours的特征？

这种方式很明显要对单个顶点做计算处理，提取的图的空间特征相当于对每个顶点的处理结果的集合。

**２、Spectral domain**：这是GCN的理论基础，本质是借助图论的相关理论来实现拓扑图上的卷积操作。从整个研究的时间进程来看：首先研究GSP（graph signal processing）的学者定义了graph上的Fourier Transformation，进而定义了graph上的convolution，最后与深度学习结合提出了Graph Convolutional Network。 理论基础： 

* 谱图理论\(Spectral Graph Theory\)：借助图的拉普拉斯矩阵的特征值和特征向量研究图的性质 
* Graph上的傅里叶变换\(Fourier Transformation\) 
* GSP\(graph signal processing\)

