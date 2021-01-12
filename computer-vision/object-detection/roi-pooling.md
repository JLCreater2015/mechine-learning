# RoI Pooling

 `RoI(Region of Interest)`是通过不同区域选择方法，从**原始图像（original image）**得到的候选区域（proposal region）。

量化（`quatization`）是指将输入从连续值（或大量可能的离散取值）采样为有限多个离散值的过程。也可以理解为，将输入数据集（如实数）约束到离散集（如整数）的过程。

## 🖌 1、**`RoI Pooling`**

分析之前先来看看`RoI Pooling Layer`的`caffe prototxt`的定义：

```python
layer {
  name: "roi_pool5"
  type: "ROIPooling"
  bottom: "conv5_3"
  bottom: "rois"
  top: "pool5"
  roi_pooling_param {
    pooled_w: 7
    pooled_h: 7
    spatial_scale: 0.0625 # 1/16
  }
}
```

其中有新参数`pooled_w`和`pooled_h`，`RoI Pooling layer forward`过程：

* 由于proposal是对应 $$M\times N$$ 尺度的，所以首先使用spatial\_scale参数将其映射回 $$(M/16)\times (N/16)$$ 大小的feature map尺度；
* 再将每个proposal对应的feature map区域分为 $$pooled_w \times pooled_h$$ 的网格；
* 对网格的每一份都进行max pooling处理。

这样处理后，即使大小不同的`proposal`输出结果都是 $$pooled_w \times pooled_h$$ 固定大小，实现了固定长度输出。

 **`RoI Pooling = crop feature + resize feature`**

### 🖋 执行步骤

**以输出目标特征图尺寸大小为** ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2%5Ctimes512) **进行说明**

* 对齐到网格单元（snap to grid cell）

首先将一个浮点数`RoI`量化为特征映射的离散粒度。表现为`RoI`对应的特征图的与原始特征图的网格单元对齐。**这里为第一次量化操作。**下图中绿色框为`RoI`对应的实际区域（**由于经过特征尺度变换，导致`RoI`的坐标会可能会落到特征图的单元之间**）， 蓝色框代表量化（网格对齐）后的`RoI`所对应的特征图。（得到到量化特征图尺寸为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=5%5Ctimes7%5Ctimes512) ）。

![](../../.gitbook/assets/image%20%2842%29.png)

* 划分网格为子区域（`bin`）

粗略地将网格分为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=H%5Ctimes+W) （Fast R-CNN 中设为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=7%5Ctimes7) ）个子网格区域。将上一步得到的量化`RoI` 特征进一步细分为量化的空间单元\(bin\)。**这里进行了第二次量化操作。**

为了得到输出的特征图为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2%5Ctimes512) ，这里的量化操作就是将上一步的到量化特征图划分为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2) 个特征单元。**如果无法通过直接均分得到量化的子区域，通过分别采取向上取整（`ceil`）和向下取整（floor）的到对应的单元尺寸大小**。以当前 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=4+%5Ctimes+5) 尺寸的特征图为例，对于宽度方向![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%5Cfrac%7B4%7D%7B2%7D+%3D+2)，但是对于高度方向由于 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%5Cfrac%7B5%7D%7B2%7D+%3D2.5)， 通过向上和向下取整整，确定高度方向特征子区域的大小分别为2和3。

![](../../.gitbook/assets/image%20%2844%29.png)

* 最大池化操作

在每一个子区域执行聚合操作得到单元的特征值（一般是最大池化）。对上一步得到的 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2) 个子区域分别做最大池化操作，得到 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2%5Ctimes512) 的目标特征图。

![](../../.gitbook/assets/image%20%2833%29.png)

### 🖋 缺点

每一次量化操作都会对应着轻微的区域特征错位（misaligned）， 这些量化操作在`RoI`和提取到的特征之间引入了偏差。这些量化可能不会影响对分类任务，但它对预测像素精度掩模有很大的负面影响。

## 🖌 2、`RoI Align`

### 🖋 2.1、执行步骤

**以输出目标特征图尺寸大小为** ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2%5Ctimes512) **进行说明**

* 遍历候选每个候选区域，保持浮点数边界不做量化（不对齐网格单元）；同时平均分网格分为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=H%5Ctimes+W) （这里为 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2) ）个子网格区域，每个单元的边界也不做量化。

![](../../.gitbook/assets/image%20%2832%29.png)

* 对于每个区域选择4个规则采样点（分别对应将区域进一步平均分为四个区域，取每个子区域的中点）。

![](../../.gitbook/assets/image%20%2840%29.png)

* 利用双线性插值计算得到四个采用点的像素值大小。下图为一个规则采样点所对应的邻近区域示意图。

![](../../.gitbook/assets/image%20%2843%29.png)

* 利用最大池化（max pooling）或平均池化\(average pooling\)分别对每个子区域执行聚合操作，得到最终的特征图。

![](../../.gitbook/assets/image%20%2836%29.png)

### 🖋 2.2、双线性插值`RoI Align`

双线性插值（`bilinear interpolation`），又称为双线性内插。在数学上，双线性插值是有两个变量的插值函数的线性插值扩展，其核心思想是在两个方向分别进行一次线性插值。在数字图像和音频处理领域都有应用。在图像处理中，双线性插值法**考虑围绕未知像素的计算位置的** ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2) **最近邻域的已知像素**。然后对这4个像素进行加权平均，以得出其最终的内插值。

如图，已知求位置像素P的像素值， 已知相邻 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=2%5Ctimes2) 的像素区域对应位置和像素值，其中坐下角像素区域标号为11，左上角像素区域标号为12，右下角像素区域标号为21，右上角像素区域标号为22。

![](../../.gitbook/assets/image%20%2841%29.png)

 $$Q_{11}=(x_1,y_1),Q_{12}=(x_1,y_2),Q_{21}=(x_2,y_1),Q_{22}=(x_2,y_2)$$ 分别对应**像素单元（区域）的中心点**。线性插值的计算公式描述：已知直线上两点 $$y_1=\phi(x_1,y_1), y_2=\phi(x_2,y_2)$$ ， 求直线上任意一点 $$(x,y)$$ 的值 $$\phi(x,y)$$ ：

$$
\phi(x,y)=\frac{x-x_1}{x_2-x_1}y2+\frac{x-x_2}{x_1-x_2}y1 \tag{1}
$$

 第一步，利用公式（1）执行两次线性插值操作：使用 $$Q_{11}$$ 和 $$Q_{21}$$ 计算 $$R_1=(x,y_1)$$ 点的像素值大小；使用 $$Q_{12}$$ 和 $$Q_{22}$$ 计算 $$R_2=(x,y_2)$$ 点像素值大小：

$$
\begin{align}
\phi(x,y_1)&=\frac{x-x_1}{x_2-x_1}\phi(x_2,y_1)+\frac{x-x_2}{x_1-x_2}\phi(x_1,y_1) \tag{2} \\
\phi(x,y_2)&=\frac{x-x_1}{x_2-x_1}\phi(x_2,y_2)+\frac{x-x_2}{x_1-x_2}\phi(x_1,y_2) \tag{3}
\end{align}
$$

 第二步， 利用公式 （2） 和公式 （3） 的到的结果，再次执行一次线性插值，得到目标位置 $$P$$ 的像素值：

$$
\begin{align}
\phi(x,y)&=\frac{y-y_1}{y_2-y_1}\phi(x,y_2)+\frac{y-y_2}{y_1-y_2}\phi(x,y_1) \\ &= \frac{1}{(y_2-y_1)(x_2-x_1)}((x-x_1)(y-y_1)\phi(x_2,y_2)+(x-x_2)(y-y_1)\phi(x_1,y_2)+)
\end{align}
$$

 由于 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=Q_%7B11%7D) , ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=Q_%7B12%7D) , ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=Q_%7B21%7D) , ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=Q_%7B22%7D) 分别为相邻像素的中间位置坐标，如下图所示：

![](../../.gitbook/assets/image%20%2834%29.png)



容易得到 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=y_2+-+y_1%3D1) ， ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=x_2-x_1%3D1) ，因此双线性插值公式 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=4) 可以进一步简化为

![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%5Cbegin%7Balign%7D+%5Cphi%28x%2Cy%29+%3D%26%28x-x_1%29%28y-y_1%29%5Cphi%28x_2%2C+y_2%29+%2B%28x-x_2%29%28y-y_1%29%5Cphi%28x_1%2C+y_2%29+%5C%5C%26%2B%28x-x_1%29%28y-y_2%29%5Cphi%28x_2%2C+y_1%29+%2B+%28x-x_1%29%28y-y_1%29%5Cphi%28x_2%2C+y_2%29+%5C%5C+%5Ctag%7B5%7D+%5Cend%7Balign%7D)

同理容易得到

![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%28x-x_1%29%28y-y_1%29+%2B%28x-x_2%29%28y-y_1%29+%2B%28x-x_1%29%28y-y_2%29%2B+%28x-x_1%29%28y-y_1%29+%3D+1+%5Ctag%7B6%7D)

公式 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=5) 也可以进一步表示为

![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=+%5Cphi%28x%2Cy%29+%3D+%5Csum_%7Bi%2Cj%3D1%7D%5E2%7B%5Cphi%28x_i%2C+y_j%29max%280%2C+1-%7Cx-x_i%7C%29max%280%2C+1-%7Cy-x_i%7C%29%7D+++%5Ctag%7B7%7D+)

* 公式物理意义

![](../../.gitbook/assets/image%20%2838%29.png)

通过公式 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=5) 可以看出， 双线性插值本质上是目标像素值相邻四个像素的像素值加权和值。

对于第一项 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%28x-x_1%29%28y-y_1%29%5Cphi%28x_2%2C+y_2%29) 表示右上角像素 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%28x_2%2C+y_2%29) 的像素值加权后的结果，其对应的权重公式 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%28x-x_1%29%28y-y_1%29) ，可以看出第一项权重本质上是目标像素 ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%28x_2%2Cy_2%29) 对应的对角像素![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=%28x_1%2C+y_1%29)所构成的矩形区域的面积大小，如上图紫色区域。同理其它三项也满足同样的规律。

**当目标元素与某个相邻元素的距离越近，目标元素元素与该相邻像素的对角像素组成的矩形框面积大小就越大，该相邻像素对应的权重值就越大。**

综上可以得到， **双线性插值本质上是目标像素所相邻的四个像素， 分别以像素对应的对角像素与目标像素的构成的矩形区域为权重，像素大小为值的加权和**。

