# 线性回归

首先确定一组（ $$N$$ 个） $$x$$ 和 $$y$$ 的观测值，以 $$(x_1 ,y_1 ), (x_2 ,y_2 ), ..., (x_N ,y_N); x_i\in R^p ; y\in R$$ 形式给出。 $$X=(x_1, x_2, \cdots, x_N)^T_{N\times p}=\begin{pmatrix}  x_{11} & x_{12} & \cdots & x_{1p} \\  x_{21} & x_{22} & \cdots & x_{2p} \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ x_{N1} & x_{N2} & \cdots & x_{Np} \end{pmatrix} _{N\times p}$$ ， $$Y=\begin{pmatrix}  y_{1} \\  y_{2} \\ \vdots \\ y_{N}  \end{pmatrix} _{N\times 1}$$ 

线性回归假设目标值与特征之间线性相关，即满足一个多元一次方程。通过构建损失函数，来求解损失函数最小时的参数 $$w$$ 和 $$b$$ 。通长我们可以表达成如下公式：![](https://img2018.cnblogs.com/blog/856725/201903/856725-20190303225317608-462127381.png)

$$
\hat{y}=w^Tx+b
$$

$$\hat{y}$$ 为预测值，自变量`x`和因变量`y`是已知的，而我们想实现的是预测新增一个`x`，其对应的`y`是多少。因此，为了构建这个函数关系，目标是通过已知数据点，求解线性模型中`w`和`b`两个参数。

## ✏ 1、最小二乘法

### 🖋 1.1、矩阵表达

求解最佳参数，需要一个标准来对结果进行衡量，为此我们需要定量化一个目标函数式，使得计算机可以在求解过程中不断地优化。对这个问题，采用二范数定义的平方误差来定义损失函数：

$$
L(w)=\sum\limits_{i=1}^N||w^Tx_i-y_i||^2_2=\sum\limits_{i=1}^N(w^Tx_i-y_i)^2
$$

展开得到：

$$
\begin{align}
L(w)&=(w^Tx_1-y_1,\cdots,w^Tx_N-y_N)\cdot (w^Tx_1-y_1,\cdots,w^Tx_N-y_N)^T\nonumber\\
&=(w^TX^T-Y^T)\cdot (Xw-Y)=w^TX^TXw-Y^TXw-w^TX^TY+Y^TY\nonumber\\
&=w^TX^TXw-2w^TX^TY+Y^TY
\end{align}
$$

最小化这个值的 $$\hat{w}$$ ：

$$
\begin{align}
\hat{w}=\mathop{argmin}\limits_wL(w)&\longrightarrow\frac{\partial}{\partial w}L(w)=0\nonumber\\
&\longrightarrow2X^TX\hat{w}-2X^TY=0\nonumber\\
&\longrightarrow \hat{w}=(X^TX)^{-1}X^TY=X^TY
\end{align}
$$

### 🖋 1.2、几何意义

### 🖋 1.3、概率视角

## ✏ 2、正则化

