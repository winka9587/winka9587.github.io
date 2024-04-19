---
title: NOCS:类级别任务与数据集
date: 2024-04-17 14:49
category: Category-level
author: winka9587
tags: [category-level, dataset]
summary: Summary of the article
math: true
---

## 在前面

Normalized object coordinate space of category level 6D object pose and size estimation

本文是CVPR 2019 oral，最核心的部分就是提出了NOCS这个概念以及NOCS数据集，目前看到的类级别相关的检测和跟踪工作都是在这个数据集上跑的（后来也出现了如Wild6d这种数据集, 之后也会分析）。因此了解NOCS的概念以及了解NOCS数据集的结构都是很重要的。

对照着NOCS和CAPTRA的源码中数据处理部分能理解个差不多。

数据集大致结构如图：D

放点可视化结果：