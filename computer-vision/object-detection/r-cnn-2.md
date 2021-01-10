# R-CNN原理（二）

## 🖌 3、Fast R-CNN

Fast R-CNN是端到端（end-to-end）的。

![&#x56FE;5 Fast R-CNN&#x6A21;&#x578B;](../../.gitbook/assets/image%20%2810%29.png)

图中省略了通过 `ss` 获得proposal的过程，第一张图中红框里的内容即为通过`ss`提取到的proposal，中间的一块是经过深度卷积之后得到的`conv feature map`，图中灰色的部分就是我们红框中的proposal对应于`conv feature map`中的位置，之后对这个特征经过`ROI pooling layer`处理，之后进行全连接。在这里得到的`ROI feature vector`最终被分享，一个进行全连接之后用来做`softmax`回归，用来进行分类，另一个经过全连接之后用来做`bbox`回归。

> 对中间的`Conv feature map`进行特征提取。每一个区域经过`RoI pooling layer`和`FC layers`得到一个 “固定长度” 的feature vector（这里需要注意的是，输入到后面`RoI pooling layer`的feature map是在`Conv feature map`上提取的，故整个特征提取过程，只计算了一次卷积。虽然在最开始也提取出了大量的`RoI`，但他们还是作为整体输入进卷积网络的，最开始提取出的`RoI`区域只是为了最后的Bounding box 回归时使用，用来输出原图中的位置）。

### 🖋 3.1、特点

1. 规避R-CNN中冗余的特征提取操作，只对整张图像全区域进行一次特征提取（`SPP Net`）；
2. 用`RoI pooling`层取代最后一层max pooling层，同时引入建议框信息，提取相应建议框特征；
3. Fast R-CNN网络末尾采用并行的不同的全连接层，可同时输出分类结果和窗口回归结果，实现了end-to-end的多任务训练【建议框提取除外】，也不需要额外的特征存储空间【R-CNN中这部分特征是供`SVM`和Bounding-box regression进行训练的】；
4. 采用`SVD`对Fast R-CNN网络末尾并行的全连接层进行分解，减少计算复杂度，加快检测速度。

Fast R-CNN方法解决了R-CNN的三个问题：

1. R-CNN网络训练、测试速度都很慢：R-CNN网络中，一张图经由selective search算法提取约`2k`个建议框【这`2k`个建议框大量重叠】，而所有建议框变形后都要输入`AlexNet` CNN网络提取特征【即约`2k`次特征提取】，会出现上述重叠区域多次重复提取特征，提取特征操作冗余；
2. R-CNN网络训练、测试繁琐：R-CNN网络训练过程分为`ILSVRC 2012`样本下有监督预训练、`PASCAL VOC 2007`该特定样本下的微调、20类即21个`SVM`分类器训练和20个Bounding-box回归器训练，该训练流程繁琐复杂，并且需要大量存储空间；
3. R-CNN网络需要对建议框进行形变操作后【形变为227×227 size】再输入CNN网络提取特征，其实像`AlexNet`等网络在提取特征过程中对图像的大小并无要求，只是在提取完特征进行全连接操作的时候才需要固定特征尺寸【R-CNN中将输入图像形变为227×227可正好满足`AlexNet`网络最后的特征尺寸要求】，然后才使用`SVM`分类器分类，R-CNN需要进行形变操作的问题在Fast R-CNN已经不存在。

### 🖋 3.2、网络结构

#### ✏ 3.2.1、基本网络

图像归一化为 224×224 后直接送入网络。前五阶段是基础的`conv+relu+pooling`形式，在第五阶段结尾，输入P个候选区域（图像序号×1 + 几何位置×4，序号用于训练）。

![&#x56FE;6 Fast R-CNN&#x6A21;&#x578B;&#x4E2D;&#x7684;Backbone&#x7F51;&#x7EDC;](../../.gitbook/assets/image%20%2819%29.png)

> 注：文中给出了大中小三种网络，此处显示出最大的一种。三种网络基本结构相似，仅`conv+relu`层数有差别，或者增删了norm层。

#### ✏ 3.2.2、[`RoI pooling layer`](roi-pooling.md) 

每一个`RoI`都有一个四元组 $$(r,c,h,w)$$ 表示，其中 $$(r,c)$$ 表示左上角，而 $$(h,w)$$ 则代表高度和宽度。这一层使用最大池化（max pooling）来将`RoI`区域转化成固定大小的 $$H\times W$$ 的特征图。假设一个`RoI`的窗口大小为 $$h\times w$$ ，则转换成 $$H\times W$$ 之后，每一个网格都是一个 $$h/H * w/W$$ 大小的子网，利用最大池化将这个子网中的值映射到 $$H\times W$$ 窗口即可。Pooling对每一个特征图通道都是独立的，这是`SPP layer`的特例，即只有一层的空间金字塔。

#### ✏ 3.2.3、**分类器与回归器**

分类器和回归器的输入为`RoI`池化输出的固定大小向量经过两层全连接层后产生的特征向量，属于并联，分类器用于判断物品属于哪一类（类别+背景），回归器用于计算4个调整因子，调整因子部分内容见R-CNN笔记。

### 🖋 3.3、测试过程

![](../../.gitbook/assets/image%20%2816%29.png)

1. 任意size图片输入CNN网络，经过若干卷积层与池化层，得到特征图；
2. 在任意size图片上采用selective search算法提取约`2k`个建议框；
3. 根据原图中建议框到特征图映射关系，在特征图中找到每个建议框对应的特征框【深度和特征图一致】，并在`RoI`池化层中将每个特征框池化到 $$H\times W$$ 【`VGG-16`网络是`7×7`】的size；
4. 固定 $$H\times W$$【`VGG-16`网络是`7×7`】大小的特征框经过全连接层得到固定大小的特征向量；
5. 第4步所得特征向量经由各自的全连接层【由`SVD`分解实现】，分别得到两个输出向量：一个是`softmax`的分类得分，一个是Bounding-box窗口回归；
6. 利用窗口得分分别对每一类物体进行非极大值抑制剔除重叠建议框，最终得到每个类别中回归修正后的得分最高的窗口。

#### ✏ **3.2.1、全连接层提速**

**为什么要采用`SVD`分解实现Fast R-CNN网络中最后的全连接层？具体如何实现？**

图像分类任务中，用于卷积层计算的时间比用于全连接层计算的时间多，而在目标检测任务中，selective search算法提取的建议框比较多，几乎有一半的前向计算时间被花费于全连接层，就Fast R-CNN而言，`RoI`池化层后的全连接层需要进行约 `2k` 次【每个建议框都要计算】，因此在Fast R-CNN中可以采用`SVD`分解加速全连接层计算；

分类和位置调整都是通过全连接层实现的，设前一级数据为 x 后一级为 y，全连接层参数为 $$W$$ ，尺寸 $$u\times v$$ 。一次前向传播（forward）即为 $$y=Wx$$ ，计算复杂度为$$u\times v$$ 。 将W进行`SVD`分解，并用前 $$t$$ 个特征值近似：

$$
W=U\sum V^T \approx U(:,1:t)⋅\sum (1:t,1:t)⋅V(:,1:t)^T
$$

原来的前向传播分解成两步：

$$
y=Wx=U⋅(\sum ⋅V^T)⋅x=U⋅z
$$

计算复杂度变为 $$u×t+v×t$$ 。 在实现时，相当于把一个全连接层拆分成两个，中间以一个低维数据相连。第一个全连接层不含偏置，第二个全连接层含偏置；实验表明，`SVD`分解全连接层能使`mAP`只下降0.3%的情况下提升30%的速度，同时该方法也不必再执行额外的微调操作。

![](../../.gitbook/assets/image%20%2817%29.png)

> 在GitHub的源码中，这部分似乎没有实现。

### 🖋 3.4、训练过程

#### ✏ 3.4.1、有监督预训练

**3.4.1.1、参数初始化**

（1）、网络除去末尾部分（即backbone）如图6，在`ImageNet`上训练 1000 类分类器。结果参数作为相应层的初始化参数。其余参数随机初始化。

（2）、从预训练的网络中初始化数据 有三种预训练的网络：`CaffeNet`，`VGG_CNN_M_1024`，`VGG-16`，他们都有5个最大池化层和5到13个不等的卷积层。用他们来初始化Fast R-CNN时，需要修改三处：

* ①最后一个池化层被`RoI pooling layer`取代；
* ②最后一个全连接层和`softmax`被替换成之前介绍过的两个兄弟并列层；
* ③网络输入两组数据：一组图片和那些图片的一组`RoIs`。

**3.4.1.2、分类与位置调整**

第五阶段的特征输入到两个并行的全连层中（称为multi-task）。

![&#x56FE;7 Fast R-CNN&#x4E2D;&#x7684;&#x5206;&#x7C7B;&#x548C;&#x4F4D;&#x7F6E;&#x56DE;&#x5F52;&#x7F51;&#x7EDC;](../../.gitbook/assets/image%20%2820%29.png)

> `cls_score`层用于分类，输出 $$K+1$$ 维数组p，表示属于 K 类和背景的概率。 `bbox_prdict`层用于调整候选区域位置，输出 $$4\times K$$ 维数组 t，表示分别属于 K 类时，应该平移缩放的参数。

#### ✏ 3.4.2、特定样本下的微调

使用BP算法训练网络是Fast R-CNN的重要能力，`SPP-net`不能微调`SPP`层之前的层，主要是因为当每一个训练样本来自于不同的图片时，经过`SPP`层的BP算法是很低效的（感受野太大）。

Fast R-CNN提出`SGD mini_batch`分层取样的方法：首先随机取样`N`张图片，然后每张图片取样`R/N`个`RoIs e.g. N=2 and R=128`， 除了分层取样，还有一个就是Fast R-CNN在一次微调中联合优化`softmax`分类器和`bbox`回归，看似一步，实际包含了多任务损失（multi-task loss）、小批量取样（mini-batch sampling）、`RoI pooling`层的反向传播（`backpropagation through RoI pooling layers`）、`SGD`超参数（`SGD hyperparameters`）。

**3.4.2.1、分层数据**

在调优训练时，每一个mini-batch中首先加入N张完整图片，而后加入从 N 张图片中选取的 R 个候选框。这 R 个候选框可以复用 N 张图片前5个阶段的网络特征。

> 实际选择N=2， R=128。

**3.4.2.2、训练数据构成**

N张完整图片以50%概率水平翻转。 R个候选框的构成方式如下：

| 类别 | 比例 | 方式 |
| :--- | :--- | :--- |
| 前景 | 25% | 与某个真值重叠在 \[0.5,1\] 的候选框 |
| 背景 | 75% | 与真值重叠的最大值在 \[0.1,0.5\) 的候选框 |

#### \*\*\*\*✏ 3.4.3、**代价函数** **（Multi-task loss）**

![](../../.gitbook/assets/image%20%2834%29.png)

* $$p$$ 是分类器预测的 ****`softmax` 概率分布 $$p=(p_0,\ldots,p_k)$$ ；
* $$u$$ 对应目标真实类别标签；
* $$t^u$$ 对应边界框回归器预测的对应类别 $$u$$ 的回归参数 $$(t_x^u, t_y^u, t_w^u, t_h^u)$$ ；
* $$v$$ 对应真实目标的边界框回归参数 $$(v_x, v_y, v_w, v_h)$$ ；
* $$[u\ge 1]$$ 是艾弗森括号。

`loss_cls`层评估分类代价。由真实分类 $$u$$ 对应的概率决定：

$$
L_{cls} = −log\ p_u
$$

`loss_bbox`评估检测框定位代价。比较真实分类对应的预测参数 $$t^u$$ 和真实平移缩放参数为 $$v$$ 的差别：

$$
L_{loc}=\sum\limits_{i\in\{x,y,w,h\}} smooth_{L_1}(t^u_i−v_i)
$$

$$smooth_{L_1}$$ 为`Smooth L1`误差，对outlier不敏感：

$$
smooth_{L_1}(x)= \begin{cases}0.5x^2 & |x|<1 \\
|x|−0.5 & otherwise \end{cases}
$$

总代价为两者加权和，如果分类为背景则不考虑定位代价：

$$
L= \begin{cases} L_{cls}+λL_{loc} & u\text{为前景} \\
L_{cls} & u\text{为背景} \end{cases}
$$

> 源码中`bbox_loss_weights`用于标记每一个`bbox`是否属于某一个类。

## 🖌 4、Faster R-CNN

经过R-CNN和Fast R-CNN的积淀，`Ross B. Girshick`在2016年提出了新的Faster R-CNN，在结构上，Faster R-CNN已经将特征抽取，proposal提取，bounding box regression和classification都整合在了一个网络中，使得综合性能有较大提高，在检测速度方面尤为明显。

![&#x56FE;8 Faster R-CNN&#x57FA;&#x672C;&#x7ED3;&#x6784;&#xFF08;&#x6765;&#x81EA;&#x539F;&#x8BBA;&#x6587;&#xFF09;](../../.gitbook/assets/image%20%2818%29.png)

如图，Faster R-CNN其实可以分为4个主要内容：

1. `Conv layers`。作为一种CNN网络目标检测方法，Faster R-CNN首先使用一组基础的`conv+relu+pooling`层提取image的feature maps。该feature maps被共享用于后续`RPN`层和全连接层。
2. `Region Proposal Networks`。`RPN`网络用于生成region proposals。该层通过`softmax`判断anchors属于positive或者negative，再利用bounding box regression修正anchors获得精确的proposals。（anchor与proposal的区别）
3. `Roi Pooling`。该层收集输入的feature maps和proposals，综合这些信息后提取proposal feature maps，送入后续全连接层判定目标类别。
4. `Classification and Bounding Box Regression`。利用proposal feature maps计算proposal的类别，**同时再次bounding box regression获得检测框最终的精确位置**。

![&#x56FE;9 faster\_rcnn\_test.pt&#x7F51;&#x7EDC;&#x7ED3;&#x6784; &#xFF08;pascal\_voc/VGG16/faster\_rcnn\_alt\_opt/faster\_rcnn\_test.pt&#xFF09;](../../.gitbook/assets/image%20%2827%29.png)

图9展示了python版本中的`VGG16`模型中的`faster_rcnn_test.pt`的网络结构，可以清晰的看到该网络对于一副任意大小 $$P\times Q$$ 的图像，首先缩放至固定大小 $$M\times N$$ ，然后将 $$M\times N$$ 图像送入网络；而`Conv layers`中包含了 `13个conv层+13 个relu层+4个pooling层`；`RPN`网络首先经过 `3x3` 卷积，再分别生成positive anchors和对应bounding box regression偏移量，然后计算出`proposals`；而`Roi Pooling`层则利用`proposals`从`feature maps`中提取`proposal feature`送入后续全连接和`softmax`网络作`classification`（即分类proposal到底是什么object）。

### 🖋 4.1、`Conv layers`

`Conv layers`包含了`conv`，pooling，`relu`三种层。以python版本中的`VGG16`模型中的`faster_rcnn_test.pt`的网络结构为例，如图9，`Conv layers`部分共有13个`conv`层，13个`relu`层，4个pooling层。这里有一个非常容易被忽略但是又无比重要的信息，在`Conv layers`中：

1. 所有的`conv`层都是：kernel\_size=3，pad=1，stride=1；
2. 所有的pooling层都是：kernel\_size=2，pad=1，stride=1。

为何重要？在`Faster R-CNN Conv layers`中对所有的卷积都做了扩边处理（ `pad=1`，即填充一圈0），导致原图变为 $$(M+2)\times(N+2)$$ 大小，再做 $$3\times 3$$ 卷积后输出$$M\times N$$ 。正是这种设置，导致`Conv layers`中的`conv`层不改变输入和输出矩阵大小。如图：

![](../../.gitbook/assets/image%20%2815%29.png)

类似的是，`Conv layers`中的pooling层`kernel_size=2`，`stride=2`。这样每个经过pooling层的 $$M\times N$$ 矩阵，都会变为 $$(M/2)\times (N/2)$$ 大小。综上所述，在整个`Conv layers`中，`conv`和`relu`层不改变输入输出大小，只有pooling层使输出长宽都变为输入的 $$1/2$$ 。那么，一个$$M\times N$$大小的矩阵经过`Conv layers`固定变为$$(M/16)\times (N/16)$$ 。这样`Conv layers`生成的feature map中都可以和原图对应起来。

### 🖋 4.2、`Region Proposal Networks(RPN)`

经典的检测方法生成检测框都非常耗时，如`OpenCV adaboost`使用**滑动窗口+图像金字塔**生成检测框；或如R-CNN使用`SS(Selective Search)`方法生成检测框。而`Faster RCNN`则抛弃了传统的滑动窗口和SS方法，直接使用`RPN`生成检测框，这也是Faster R-CNN的巨大优势，能极大提升检测框的生成速度。

![&#x56FE;10 RPN&#x7F51;&#x7EDC;&#x7ED3;&#x6784;](../../.gitbook/assets/image%20%2824%29.png)

#### ✏ 4.2.1、anchors

所谓anchors，实际上就是一组由`rpn/generate_anchors.py`生成的矩形。直接运行作者demo中的`generate_anchors.py`可以得到以下输出：

```text
[[ -84.  -40.   99.   55.]
 [-176.  -88.  191.  103.]
 [-360. -184.  375.  199.]
 [ -56.  -56.   71.   71.]
 [-120. -120.  135.  135.]
 [-248. -248.  263.  263.]
 [ -36.  -80.   51.   95.]
 [ -80. -168.   95.  183.]
 [-168. -344.  183.  359.]]
```

其中每行的4个值 $$(x_1,y_1,x_2,y_2)$$ 表矩形左上和右下角点坐标。9个矩形共有3种形状，长宽比为大约为 $$width:height\in \{1:1,1:2,2:1\}$$ 三种，如图11。实际上通过anchors就引入了检测中常用到的多尺度方法。

![&#x56FE;11 anchors](../../.gitbook/assets/image%20%2825%29.png)

那么这9个anchors是做什么的呢？借用Faster R-CNN论文中的原图，如图12，遍历`Conv layers`计算获得的feature maps，为每一个点都配备这9种anchors作为初始的检测框。这样做获得检测框很不准确，不用担心，**后面还有2次bounding box regression可以修正检测框位置**。

![&#x56FE;12](../../.gitbook/assets/image%20%2822%29.png)

解释一下上面这张图的数字。

1. 在原文中使用的是`ZF model`中，其`Conv Layers`中最后的`conv5`层`num_output=256`，对应生成256张特征图，所以相当于feature map每个点都是256-dimensions；
2. 在`conv5`之后，做了`rpn_conv/3x3`卷积且`num_output=256`，相当于每个点又融合了周围 $$3\times 3$$ 的空间信息，同时`256-d`不变（如图11中的红框）；
3. 假设在`conv5 feature map`中每个点上有k个anchor（默认k=9），而每个`anhcor`要分positive和negative，所以每个点由`256-d feature`转化为`cls=2k scores`；而每个anchor都有`(x, y, w, h)`对应4个偏移量，所以`reg=4k coordinates`；
4. 补充一点，全部anchors拿去训练太多了，训练程序会在合适的anchors中随机选取128个`postive anchors`和128个`negative anchors`进行训练。

**其实`RPN`最终就是在原图尺度上，设置了密密麻麻的候选anchor。然后用CNN去判断哪些anchor是里面有目标的positive anchor，哪些是没目标的negative anchor。所以，仅仅是个二分类而已！**

那么anchor一共有多少个？原图`800x600`，`VGG`下采样16倍，feature map每个点设置9个Anchor，所以：

$$
ceil(800/16)\times ceil(600/16) \times 9 = 50 \times 38 \times 9 = 17100
$$

其中`ceil()`表示向上取整，是因为`VGG`输出的`feature map size= 50*38`。**忽略跨越边界的anchor以后，剩下约`6k`个。对于`RPN`生成的候选框之间存在大量重叠，基于候选框的`cls`得分，采用非极大值抑制，`IoU`设为0.7，这样每张图片只剩下`2k`个候选框**。

**1、为什么anchor坐标中有负数?**

回顾anchor生成步骤：首先生成 9 个base anchor，然后通过坐标偏移在 `50*38` 大小的 $$\frac{1}{16}$$ 下采样Feature Map每个点都放上这 9 个base anchor，就形成了 $$50*38*k$$ 个anchors。至于这 9 个base anchor坐标是什么其实并不重要，不同代码实现也许不同。

显然这里面有一部分边缘anchors会超出图像边界，而真实中不会有超出图像的目标，所以会有clip anchor步骤。

![](../../.gitbook/assets/image%20%2826%29.png)

**2、anchor到底与网络输出如何对应?**

`VGG`输出 $$50*38*512$$ 的特征，对应设置 $$50*38*k$$ 个anchors，而`RPN`输出 $$50*38*2k$$ 的分类特征矩阵和 $$50*38*4k$$ 的坐标回归特征矩阵。

![](../../.gitbook/assets/image%20%2823%29.png)

其实在实现过程中，每个点的 `2k` 个分类特征与 `4k` 回归特征，与 `k` 个anchor逐个对应即可，这实际是一种“人为设置的逻辑映射”。当然，也可以不这样设置，但是无论如何都需要保证在训练和测试过程中映射方式必须一致。

#### ✏ 4.2.2、**`softmax`判定positive与negative**

一副 $$M\times N$$ 大小的矩阵送入Faster R**-**CNN网络后，到`RPN`网络变为 $$(M/16)\times (N/16)$$ ，不妨设 $$W=M/16$$ ， $$H=N/16$$ 。在进入reshape与`softmax`之前，先做了 $$1\times 1$$ 卷积，该$$1\times 1$$ 卷积的`caffe prototxt`定义如下：

```python
layer {
  name: "rpn_cls_score"
  type: "Convolution"
  bottom: "rpn/output"
  top: "rpn_cls_score"
  convolution_param {
    num_output: 18   # 2(positive/negative) * 9(anchors)
    kernel_size: 1 pad: 0 stride: 1
  }
}
```

可以看到其`num_output=18`，也就是经过该卷积的输出图像为 $$W\times H\times 18$$ 大小。这也就刚好对应了feature maps每一个点都有9个anchors，同时每个anchors又有可能是positive和negative，所有这些信息都保存 $$W\times H\times (9\times2)$$ 大小的矩阵。后面接`softmax`分类获得positive anchors，也就相当于初步提取了检测目标候选区域`box`（一般认为目标在positive anchors中）。

那么为何要在`softmax`前后都接一个reshape layer？其实只是为了便于`softmax`分类，至于具体原因这就要从`caffe`的实现形式说起了。在`caffe`基本数据结构blob中以如下形式保存数据：

```text
blob=[batch_size, channel, height, width]
```

对应至上面的保存`positive/negative anchors`的矩阵，其在`caffe blob`中的存储形式为 $$ [1, 2\times 9, H, W]$$ 。而在`softmax`分类时需要进行`positive/negative`二分类，所以reshape layer会将其变为 $$[1, 2, 9\times H, W]$$ 大小，即单独“腾空”出来一个维度以便`softmax`分类，之后再reshape回复原状。贴一段`caffe softmax_loss_layer.cpp`的reshape函数的解释，非常精辟：

```text
"Number of labels must match number of predictions; "
"e.g., if softmax axis == 1 and prediction shape is (N, C, H, W), "
"label count (number of labels) must be N*H*W, "
"with integer values in {0, 1, ..., C-1}.";
```

综上所述，`RPN`网络中利用`softmax`初步提取出positive anchors作为候选区域（另外也有实现用`sigmoid`代替`softmax`，原理类似）。

#### ✏ 4.2.3、**对proposals进行bounding box regression**

这里的$$1\times 1$$ 卷积的`caffe prototxt`定义：

```python
layer {
  name: "rpn_bbox_pred"
  type: "Convolution"
  bottom: "rpn/output"
  top: "rpn_bbox_pred"
  convolution_param {
    num_output: 36   # 4 * 9(anchors)
    kernel_size: 1 pad: 0 stride: 1
  }
}
```

可以看到其 `num_output=36`，即经过该卷积输出图像为 $$W\times H\times 36$$ ，在`caffe blob`存储为 $$[1, 4\times 9, H, W]$$ ，这里相当于feature maps每个点都有 9 个anchors，每个anchors又都有 4 个用于回归的变换量：

$$
[d_x(A),d_y(A),d_w(A),d_h(A)]
$$

回到图10，`VGG`输出 $$50*38*512$$ 的特征，对应设置 $$50*38*k$$ 个anchors，而`RPN`输出：

1. 大小为 $$50*38*2k$$ 的`positive/negative softmax`分类特征矩阵；
2. 大小为 $$50*38*4k$$ 的regression坐标回归特征矩阵。

恰好满足 `RPN` 完成 positive/negative 分类 和bounding box regression**。**

#### ✏ **4.2.4、**Proposal Layer

**Proposal Layer负责综合所有** $$[d_x(A),d_y(A),d_w(A),d_h(A)]$$ **变换量和positive anchors，计算出精准的proposals，送入后续`RoI Pooling Layer`**。还是先来看看Proposal Layer的`caffe prototxt`定义：

```python
layer {
  name: 'proposal'
  type: 'Python'
  bottom: 'rpn_cls_prob_reshape'
  bottom: 'rpn_bbox_pred'
  bottom: 'im_info'
  top: 'rois'
  python_param {
    module: 'rpn.proposal_layer'
    layer: 'ProposalLayer'
    param_str: "'feat_stride': 16"
  }
}
```

Proposal Layer有3个输入：`positive vs negative anchors`分类器结果`rpn_cls_prob_reshape`，对应的`bbox reg`的 $$[d_x(A),d_y(A),d_w(A),d_h(A)]$$ 变换量`rpn_bbox_pred`，以及`im_info`；另外还有参数`feat_stride=16`。 首先解释`im_info`。对于一副任意大小 $$P\times Q$$ 图像，传入Faster R-CNN前首先reshape到固定 $$M\times N$$ ， $$\text{im_info}=[M, N, \text{scale_factor}]$$则保存了此次缩放的所有信息。然后经过`Conv Layers`，经过4次pooling变为 $$W \times H=(M/16)\times (N/16)$$ 大小，其中 `feature_stride=16` 则保存了该信息，用于计算anchor偏移量。

Proposal Layer forward（`caffe layer`的前传函数）按照以下顺序依次处理：

1. 生成anchors，利用 $$[d_x(A),d_y(A),d_w(A),d_h(A)]$$ 对所有的anchors做`bbox regression`回归（这里的anchors生成和训练时完全一致）。
2. 按照输入的`positive softmax scores`由大到小排序anchors，提取前`pre_nms_topN(e.g. 6000)`个anchors，即提取修正位置后的positive anchors。
3. clip超出图像边界的positive anchors（防止后续`roi pooling`时proposal超出图像边界）。
4. 剔除非常小（`width<threshold or height<threshold`）的positive anchors。
5. 进行`nonmaximum suppression`。

之后输出 $$proposal=(x_1,y_1,x_2,y_2)$$ ，注意，由于在第三步中将anchors映射回原图判断是否超出边界，所以这里输出的proposal是对应 $$M\times N$$ 输入图像尺度的，这点在后续网络中有用。另外我认为，严格意义上的检测应该到此就结束了，后续部分应该属于识别了。

> `RPN`网络结构总结起来就是：生成`anchors -> softmax`分类器提取`positvie anchors -> bbox reg`回归`positive anchors -> Proposal Layer`生成`proposals`

### 🖋 4.3、[`RoI Pooling`](roi-pooling.md) 

### 🖋 4.4、`RPN Multi-task loss`

![](../../.gitbook/assets/image%20%2831%29.png)

* $$p_i$$ 表示第 $$i$$ 个anchor 预测为真实标签的概率；
* $$p_i^*$$ 当为正样本时为 1，当为负样本时为0；
* $$t_i$$ 表示预测第 $$i$$ 个 anchor的边界框回归参数；
* $$t_i^*$$ 表示第 $$i$$ 个 anchor 对应的 `GT Box` 的边界框回归参数；
* $$N_{cls}$$ 表示一个 `mini-batch` 中的所有样本数量256；
* $$N_{reg}$$ 表示 anchor 位置的个数 （不是anchor 个数） 约2400.

可以化简： $$\lambda$$ 在论文中取值为10，则$$\frac{1}{N_{cls}} \approx \lambda\frac{1}{N_{reg}}$$ ，PyTorch官方实现就是这种方法。

#### 1、分类损失有两种实现方式

![](../../.gitbook/assets/image%20%2833%29.png)

![](../../.gitbook/assets/image%20%2828%29.png)

#### 2、边界框回归损失

![](../../.gitbook/assets/image%20%2832%29.png)

### 🖋 4.5、Faster R-CNN训练

#### ✏ 4.5.1、分部训练（原论文）

原论文中采用分别训练`RPN`以及Fast R-CNN的方法：

1. 利用`ImageNet`预训练分类模型初始化前置卷积网络层参数，并 开始单独训练`RPN`网络参数； 
2. 固定`RPN`网络独有的卷积层以及全连接层参数，再利用 `ImageNet`预训练分类模型初始化前置卷积网络参数，并利用`RPN` 网络生成的目标建议框去训练Fast R-CNN网络参数。 
3. 固定利用Fast R-CNN训练好的前置卷积网络层参数，去微调`RPN` 网络独有的卷积层以及全连接层参数。 
4. 同样保持固定前置卷积网络层参数，去微调Fast R-CNN网络的全 连接层参数。最后`RPN`网络与Fast R-CNN网络共享前置卷积网络层 参数，构成一个统一网络。

#### ✏ 4.5.2、联合训练

直接采用`RPN Loss+ Fast R-CNN Loss`的联合训练方法。

