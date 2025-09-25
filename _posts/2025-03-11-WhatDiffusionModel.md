---
title: diffusion model是如何工作的
date: 2025-03-11 14:43
category: 
author: winka9587
tags: [diffusion_model]
summary: 
math: true
---

本文是对diffusion的入门级学习记录。

**现在只考虑最简单的情况**

学习资料来自:

[How diffusion models work: the math from scratch](https://theaisummer.com/diffusion-models)

[apxml](https://apxml.com/zh/courses/intro-diffusion-models/chapter-1-generative-modeling-fundamentals/probabilistic-framework-intro)

GaussianObject: High-Quality 3D Object Reconstruction from Four Views with Gaussian Splatting

## base idea of diffusion model

扩散模型的基础思想非常简单，输入一张图像$x_0$并在T步操作下逐渐add高斯噪声，这一过程被称为forward process（与神经网络中的forward并不是一回事）

forward是为了生成训练数据, 如果你要恢复一幅被损坏的画，那么你有一幅完整的画，只需要不断损坏它（这就是这T步在做的事情），然后将这T步每一步的结果作为输入，最初完整的画作为gt监督即可。

因此，只需要将加噪的过程逆向过来，就是生成的过程。这个从噪声图像恢复原始图像$x_0$的过程被称为**reverse diffusion process**(反向扩散过程)或**sampling process**(采样过程)

**Q:为什么被称为sampling process？**

**因为噪声→图像的过程并非是确定性的去噪，而是从模型的分布中生成一个样本(sample)**

DDPM的一个标准去噪step是

$$
x_{t-1} = \mu_\theta(x_t, t) + \sigma_t z_t, 
$$

$z_t$是随机噪声(有的公式也会直接写$z$)，正是$z$的加入为这个过程引入了随机性，使得同样的输入$x_t$会得到不同的$x_{t-1}$

$$
z_t \sim \mathcal{N}(0, I)
$$

因此完全可以说: **从$x_t$采样得到$x_{t-1}$**

$x_{t-1} \sim \mathcal{N}(\mu_\theta(x_t, t), \sigma^2_tI)$

**Q: 网络估计什么？**

多数DDPM方法的输出是一个与$x_t$形状相同的张量，用来表示当前步的去噪方向，等价参数化的方法有很多种，这里先说一下预测噪声的。

网络估计噪声$\hat\epsilon=\epsilon_\theta(x_t, t)$

$$
\mu_\theta(x_t, t) = \frac{1}{\sqrt{\alpha_t}}(x_t - \frac{\beta_t}{\sqrt{1-\bar\alpha_t}}\hat\epsilon)
$$

其中的$\alpha_t, \beta_t, \bar\alpha_t$都是噪声调度参数。

## forward

关于前向过程,

$$
x_0 \sim q(x)
$$

**可以说$x_0$服从$q(x)$分布，也可以说$x_0$是从分布$q(x)$中采样得到的**。这两种说法是等价的, 只是强调/侧重的点不同。服从分布是在从概率角度描述随机变量性质，强调取值模式。而从分布采样，则是在强调获取样本的过程。

在标准的DDPM(Denoising Diffusion Probabilistic Models)中，

根据公式


均值$x_t$是上一时刻的缩放版本$\sqrt{1-\beta_t}x_{t-1}$

协方差矩阵则是由噪声控制的($\beta_t$是一个标量, **现在只考虑最简单的情况**)

$$
\Sigma_t=\beta_t I
$$

---

我们得到正态分布

$$
q(x_t|x_{t-1}) = \textbf{\( \mathcal{N}(\mathbf{x}; \mu_t, \Sigma_t)\)}
$$

$\textbf{\( \mathcal{N}(\mathbf{x}; \mu_t, \Sigma_t)\)}$ 和 $\textbf{\(\mathbf{x} \sim \mathcal{N}(\mu, \Sigma)\)}$ 是等价的。

具体来说：

$$
\mathcal{N}(\mathbf{x}; \mu, \Sigma) = 
\frac{1}{\sqrt{(2\pi)^d |\Sigma|}} 
\exp \left( -\frac{1}{2} (\mathbf{x} - \mu)^T \Sigma^{-1} (\mathbf{x} - \mu) \right)
$$

$q(x_t|x_{t-1})$依然是一个正态分布，从输入的$x_0$一直到$x_T$, 只需要连乘就能得到最终的分布

最终的分布$x_T$服从标准正态分布（从标准正态分布中的采样），是纯粹的噪声。

$$x_T \sim \mathcal{N}(0, I)$$

### Reparameterization (重参数化)

同样的名词我们在学习3dgs的协方差投影中也遇见过，当时是为了处理协方差在非线性变换后不满足半正定问题，因而将其分解。
