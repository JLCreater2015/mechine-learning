# YOLO原理

## 🖌 1、YOLO v1

**论文：You Only Look Once: Unified, Real-Time Object Detection 【**[**链接**](https://arxiv.org/pdf/1506.02640.pdf)**】**

![&#x56FE; 1 YOLO v1&#x7B97;&#x6CD5;&#x601D;&#x60F3;](../../.gitbook/assets/image%20%2854%29.png)

### ✏ 1.1、算法流程

![&#x56FE; 2](../../.gitbook/assets/image%20%2851%29.png)

1. 将一幅图像分成 $$S\times S$$ （论文中为7）个网格`(grid cell)`， 如果某个object的中心（GT）落在这个网格 中，则这个网格就负责预测这个object。如图2所示，图中物体狗的中心点（红色原点）落入第5行、第2列的格子内，所以这个格子负责预测图像中的物体狗。
2. 每个网格要预测B（论文中为2）个bounding box，每个bounding box 除了要预测位置之外，还要附带预测一个confidence值。 每个网格还要预测C个类别的分数（PASCAL VOC为20）。

经过神经网络对输入图像信息的提取和变换，网格周边的信息也会被识别和整理，最后编码到那个30维向量中：

*  **20个对象分类的概率：**可以记为 $$P(C_i|Object)$$ ，之所以写成条件概率，意思是如果该网格存在一个对象Object，那么它是 $$C_i$$ 的概率是 $$P(C_i|Object)$$ 。
* **2个bounding box的位置：**每个bounding box需要4个数值来表示其位置`(Center_x,Center_y,width,height)`，即\(bounding box的中心点的`x`坐标，`y`坐标，bounding box的宽度，高度\)，2个bounding box共需要8个数值来表示其位置。
* **2个bounding box的置信度：**bounding box的置信度 = 该bounding box内存在对象的概率 \* 该bounding box与该对象实际bounding box的IOU， 用公式来表示就是

$$
Confidence = Pr(Object)*IOU_{pred}^{truth}
$$

$$IOU_{pred}^{truth}$$ 可以直接计算出来，就是用网络输出的2个 bounding box 与对象真实 bounding box 一起计算出IOU。然后看2个 bounding box 的 IOU，哪个比较大（更接近对象实际的 bounding box），就由哪个bounding box来负责预测该对象是否存在，即该 bounding box 的 $$Pr(Object)=1$$ ，同时对象真实bounding box 的位置也就填入该 bounding box。另一个不负责预测的bounding box的 $$Pr(Object)=0$$ 。总的来说就是，与对象实际 bounding box 最接近的那个 bounding box，其 $$Confidence = IOU_{pred}^{truth}$$ ，该网格的其它 bounding box 的 $$Confidence = 0$$ 。

### ✏ 1.2、损失函数

![&#x56FE; 3 YOLO v1&#x7684;&#x635F;&#x5931;&#x51FD;&#x6570;](../../.gitbook/assets/image%20%2853%29.png)

## 🖌 2、YOLO v2

**论文：YOLO9000: Better, Faster, Stronger** 【[链接](https://arxiv.org/pdf/1612.08242v1.pdf)】



## 🖌 3、YOLO v3

**论文：YOLOv3: An Incremental Improvement 【**[**链接**](https://pjreddie.com/media/files/papers/YOLOv3.pdf)**】**

\*\*\*\*

## 🖌 4、YOLO v3 + SPP

