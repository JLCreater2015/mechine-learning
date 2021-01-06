# Faster-RCNN原理

## ✏ 1、R-CNN

 **论文：Rich feature hierarchies for accurate object detection and semantic segmentation**

![](../../.gitbook/assets/image%20%286%29.png)

R**-**CNN算法分为4个步骤

* 一张图像生成`1K~2K`个**候选区域（`ROI`）；**
* 对每个候选区域（Warp后的Region）使用深度网络**提取特征；**
* 将特征送入每一类的**`SVM` 分类器**，判别是否属于该类；
* 使用**回归器**精细修正候选框位置。

### 🖋 1.1、

\*\*\*\*

