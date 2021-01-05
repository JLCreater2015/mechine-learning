# 高斯分布

数据 $$X=(x_1, x_2, \cdots, x_N)^T_{N\times p}=\begin{pmatrix}  x_{11} & x_{12} & \cdots & x_{1p} \\  x_{21} & x_{22} & \cdots & x_{2p} \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ x_{N1} & x_{N2} & \cdots & x_{Np} \end{pmatrix} _{N\times p}$$ 有 $$N$$ 个样本，每个样本的维度为 $$p$$ ， 即 $$x_i \in R^p$$ ， $$x_i \  \mathop{\sim}\limits^{iid} \  N(\mu, \Sigma)$$ （`iid`：独立同分布）。

令 $$\theta=(\mu,\Sigma)=(\mu,\sigma^{2})$$ ，在 `MLE` 方法中：

$$
\theta_{MLE}=\mathop{argmax}\limits _{\theta}\log p(X|\theta)\\
\log p(X|\theta)\mathop{=}\limits _{iid} log\prod\limits_{i = 1}^{N}p(x_{i}|\theta) = \sum\limits _{i=1}^{N}\log p(x_{i}|\theta)\tag{1}
$$

一般地，高斯分布的概率密度函数PDF写为：

$$
p(x|\mu,\Sigma)=\frac{1}{(2\pi)^{p/2}|\Sigma|^{1/2}}e^{-\frac{1}{2}(x-\mu)^{T}\Sigma^{-1}(x-\mu)}\tag{2}
$$

## ✏ 1、一维情况 `MLE`

将公式（2）带入 `MLE` 中，考虑一维的情况，即 $$p=1$$ ：

$$
\begin{split} \log p(X|\theta)=\sum\limits _{i=1}^{N}\log p(x_{i}|\theta) &=\sum\limits _{i=1}^{N}\log\frac{1}{\sqrt{2\pi}\sigma}\exp(-(x_{i}-\mu)^{2}/2\sigma^{2})\\ &=\sum\limits _{i=1}^{N}[log\frac{1}{\sqrt{2\pi}} + \frac{1}{\sigma} - (x_{i}-\mu)^{2}/2\sigma^{2}]
\end{split}
$$

首先对 $$\mu$$ 的极值可以得到 ：

$$
\mu_{MLE}=\mathop{argmax}\limits _{\mu}\log p(X|\theta)=\mathop{argmin}\limits _{\mu}\sum\limits _{i=1}^{N}(x_{i}-\mu)^{2}
$$

于是：

$$
\frac{\partial}{\partial\mu}\sum\limits _{i=1}^{N}(x_{i}-\mu)^{2}=0\longrightarrow\mu_{MLE}=\frac{1}{N}\sum\limits _{i=1}^{N}x_{i}
$$

其次对 $$\theta$$ 中的另一个参数 $$\sigma^2$$ ，有：

$$
\begin{align}
\sigma_{MLE}=\mathop{argmax}\limits _{\sigma}\log p(X|\theta)&=\mathop{argmax}\limits _{\sigma}\sum\limits _{i=1}^{N}[-\log\sigma-\frac{1}{2\sigma^{2}}(x_{i}-\mu)^{2}]\nonumber\\
&=\mathop{argmin}\limits _{\sigma}\sum\limits _{i=1}^{N}[\log\sigma+\frac{1}{2\sigma^{2}}(x_{i}-\mu)^{2}]
\end{align}
$$

于是：

$$
\frac{\partial}{\partial\sigma}\sum\limits _{i=1}^{N}[\log\sigma+\frac{1}{2\sigma^{2}}(x_{i}-\mu)^{2}]=0\longrightarrow\sigma_{MLE}^{2}=\frac{1}{N}\sum\limits _{i=1}^{N}(x_{i}-\mu)^{2}
$$

值得注意的是，上面的推导中，首先对 $$\mu$$ 求 `MLE`， 然后利用这个结果求 $$\sigma_{MLE}$$ ，因此可以预期的是对数据集求期望时 $$\mathbb{E}_{\mathcal{D}}[\mu_{MLE}]$$ 是无偏差的：

$$
\mathbb{E}_{\mathcal{D}}[\mu_{MLE}]=\mathbb{E}_{\mathcal{D}}[\frac{1}{N}\sum\limits _{i=1}^{N}x_{i}]=\frac{1}{N}\sum\limits _{i=1}^{N}\mathbb{E}_{\mathcal{D}}[x_{i}]=\mu
$$

但是当对 $$\sigma_{MLE}$$ __求 期望的时候由于使用了单个数据集的 $$\mu_{MLE}$$ ，因此对所有数据集求期望的时候我们会发现 $$\sigma_{MLE}$$ 是 有偏的：

$$
\begin{align}
\mathbb{E}_{\mathcal{D}}[\sigma_{MLE}^{2}]&=\mathbb{E}_{\mathcal{D}}[\frac{1}{N}\sum\limits _{i=1}^{N}(x_{i}-\mu_{MLE})^{2}]=\mathbb{E}_{\mathcal{D}}[\frac{1}{N}\sum\limits _{i=1}^{N}(x_{i}^{2}-2x_{i}\mu_{MLE}+\mu_{MLE}^{2})\nonumber
\\&=\mathbb{E}_{\mathcal{D}}[\frac{1}{N}\sum\limits _{i=1}^{N}x_{i}^{2}-\mu_{MLE}^{2}]=\mathbb{E}_{\mathcal{D}}[\frac{1}{N}\sum\limits _{i=1}^{N}x_{i}^{2}-\mu^{2}+\mu^{2}-\mu_{MLE}^{2}]\nonumber\\
&= \mathbb{E}_{\mathcal{D}}[\frac{1}{N}\sum\limits _{i=1}^{N}x_{i}^{2}-\mu^{2}]-\mathbb{E}_{\mathcal{D}}[\mu_{MLE}^{2}-\mu^{2}]=\sigma^{2}-(\mathbb{E}_{\mathcal{D}}[\mu_{MLE}^{2}]-\mu^{2})\nonumber\\&=\sigma^{2}-(\mathbb{E}_{\mathcal{D}}[\mu_{MLE}^{2}]-\mathbb{E}_{\mathcal{D}}^{2}[\mu_{MLE}])=\sigma^{2}-Var[\mu_{MLE}]\nonumber\\&=\sigma^{2}-Var[\frac{1}{N}\sum\limits _{i=1}^{N}x_{i}]=\sigma^{2}-\frac{1}{N^{2}}\sum\limits _{i=1}^{N}Var[x_{i}]=\frac{N-1}{N}\sigma^{2}
\end{align}
$$

所以：

$$
\hat{\sigma}^{2}=\frac{1}{N-1}\sum\limits _{i=1}^{N}(x_{i}-\mu_{MLE})^{2}
$$

## ✏ 2、高维情况 `MLE`

多维高斯分布表达式为：

$$
p(x|\mu,\Sigma)=\frac{1}{(2\pi)^{p/2}|\Sigma|^{1/2}}e^{-\frac{1}{2}(x-\mu)^{T}\Sigma^{-1}(x-\mu)}
$$

其中 $$x,\mu\in\mathbb{R}^{p},\Sigma\in\mathbb{R}^{p\times p}$$ ， $$\Sigma$$ 为协方差矩阵，一般而言也是半正定矩阵。这里我们只考虑正定矩阵。首先我们处理指数上的数字，指数上的数字 $$(x-\mu)^{T}\Sigma^{-1}(x-\mu)$$ 可以记为 $$x$$ 和 $$\mu$$ 之间的马氏距离（马氏距离与欧式距离的关系：当方差矩阵为单位矩阵时，马氏距离为欧式距离）。对于对称的协方差矩阵可进行特征值分解：

$$
\Sigma=U\Lambda U^{T}=(u_{1},u_{2},\cdots,u_{p})diag(\lambda_{i})(u_{1},u_{2},\cdots,u_{p})^{T}=\sum\limits _{i=1}^{p}u_{i}\lambda_{i}u_{i}^{T}
$$

于是：

$$
\Sigma^{-1}=\sum\limits _{i=1}^{p}u_{i}\frac{1}{\lambda_{i}}u_{i}^{T}
$$

令 $$y=(y_1, y_2, \ldots, y_p)^T,y_i=(x-\mu)^Tu_i$$ ，则马氏距离 $$\Delta$$ 为：

$$
\Delta=(x-\mu)^{T}\Sigma^{-1}(x-\mu)=\sum\limits _{i=1}^{p}(x-\mu)^{T}u_{i}\frac{1}{\lambda_{i}}u_{i}^{T}(x-\mu)=\sum\limits _{i=1}^{p}\frac{y_{i}^{2}}{\lambda_{i}}
$$

例如：令 $$p=2$$ ，则 $$\Delta = \frac{y_1^2}{\lambda_1} + \frac{y_2^2}{\lambda_2} = r$$ ，（椭圆，如果 $$\lambda_i=c$$ ，则为圆）。 我们注意到 $$y_{i}$$ 是 $$x-\mu$$ 在特征向量 $$u_{i}$$ 上的投影长度，因此上式子就是 $$\Delta$$ 取不同值时的同心椭圆。

![](../../.gitbook/assets/image%20%281%29.png)

### 💎 多维高斯模型在实际应用时的两个问题：

#### 🎾 1、参数 $$\Sigma,\mu$$ 的自由度为 $$O(p^{2})$$ ，对于维度很高的数据其自由度太高。

解决方案：高自由度的来源是 $$\Sigma$$ 有 $$\frac{p(p+1)}{2}$$ 个自由参数，可以假设其是对角矩阵，甚至在各向同性假设中假设其对角线上的元素都相同（椭圆变成圆）。前一种的算法有 Factor Analysis，后一种有概率 `PCA`（ $$p-PCA$$ ）。

#### 🎾 2、第二个问题是单个高斯分布是单峰的，对有多个峰的数据分布不能得到好的结果。解决方案：高斯混合`GMM` 模型。

### 💎 已知联合概率求边缘概率和条件概率

记 $$x=(x1, x_2,\cdots,x_p)^T=(x_{a,m\times 1}, x_{b,n\times1})^T, m+n = p$$ ， $$\mu=(\mu_{a,m\times1}, \mu_{b,n\times1})$$ ， $$\Sigma=\begin{pmatrix}\Sigma_{aa}&\Sigma_{ab}\\ \Sigma_{ba}&\Sigma_{bb}\end{pmatrix}$$ ， $$x\sim\mathcal{N}(\mu,\Sigma)$$ 。求 $$p(x_a),p(x_b|x_a),p(x_b),p(x_a|x_b)$$ ？（配方法）

首先是一个高斯分布的定理：

> 定理：已知 $$x\sim\mathcal{N}(\mu,\Sigma), y\sim Ax+b$$ ，那么 $$y\sim\mathcal{N}(A\mu+b, A\Sigma A^T)$$ 。
>
> 证明： $$\mathbb{E}[y]=\mathbb{E}[Ax+b]=A\mathbb{E}[x]+b=A\mu+b$$ ， $$Var[y]=Var[Ax+b]=Var[Ax]=A\cdot Var[x]\cdot A^T$$ 。

1、 $$x_a=\begin{pmatrix}\mathbb{I}_{m\times m}&\mathbb{O}_{m\times n})\end{pmatrix}\begin{pmatrix}x_a\\x_b\end{pmatrix}$$ ，代入定理中得到：

$$
\mathbb{E}[x_a]=\begin{pmatrix}\mathbb{I}&\mathbb{O}\end{pmatrix}\begin{pmatrix}\mu_a\\\mu_b\end{pmatrix}=\mu_a\\
Var[x_a]=\begin{pmatrix}\mathbb{I}&\mathbb{O}\end{pmatrix}\begin{pmatrix}\Sigma_{aa}&\Sigma_{ab}\\\Sigma_{ba}&\Sigma_{bb}\end{pmatrix}\begin{pmatrix}\mathbb{I}\\\mathbb{O}\end{pmatrix}=\Sigma_{aa}
$$

所以 $$x_a\sim\mathcal{N}(\mu_a,\Sigma_{aa})$$ 。

2、同样的， $$x_b\sim\mathcal{N}(\mu_b,\Sigma_{bb})$$ 。

3、对于两个条件概率，我们引入三个量：

$$
x_{b\cdot a}=x_b-\Sigma_{ba}\Sigma_{aa}^{-1}x_a\\
    \mu_{b\cdot a}=\mu_b-\Sigma_{ba}\Sigma_{aa}^{-1}\mu_a\\
    \Sigma_{bb\cdot a}=\Sigma_{bb}-\Sigma_{ba}\Sigma_{aa}^{-1}\Sigma_{ab}
$$

特别的，最后一个式子叫做 $$\Sigma_{aa}$$ 的 `Schur Complementary`。可以看到：

$$
x_{b\cdot a}=\begin{pmatrix}-\Sigma_{ba}\Sigma_{aa}^{-1}&\mathbb{I}_{n\times n}\end{pmatrix}\begin{pmatrix}x_a\\x_b\end{pmatrix}
$$

所以：

$$
\mathbb{E}[x_{b\cdot a}]=\begin{pmatrix}-\Sigma_{ba}\Sigma_{aa}^{-1}&\mathbb{I}_{n\times n}\end{pmatrix}\begin{pmatrix}\mu_a\\\mu_b\end{pmatrix}=\mu_{b\cdot a}\\
    Var[x_{b\cdot a}]=\begin{pmatrix}-\Sigma_{ba}\Sigma_{aa}^{-1}&\mathbb{I}_{n\times n}\end{pmatrix}\begin{pmatrix}\Sigma_{aa}&\Sigma_{ab}\\\Sigma_{ba}&\Sigma_{bb}\end{pmatrix}\begin{pmatrix}-\Sigma_{aa}^{-1}\Sigma_{ba}^T\\\mathbb{I}_{n\times n}\end{pmatrix}=\Sigma_{bb\cdot a}
$$

利用这三个量可以得到 $$x_b=x_{b\cdot a}+\Sigma_{ba}\Sigma_{aa}^{-1}x_a$$ 。因此：

$$
\mathbb{E}[x_b|x_a]=\mu_{b\cdot a}+\Sigma_{ba}\Sigma_{aa}^{-1}x_a \\
Var[x_b|x_a]=\Sigma_{bb\cdot a}
$$

这里同样用到了定理。

4、同样：

$$
x_{a\cdot b}=x_a-\Sigma_{ab}\Sigma_{bb}^{-1}x_b\\
    \mu_{a\cdot b}=\mu_a-\Sigma_{ab}\Sigma_{bb}^{-1}\mu_b\\
    \Sigma_{aa\cdot b}=\Sigma_{aa}-\Sigma_{ab}\Sigma_{bb}^{-1}\Sigma_{ba}
$$

所以：

$$
\mathbb{E}[x_a|x_b]=\mu_{a\cdot b}+\Sigma_{ab}\Sigma_{bb}^{-1}x_b \\
Var[x_a|x_b]=\Sigma_{aa\cdot b}
$$

### 💎 已知边缘概率和条件概率求联合概率

已知 $$p(x)=\mathcal{N}(\mu,\Lambda^{-1}),p(y|x)=\mathcal{N}(Ax+b,L^{-1})$$ ，求解 $$p(y),p(x|y)$$ ？ 

贝叶斯定理：$$p(x|y)=\frac{p(y|x)p(x)}{p(y)}$$ ，且均值看起来有一个假设 $$y=Ax+B$$ 。

