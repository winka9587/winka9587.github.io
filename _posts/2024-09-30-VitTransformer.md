---
title: VitT in MASt3r
date: 2024-09-30 10:51
category: Net
author: winka9587
tags: [Net]
summary: Summary of the article ViT in MASt3r
math: true
---

[toc]

MASt3r是如何应用ViT的，如果要拓展到其他工作, 应该如何使用它。

参考论文: An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale

参考代码: https://github.com/huggingface/pytorch-image-models/blob/main/timm/models/vision_transformer.py

![](/assets/img/2024-09-30-15-37-06.png)

![](/assets/img/vit.gif)

## 输入

ViT是由逐像素、2×2patch + self-attn发展演变而来的, 其初衷是为了匹配CNN。

transformer能够接受的输入是1D令牌形式

如图中所示: patch+position 

输入图像-> 固定大小的patch, 计算图像的embedding和position embedding, 将结果的作为vector的序列输入到Transformer Encoder中, 图中为了执行分类任务, 还添加了额外的可学习的"classification token".

输入的图像$x \in \R^{(H, W, C)}$, 分割为N个patch(代码中有缩放和裁剪等预处理操作, 保证图像的宽高能够被patch的尺寸整除), 图像被切分为$N$个patch, 表示为$x_p \in \R^{(N×(P^2 \cdot C))}$, $N=HW/P^2$

在dust3r/croco/models/blocks.py中有一个PatchEmb类, 其初始化使用了4个参数:
~~~
img_size: (512, 512)
patch_size: 16
in_channel: 3
enc_embed_dim: 1024
~~~

其中会计算新的属性:
~~~
self.grid_size = (img_size[0] // patch_size[0], img_size[1] // patch_size[1])
self.num_patches = self.grid_size[0] * self.grid_size[1]
~~~

其中grid_size其实就是计算H和W能被当前的patch分割为几份$(N_y, N_x)$, 并计算patch的数量num_patches也就是$N$

这个类还附带一个proj, 是一个nn.Conv2d, 输入维度为in_chans=3, 输出维度为embed_dim=1024, kernel_size=patch_size, 步长stride=patch_size. 所以这个proj其实就是对图像进行embed的操作。

norm则是一个归一化层, 负责对proj输出的结果进行归一化。如果初始化时指定了norm_layer则使用该layer, 如果没有指定则使用nn.Identity, 原样返回输入。