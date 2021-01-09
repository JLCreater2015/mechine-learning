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

#### ✏ 3.2.2、`RoI pooling layer`

每一个`RoI`都有一个四元组 $$(r,c,h,w)$$ 表示，其中 $$(r,c)$$ 表示左上角，而 $$(h,w)$$ 则代表高度和宽度。这一层使用最大池化（max pooling）来将`RoI`区域转化成固定大小的 $$H\times W$$ 的特征图。假设一个`RoI`的窗口大小为 $$h\times w$$ ，则转换成 $$H\times W$$ 之后，每一个网格都是一个 $$h/H * w/W$$ 大小的子网，利用最大池化将这个子网中的值映射到 $$H\times W$$ 窗口即可。Pooling对每一个特征图通道都是独立的，这是`SPP layer`的特例，即只有一层的空间金字塔。

#### ✏ 3.2.3、**分类器与回归器**

分类器和回归器的输入为`RoI`池化输出的固定大小向量经过两层全连接层后产生的特征向量，分类器用于判断物品属于哪一类（类别+背景），回归器用于计算4个调整因子，调整因子部分内容见R-CNN笔记。

### 🖋 3.3、测试过程

![](../../.gitbook/assets/image%20%2816%29.png)

1. 任意size图片输入CNN网络，经过若干卷积层与池化层，得到特征图；
2. 在任意size图片上采用selective search算法提取约`2k`个建议框；
3. 根据原图中建议框到特征图映射关系，在特征图中找到每个建议框对应的特征框【深度和特征图一致】，并在`RoI`池化层中将每个特征框池化到 $$H\times W$$ 【`VGG-16`网络是7×7】的size；
4. 固定 $$H\times W$$【`VGG-16`网络是7×7】大小的特征框经过全连接层得到固定大小的特征向量；
5. 第4步所得特征向量经由各自的全连接层【由`SVD`分解实现】，分别得到两个输出向量：一个是`softmax`的分类得分，一个是Bounding-box窗口回归；
6. 利用窗口得分分别对每一类物体进行非极大值抑制剔除重叠建议框，最终得到每个类别中回归修正后的得分最高的窗口。

#### ✏ **3.2.1、全连接层提速**

**为什么要采用`SVD`分解实现Fast R-CNN网络中最后的全连接层？具体如何实现？**

图像分类任务中，用于卷积层计算的时间比用于全连接层计算的时间多，而在目标检测任务中，selective search算法提取的建议框比较多，几乎有一半的前向计算时间被花费于全连接层，就Fast R-CNN而言，`RoI`池化层后的全连接层需要进行约 `2k` 次【每个建议框都要计算】，因此在Fast R-CNN中可以采用`SVD`分解加速全连接层计算；

分类和位置调整都是通过全连接层实现的，设前一级数据为 x 后一级为 y，全连接层参数为W，尺寸 $$u\times v$$ 。一次前向传播（forward）即为 $$y=Wx$$ ，计算复杂度为$$u\times v$$ 。 将W进行`SVD`分解，并用前 $$t$$ 个特征值近似：

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

**3.4.1.2、**[**`roi_pool`层**](roi-pooling.md)**的训练**

**3.4.1.3、分类与位置调整**

第五阶段的特征输入到两个并行的全连层中（称为multi-task）。

![&#x56FE;7 Fast R-CNN&#x4E2D;&#x7684;&#x5206;&#x7C7B;&#x548C;&#x4F4D;&#x7F6E;&#x56DE;&#x5F52;&#x7F51;&#x7EDC;](../../.gitbook/assets/image%20%2820%29.png)

> `cls_score`层用于分类，输出 $$K+1$$ 维数组p，表示属于 K 类和背景的概率。 `bbox_prdict`层用于调整候选区域位置，输出 $$4\times K$$ 维数组 t，表示分别属于 K 类时，应该平移缩放的参数。

**代价函数** 

`loss_cls`层评估分类代价。由真实分类 $$u$$ 对应的概率决定：

$$
L_{cls} = −log\ p_u
$$

`loss_bbox`评估检测框定位代价。比较真实分类对应的预测参数 $$t^u$$ 和真实平移缩放参数为 $$v$$ 的差别：

$$
L_{loc}=Σ^4_{i=1} g(t^u_i−v_i)
$$

$$g$$ 为`Smooth L1`误差，对outlier不敏感：

$$
g(x)= \begin{cases}0.5x^2 & |x|<1 \\
|x|−0.5 & otherwise \end{cases}
$$

总代价为两者加权和，如果分类为背景则不考虑定位代价：

$$
L= \begin{cases} L_{cls}+λL_{loc} & u\text{为前景} \\
L_{cls} & u\text{为背景} \end{cases}
$$

> 源码中`bbox_loss_weights`用于标记每一个`bbox`是否属于某一个类。

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

## 🖌 4、Faster R-CNN

经过R-CNN和Fast R-CNN的积淀，`Ross B. Girshick`在2016年提出了新的Faster R-CNN，在结构上，Faster R-CNN已经将特征抽取，proposal提取，bounding box regression和classification都整合在了一个网络中，使得综合性能有较大提高，在检测速度方面尤为明显。

![&#x56FE;8 Faster R-CNN&#x57FA;&#x672C;&#x7ED3;&#x6784;&#xFF08;&#x6765;&#x81EA;&#x539F;&#x8BBA;&#x6587;&#xFF09;](../../.gitbook/assets/image%20%2818%29.png)

如图，Faster R-CNN其实可以分为4个主要内容：

1. `Conv layers`。作为一种CNN网络目标检测方法，Faster R-CNN首先使用一组基础的`conv+relu+pooling`层提取image的feature maps。该feature maps被共享用于后续`RPN`层和全连接层。
2. `Region Proposal Networks`。`RPN`网络用于生成region proposals。该层通过`softmax`判断anchors属于positive或者negative，再利用bounding box regression修正anchors获得精确的proposals。
3. `Roi Pooling`。该层收集输入的feature maps和proposals，综合这些信息后提取proposal feature maps，送入后续全连接层判定目标类别。
4. Classification and Bounding Box Regression。利用proposal feature maps计算proposal的类别，同时再次bounding box regression获得检测框最终的精确位置。

### 🖋 4.1、`Conv layers`

`Conv layers`包含了`conv`，pooling，`relu`三种层。以python版本中的`VGG16`模型中的faster\_rcnn\_test.pt的网络结构为例，如图2，`Conv layers`部分共有13个`conv`层，13个`relu`层，4个pooling层。这里有一个非常容易被忽略但是又无比重要的信息，在`Conv layers`中：

1. 所有的`conv`层都是：kernel\_size=3，pad=1，stride=1；
2. 所有的pooling层都是：kernel\_size=2，pad=1，stride=1。

为何重要？在`Faster R-CNN Conv layers`中对所有的卷积都做了扩边处理（ `pad=1`，即填充一圈0），导致原图变为 $$(M+2)\times(N+2)$$ 大小，再做 $$3\times 3$$ 卷积后输出$$M\times N$$ 。正是这种设置，导致`Conv layers`中的`conv`层不改变输入和输出矩阵大小。如图：

![](../../.gitbook/assets/image%20%2815%29.png)

类似的是，`Conv layers`中的pooling层`kernel_size=2`，`stride=2`。这样每个经过pooling层的 $$M\times N$$ 矩阵，都会变为 $$(M/2)\times (N/2)$$ 大小。综上所述，在整个`Conv layers`中，`conv`和`relu`层不改变输入输出大小，只有pooling层使输出长宽都变为输入的1/2。那么，一个$$M\times N$$大小的矩阵经过`Conv layers`固定变为$$(M/16)\times (N/16)$$ 。这样`Conv layers`生成的feature map中都可以和原图对应起来。

### 🖋 4.2、`Region Proposal Networks(RPN)`

经典的检测方法生成检测框都非常耗时，如`OpenCV adaboost`使用滑动窗口+图像金字塔生成检测框；或如R-CNN使用SS\(Selective Search\)方法生成检测框。而`Faster RCNN`则抛弃了传统的滑动窗口和SS方法，直接使用`RPN`生成检测框，这也是Faster R-CNN的巨大优势，能极大提升检测框的生成速度。

