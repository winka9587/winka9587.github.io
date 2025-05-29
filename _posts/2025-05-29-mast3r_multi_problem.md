---
title: 关于mast3r, dust3r, global alignment的一些理解 
date: 2025-05-29 16:49
category: 
author: 
tags: []
summary: 
---

最近在kaggle上使用mast3r作为baseline, 遇到了一些disk溢出的问题

在sparse_global_alignment阶段, 会将所有的图像和图像对输入到forward_mast3r，计算对应关系，并将计算的结果缓存到cache目录下，之后的prepare_canonical_data阶段中会读取这些数据来归一化pointmap。

而缓存这一阶段，得到的误差就很大。