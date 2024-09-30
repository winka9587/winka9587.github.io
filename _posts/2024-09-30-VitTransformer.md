---
title: VitT in MASt3r
date: 2024-09-30 10:51
category: Net
author: User
tags: [Net]
summary: Summary of the article ViT in MASt3r
math: true
---

[toc]

MASt3r是如何应用ViT的，如果要拓展到其他工作, 应该如何使用它。

参考论文: An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale

参考代码: https://github.com/huggingface/pytorch-image-models/blob/main/timm/models/vision_transformer.py

![](/assets/img/2024-09-30-15-37-06.png)

## 输入

ViT是由逐像素、2×2patch + self-attn发展演变而来的, 其初衷是为了匹配CNN。

transformer能够接受的输入是1D令牌形式

如图中所示: patch+position 

输入图像-> 固定大小的patch, 计算图像的embedding和position embedding, 将结果的作为vector的序列输入到Transformer Encoder中, 图中为了执行分类任务, 还添加了额外的可学习的"classification token".

在dust3r/croco/models/blocks.py中有一个PatchEmb类,  
