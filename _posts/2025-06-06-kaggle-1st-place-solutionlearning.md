---
title: kaggle-imc25 n-st Place Solution
date: 2025-06-06 19:16
category: 
author: 
tags: []
summary: 
math: true
image:
    path: /assets/img/2025-06-06-19-17-16.png
---

>
> 像站在大门前倏忽了一瞬间
> 


https://www.kaggle.com/competitions/image-matching-challenge-2025/discussion/583058

自己的知识储备和工程能力远远不够，导致无法驾驭现有的工具和方法。

作者承诺会公开代码，等待学习...


---

## 值得注意的点

### Rotation correction

MASt3R的特征提取, 不需要像ALIKED等特征提取器一样，对图像进行旋转增强/过滤

![](/assets/img/2025-06-08-19-05-16.png)

### keypoints being concentrated in only one part of the image

关键点集中于图像某一部分

https://www.kaggle.com/competitions/image-matching-challenge-2025/discussion/583401
和另外一篇discussion中都有提到这个问题。

3rd solution提出的tiled images策略很有意思。

### 4rd RDD

RDD: Robust Feature Detector and Descriptor using Deformable Transformer

有时间读一下这个

https://xtcpete.github.io/rdd/

https://www.kaggle.com/competitions/image-matching-challenge-2025/discussion/582959

## pair-match

https://arxiv.org/abs/2504.20040

>
> In addition to the semi-dense matches, keypoints extracted by other keypoint detectors are also fed to the MASt3R matcher (Inspired by MP-SfM https://arxiv.org/abs/2504.20040). These additional keypoints might regionally overlap with points subsampled by MASt3R, but this approach improved the score compared to using only MASt3R matches.
> 

MASt3R subsample + ALIKED detector + SuperPoint detector