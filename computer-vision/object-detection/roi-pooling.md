# RoI Pooling

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
* 再将每个proposal对应的feature map区域水平分为 $$pooled_w \times pooled_h$$ 的网格；
* 对网格的每一份都进行max pooling处理。

这样处理后，即使大小不同的proposal输出结果都是 $$pooled_w \times pooled_h$$ 固定大小，实现了固定长度输出。

## 🖌 2、`RoI Align`

### 🖋 2.1、双线性插值

### 🖋 2.2、`RoI Align`

