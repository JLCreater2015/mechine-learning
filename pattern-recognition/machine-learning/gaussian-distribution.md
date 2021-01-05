# 高斯分布

数据 $$X=(x_1, x_2, \cdots, x_N)^T_{N\times p}=\begin{pmatrix}  x_{11} & x_{12} & \cdots & x_{1p} \\  x_{21} & x_{22} & \cdots & x_{2p} \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ x_{N1} & x_{N2} & \cdots & x_{Np} \end{pmatrix} _{N\times p}$$ 有 $$N$$ 个样本，每个样本的维度为 $$p$$ ， 即 $$x_i \in R^p$$ ， $$x_i \  \mathop{\sim}\limits^{iid} \  N(\mu, \sum)$$ （iid：独立同分布）。

## ✏ 1、一维情况 `MLE`

令 $$\theta=(\mu,\Sigma)=(\mu,\sigma^{2})$$ ，在 `MLE` 方法中：

$$
\theta_{MLE}=\mathop{argmax}\limits _{\theta}\log p(X|\theta)\\
\log p(X|\theta)\mathop{=}\limits _{iid} log\prod\limits_{i = 1}^{N}p(x_{i}|\theta) = \sum\limits _{i=1}^{N}\log p(x_{i}|\theta)
$$

一般地，高斯分布的概率密度函数PDF写为：

$$
p(x|\mu,\Sigma)=\frac{1}{(2\pi)^{p/2}|\Sigma|^{1/2}}e^{-\frac{1}{2}(x-\mu)^{T}\Sigma^{-1}(x-\mu)}
$$

带入 `MLE` 中我们考虑一维的情况，即 $$p=1$$ ：

$$
\begin{split} \log p(X|\theta)=\sum\limits _{i=1}^{N}\log p(x_{i}|\theta) &=\sum\limits _{i=1}^{N}\log\frac{1}{\sqrt{2\pi}\sigma}\exp(-(x_{i}-\mu)^{2}/2\sigma^{2})\\ &=\sum\limits _{i=1}^{N}[log\frac{1}{\sqrt{2\pi}} + \frac{1}{\sigma} - (x_{i}-\mu)^{2}/2\sigma^{2}]
\end{split}
$$



