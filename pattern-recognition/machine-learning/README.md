# 机器学习

## ✏ 频率派 & 贝叶斯派

假设数据 $$X=(x_1, x_2, \cdots, x_N)^T_{N\times p}=\begin{pmatrix}  x_{11} & x_{12} & \cdots & x_{1P} \\  x_{21} & x_{22} & \cdots & x_{2P} \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ x_{N1} & x_{N2} & \cdots & x_{Np} \end{pmatrix} _{N\times p}$$ 有 $$N$$ 个样本，每个样本的维度为 $$p$$ ， 参数用为 $$\theta$$ 表示， $$x$$ 服从分布： $$x\sim p(x|\theta)$$ 。

### 频率派——统计机器学习

频率派认为参数 $$\theta$$ 为未知常量， $$X$$ 是已知的随机变量（r.v.），对于 $$N$$ 个观测来说，观测集的概率为 $$p(X|\theta)\ \underset{=}{iid}\ \prod_\limits{i=1}^{N}p(x_{i}|\theta))$$ 。采用最大对数似然_`MLE`_的方法，通过 $$X$$ 估计出参数 $$\theta$$ ： 

$$
\theta_{MLE} = arg max_{\theta}\  log\ P(X | \theta)
$$

 $$log\ p(X | \theta)$$ 记作 $$\mathcal{L}(\theta)$$ ，其中 $$x_i \ \underset{\sim}{iid} \ \  p(x|\theta)$$ ，则有 $$log P(X|\theta) = \sum\limits^N_{i=1}p(x_i|\theta)$$ 。

### 贝叶斯派——概率图模型

贝叶斯派认为参数 $$\theta$$ 本身也是一个随机变量，服从一定的分布，即 $$\theta \sim p(\theta)$$ ，称为先验。借助贝叶斯定理将参数的先验和后验（ $$P(\theta | X)$$ ）通过似然函数联系起来：

$$
P(\theta | X) = \frac{P(X|\theta)\cdot P(\theta)}{P(X)}=\int_{\theta} P(X|\theta)\cdot P(\theta) d\theta \  \propto \ P(X|\theta)\cdot P(\theta)
$$

MAP：为了求 $$\theta$$ 的值，我们要最大后验概率估计，找出让后验概率最大的点（众数）作为代替，则有： 

$$
\theta_{MAP}= arg max_{\theta} P(\theta | X) = arg max_{\theta} P(X|\theta)\cdot P(\theta)
$$

其中第二个等号是因为分母和 $$\theta$$ 没有关系。求解这个 $$\theta$$值后计算 $$\frac{p(X|\theta)\cdot p(\theta)}{\int_{\theta}p(X|\theta)\cdot p(\theta)d\theta}$$ ，就得到了参数的后验概率。其中 $$p(X|\theta)$$ 叫似然，是我们的模型分布。得到了参数的后验分布后，我们可以将这个分布用于预测贝叶斯预测，已知 $$X$$ ，新样本 $$\tilde{x}$$ ，则 ：

$$
p(\tilde{x} | X) = \int_{\theta}p(\tilde{x},\theta | X)d\theta = \int_{\theta}p(\tilde{x}| \theta)\cdot p(\theta | X)d\theta
$$

其中积分中的被乘数是模型，乘数是后验分布。

严格来说，MAP并不是严格的贝叶斯估计，贝叶斯估计：

$$
P(\theta | X) = \frac{P(X|\theta)\cdot P(\theta)}{\int_{\theta}p(x | \theta)\cdot p(\theta)d\theta}
$$

**总结：从贝叶斯的角度来看，机器学习的本质就是求积分的问题，从频率的角度来看，机器学习的本质是优化问题，相当于是解`loss function`。**

## ✏ 学习资料

### 书籍：

1、《统计机器学习》- 李航：感K朴决逻，支提E隐条。（频率派）

2、西瓜书 - 《机器学习》- 周志华

3、`PRML` - 《Pattern Recognition and Machine Learning》，中文译名《模式识别与机器学习》：回分神核稀，图混近采连。（贝叶斯派）

4、`MLAPP` - 《Machine Learning-A Probabilistic Perspective》（贝叶斯派）

5、ESL - 《 The Elements of Statistical Learning》（频率派）

6、花书 - 《Deep Learning》，有中译版

### 视频：

1、台大林轩田老师的《机器学习基石》和《机器学习技法》

2、张志华：《机器学习导论》（频率派）和《统计机器学习》（贝叶斯派）

3、徐亦达：《概率模型》（Github上的notes）

4、台大李宏毅《机器学习》（基础和高阶）

