---
title: 
date: 2025-03-10 19:27
category: 
author: 
tags: []
summary: 
---

## 手撕MHA

### 我们需要什么

首先, 点积注意力(Scaled Dot-Product Attention)是注意力机制所需的最小单元，你也可以叫他(自注意力)

### 准备QKV

首先我们需要将输入的tensor映射到Query, Key和Value三个向量空间中去。

~~~
self.W_q = nn.Linear(embed_dim, embed_dim)
self.W_k = nn.Linear(embed_dim, embed_dim)
self.W_v = nn.Linear(embed_dim, embed_dim)
~~~

这三个线性层不会改变[B, n, embed_dim]的维度, 在同维度的空间进行映射。

### 拆分多头

拆分多头进行注意力的并行计算。