# 谱图理论与傅里叶变换

## 🖌 1、图的拉普拉斯矩阵

**拉普拉斯矩阵（Laplacian matrix）也叫做导纳矩阵、基尔霍夫矩阵或离散拉普拉斯算子，主要应用在图论中，作为一个图的矩阵表示。** 对于图 $$G=(V,E)$$ ，其 Laplacian 矩阵的定义为 $$L=D-A$$ ，其中 L 是Laplacian 矩阵， $$D=diag(d)$$ 是顶点的度矩阵（对角矩阵）  ，对角线上元素 $$d=rowSum(A)$$ 依次为各个顶点的度， $$A$$ 是图的邻接矩阵。

Graph Fourier Transformation及Graph Convolution的定义都用到图的拉普拉斯矩阵。**频域卷积的前提条件是图必须是无向图，只考虑无向图，那么L就是对称矩阵。**

### ✏ **1.1、常见的拉普拉斯矩阵**

对于图 $$G=(V,E)$$ ，常见的拉普拉斯矩阵有：

**No.1 Combinatorial Laplacian（组合拉普拉斯）：** $$L=D-A$$ 

其中 `L` 是Laplacian矩阵，`D` 是顶点的度矩阵，是一个对角矩阵，对角上的元素依次为各个顶点的度，`A` 是图的邻接矩阵，计算方法示例如图：

![&#x56FE; 3 &#x7EC4;&#x5408;&#x62C9;&#x666E;&#x62C9;&#x65AF; ](../.gitbook/assets/image%20%2846%29.png)

其中 $$L$$ 由下列公式给出：

$$
L_{i,j}:=
\begin{cases}
deg(v_i) &  {if \ i = j}                                  \\
-1       &  {if\ i\neq j\ and\ v_i\ is\ adjacent\ to\ v_j} \\
0        &  {otherwise}
\end{cases}
$$

其中 $$deg(v_i)$$ 是顶点 $$i$$ 的度。

**No.2 Symmetric normalized Laplacian（对称归一化拉普拉斯）：** $$L^{sym}:=D^{-1/2}LD^{-1/2}=I-D^{-1/2}AD^{-1/2}$$ 

其中 $$L_{i,j}^{sym}$$ 由下列公式给出：

$$
L_{i,j}^{sym}:=
\begin{cases}
1        &  {if \ i = j}\ and\ deg(v_i)\neq 0                 \\
-\frac{1}{\sqrt{deg(v_i)deg(v_j)}} & {if\ i\neq j\ and\ v_i\ is\ adjacent\ to\ v_j} \\
0        &  {otherwise}
\end{cases}
$$

我们可以看看 $$D^{-1/2}AD^{-1/2}$$ 发生了什么，`D` 为只有主对角线上元素非０的对角阵，`A`中记录顶点间的邻接信息， $$D^{-1/2}AD^{-1/2}$$ 使得`A`中第 $$i$$ 行和第 $$i$$ 列的值都除以 $$\sqrt{D_{ii}}$$ 。

**No.3 Random walk normalized Laplacian（随机游走归一化拉普拉斯）：** $$L^{rw}:=D^{-1}L=I-D^{-1}A$$ 

其中 $$L_{i,j}^{rw}$$ 可以由下列方式计算：

$$
L_{i,j}^{sym}:=
\begin{cases}
1        &  {if \ i = j}\ and\ deg(v_i)\neq 0                 \\
-\frac{1}{deg(v_i)} & {if\ i\neq j\ and\ v_i\ is\ adjacent\ to\ v_j} \\
0        &  {otherwise}
\end{cases}
$$

**No.4 Generalized Laplacian（泛化的拉普拉斯）：**

泛化的拉普拉斯（用得少）定义为:

$$
\begin{cases}
Q_{i,j}\lt 0 & i \ \neq \ j \ and \ diag(v_i) \ \neq \ 0\\
Q_{i,j}=0 & i \neq j \ and \  v_i \  is\  adjacent \  to \  v_j\\
any\ number & otherwise
\end{cases}
$$

可以看出，标准归一化的拉普拉斯矩阵还是对称的，并且符合前面的公式定义。

Graph Convolution与Diffusion相似之处：从Random walk normalized Laplacian就能看出了两者确有相似之处（其实两者只差一个相似矩阵的变换，可参考[Diffusion-Convolutional Neural Networks](https://arxiv.org/abs/1511.02136)）。

### ✏ 1.2、对于拉普拉斯矩阵定义的理解

１、先从拉普拉斯算子说起，拉普拉斯算子数学定义是这样的：

$$
\triangle = \sum_i\frac {\partial^2} {\partial x_i^2}
$$

其含义很明确，是非混合二阶偏导数的和。

２、再看图像处理上是怎么近似的： 图像是一种离散数据，那么其拉普拉斯算子必然要进行离散化。由导数定义：

$$
\begin{aligned} f'(x) &= \frac {\partial f(x)}{\partial x}\\ & = \lim_{\delta \to 0} \frac{f(x+ \delta)-f(x)}{\delta}\\ & \approx^{离散化}
f(x+1)-f(x)\end{aligned}
$$

得出：

$$
\begin{aligned} \frac {\delta^2 f(x)}{\delta x^2} &= f''(x) \\ &\approx f'(x)-f'(x-1) \\ &\approx f(x+1)-f(x) - (f(x) - f(x-1))\\ &=f(x+1)+f(x-1)-2f(x) \end{aligned}
$$

{% hint style="success" %}
**结论1：二阶导数近似等于其二阶差分。** 

**结论2：二阶导数等于其在所有自由度上微扰之后获得的增益。**
{% endhint %}

一维函数其自由度可以理解为 `2`，分别是 `+1` 和 `-1` 两个方向。对于二维的图像来说，其有两个方向（`4` 个自由度）可以变化，即如果对 $$(x,y)$$ 处的像素进行扰动，其可以变为四种状态 $$(x+1,y),(x-1,y),(x,y+1),(x,y-1)$$ ****。当然了，如果将对角线方向也认为是一个自由度的话，会再增加几种状态 $$(x+1,y+1),(x+1,y-1),(x-1,y+1),(x-1,y-1)$$ ，事实上图像处理正是这种原理。 同理，将拉普拉斯算子离散化：

$$
\begin{aligned} \triangle &=\frac {\delta^2 f(x,y)}{\delta x^2} + \frac {\delta^2 f(x,y)}{\delta y^2} \\ &\approx f(x+1,y)+f(x-1,y)-2f(x,y) + [f(x,y+1)+f(x,y-1)-2f(x,y)]\\ &= f(x+1,y)+f(x-1,y)+f(x,y+1)+f(x,y-1)-4f(x,y) \end{aligned}
$$

上式可以理解为，在图像上某一点，其拉普拉斯算子的值，即为对其进行扰动，使其变化到相邻像素后得到的增益。这给我们一种形象的结论：拉普拉斯算子就是在所有自由度上进行微小变化后获得的增益。 推广到Graph，对于有 `N` 个节点的Graph，就设节点为 `1,...,N` 吧，且其邻接矩阵为 `A`。这个Graph的自由度最多为 `N`。因为如果该图是一个完全图，即任意两个节点之间都有一条边，那么对一个节点进行微扰，它可能变成任意一个节点。那么上面的函数 $$f$$ 就理所当然是一个 `N` 维的向量，即：

$$
f = (f_1,...,f_N)
$$

其中 `!$f_i$` 即表示函数 `!$f$` 在节点 `!$i$` 的值。类比 `!$f(x,y)$` 即为 `!$f$` 在 `!$(x,y)$` 处的值。对于任意节点 `!$i$` 进行微扰，它可能变为任意一个与他相邻的节点 `!$j\in\mathcal{N_i}$` ，其中 `!$\mathcal{N_i}$` 表示节点 `!$i$` 的一阶邻域节点。 对于Graph，从节点 `!$i$` 变化到节点 `!$j$` 增益是多少呢？即 `!$f_j-f_i$` 是多少？最容易想到就是和他们之间的边权相关。此处用 `!$A_{ij}$` 表示。那么，对于节点 `!$i$` 来说，其变化的增益:

$$
\sum_{j\in\mathcal{N}_i}A_{ij}[f_j-f_i]
$$

所以，对于Graph来说，其拉普拉斯算子如下：

$$
\begin{aligned} (\triangle f)_i &=\sum_i \frac {\delta^2 f} {\delta i^2}\\ &\approx \sum_{j\in\mathcal{N}_i}A_{ij}[f_j-f_i] \end{aligned}
$$

上式 $$j\in\mathcal{N}_i$$ 可以去掉，因为节点 `!$i$` 和 `!$j$` 不直接相邻的话， $$A_{ij} = 0$$ ； 继续化简一下：

$$
\begin{aligned} \sum_{j\in\mathcal{N}_i}A_{ij}[f_j-f_i]&=\sum_{j}A_{ij}f_j - \sum_{j}A_{ij}f_i\\ &=(Af)_i - (Df)_i \\ &=[(A-D)f]_i \end{aligned}
$$

即：

$$
(\triangle f)_i =[(A-D)f]_i
$$

对于任意的 $$i$$ 成立，那么也就是：

$$
\triangle f \equiv(A-D)f
$$

因此图上的拉普拉斯算子应该定义为 $$A-D$$ 。

