# Spectral-based GCN

## 🖌 1、GCN的演变

Deep learning 中的Graph Convolution直接看上去会和第5节推导出的图卷积公式有很大的不同，但是万变不离其宗，都是根据下式来推导的：

$$
g_\theta * x = Ug_\theta U^Tx =U\left(\begin{matrix}\hat g(\lambda_1) &\\& \ddots \\ & & \hat g(\lambda_n) \end{matrix}\right) U^T x  \tag{3}
$$

上式计算量很大，因为特征向量矩阵 $$U$$ 的复杂度是 $$O(N^2)$$ 。此外，对于大型图来说， $$L$$ 特征值分解的计算量也很大。

### ✏ 1.2、GCN的演变——卷积参数

Deep learning 中的 Convolution 就是要设计含有 trainable 共享参数的 kernel，从上式看很直观：graph convolution 中的卷积参数就是 $$diag(\hat h(\lambda_l) )$$ （对角阵）。

**1、第一代的GCN\(Spectral Networks and Deep Locally Connected Networks on Graph\)简单粗暴地把** $$diag(\hat h(\lambda_l) )$$ **变成了卷积核** $$diag(\theta_l )$$ **，也就是：**

$$
y_{output}=\sigma \left(U g_\theta(\Lambda)U^T x \right) \tag{4}
$$

