---
title: 手持物体重建相关工作汇总(截止至2025.3)
date: 2025-03-03 10:11
category: 
author: 
tags: []
summary: 
---

汇总近几年的相关工作. 

### (CVPR 2024) HOLD: Category-agnostic 3D Reconstruction of Interacting Hands and Objects from Video[[page](https://zc-alexfan.github.io/hold)]


> _Zicong Fan1,2 Maria Parelli1 Maria Eleni Kadoglou1 Muhammed Kocabas1,2 Xu Chen1,2,† Michael J. Black2 Otmar Hilliges1_<br>
> _1ETH Z ̈ urich, Switzerland 2Max Planck Institute for Intelligent Systems, T ̈ ubingen, Germany_


![](/assets/img/2025-03-03-10-16-52.png)

手持物体重建仅是其实验的一部分，

![](/assets/img/2025-03-03-10-18-02.png)

#### metrics

测试的指标包括: 手部姿态准确度(**MPJPE**, root-relative mean-per-joint error), 物体姿态和形状的准确度(**CD**, Chamfer Distance), F-score(**F10**, 10mm阈值以下被认为正确)和物体的手相对倒角距离($\textbf{CD}_{h}$, hand-relative Chamfer distance for the object)

#### 数据集

HO3D(对比方法iHOI在训练过程中使用了测试物体的3D标注)

#### 对比方法

iHOI, DiffHOI

### (3DV 2024) Color-NeuS: Reconstructing Neural Implicit Surfaces with Color[[page](https://zlicheng.com/color_neus/)]

> _Licheng Zhong1 ⋆ Lixin Yang1,2 ⋆ Kailin Li1 Haoyu Zhen1 Mei Han3 Cewu Lu1,2 †_<br>
> _1Shanghai Jiao Tong University 2Shanghai Qi Zhi Institute 3National University of Singapore_

![](/assets/img/2025-03-03-11-07-41.png)

#### 测试指标



#### 数据集

### (CVPR 2023) In-Hand 3D Object Scanning from an RGB Sequence[[page](https://rgbinhandscanning.github.io/)]

> _Shreyas Hampali1,3, Tomas Hodan1, Luan Tran1, Lingni Ma1, Cem Keskin1, Vincent Lepetit2,3_ <br>
> _1Reality Labs at Meta, 2LIGM, Ecole des Ponts, Univ Gustave Eiffel, CNRS, Marne-la-Vallee, France, 3Institute for Computer Graphics and Vision, Graz University of Technology, Graz, Austria_

![](/assets/img/2025-03-03-16-08-20.png)

将连续的RGB序列拆分成不同的片段, 并确保片段之间有重合。



YCB, Aria

对于香蕉、剪刀这种“薄”+“弱纹理”特点的物体，重建容易失败。

### (ICCV 2023) CHORD: Category-level Hand-held Object Reconstruction via Shape Deformation [[page](https://kailinli.github.io/CHORD/)]

> _Kailin Li, Zhewei Huang, Chen Wang, Zhiyuan Wang, Juyong Zhang_<br>
> _University of Science and Technology of China, Shanghai AI Lab_

![](/assets/img/2025-03-03-14-43-29.png)

CHORD 方法提出了一种基于类别形状先验的变形重建方法。

#### Metrics

测试的指标包括: 形状重建精度 (**Chamfer Distance, CD**), 形状完整性 (**F-score**)。

#### 数据集

COMIC (本文新构建的数据集) 和 HO3D。

#### 对比方法

iHOI, ObMan, NDF, DDF-HO

### (SIGGRAPH Asia 2022) Reconstructing Hand-Held Objects from Monocular Video[[page](https://dihuangdh.github.io/hhor/)]

![](/assets/img/2025-03-03-10-33-38.png)

1. RGB video作为输入
2. **相机固定不动**, 需要借助背景图像来分割手和物体，再使用hand segmentation进一步将手分割出来。

#### 测试指标

Chamfer Distance

![](/assets/img/2025-03-03-10-35-20.png)

#### 数据集

自建数据集HOD, 35个物体

#### 对比方法

ObMan, GF, IHOI

![](/assets/img/2025-03-03-10-48-53.png)

### (CVPR 2022) What's in your hands? 3D Reconstruction of Generic Objects in Hands.[[page](https://judyye.github.io/ihoi/)]


> _Wentian Qu1,2 Zhaopeng Cui3 Yinda Zhang4 Chenyu Meng1,2 Cuixia Ma1,2
Xiaoming Deng1,2* Hongan Wang1,2∗_<br>
> _1Institute of Software, Chinese Academy of Sciences 2University of Chinese Academy of Sciences 3State Key Lab of CAD&CG, Zhejiang University 4Google_

![](/assets/img/2025-03-03-10-51-43.png)


### (ICCV 2023) Novel-view Synthesis and Pose Estimation for Hand-Object Interaction from Sparse Views

![](/assets/img/2025-03-03-10-15-35.png)

稀疏视角重建

### (2021 3DV)Towards Unconstrained Joint Hand-Object Reconstruction From RGB Videos [[page](https://hassony2.github.io/homan.html)]

![](/assets/img/2025-03-03-10-13-01.png)

### () 
### () 
### () 
### () 

---


---

### (CVPR 2022) What’s in Your Hands? 3D Reconstruction of Generic Objects in Hands [[page](https://github.com/yufeiy2/whats-in-your-hands)]

> _Yufei Ye, Shubham Tulsiani, Abhinav Gupta_<br>
> _Carnegie Mellon University_

![](/assets/img/2025-03-03-10-25-42.png)

该方法利用手部姿态作为条件约束，从单帧 RGB 图像中重建物体。

#### Metrics

测试的指标包括: 形状重建精度 (**Chamfer Distance, CD**) 和 F-score。

#### 数据集

HO3D, DexYCB

#### 对比方法

ObMan, iHOI, SDF-based methods

---

### (ICCV 2023) HO-NeRF: Novel-view Synthesis and Pose Estimation for Hand-Object Interaction from Sparse Views [[page](https://github.com/wentianqu/HO-NeRF)]

> _Wentian Qu, Jiarui Xu, Animesh Garg_<br>
> _University of Toronto_

![](/assets/img/2025-03-03-10-30-15.png)

HO-NeRF 使用 NeRF 进行新视角合成，并优化手部和物体的位姿。

#### Metrics

测试的指标包括: 视角合成质量 (PSNR, SSIM, LPIPS)，姿态估计误差 (MPJPE, CD)。

#### 数据集

DexYCB, ObMan

#### 对比方法

iHOI, DiffHOI, NeRF-baselines

---

### (CVPR 2022) Collaborative Learning for Hand and Object Reconstruction with Attention-Guided Graph Convolution [[page](https://github.com/tsehoeldentse/hand-object-reconstruction)]

> _Tze Ho Elden Tse, Zhimin Chen, Antonio Garcia-Uceda, Gregory Rogez, Edmond Boyer, Helge Rhodin_<br>
> _INRIA, University of British Columbia_

![](/assets/img/2025-03-03-10-40-20.png)

该方法利用注意力引导的图神经网络进行手部和物体的联合重建。

#### Metrics

测试的指标包括: 形状重建精度 (Chamfer Distance, CD)，F-score，手部姿态误差 (MPJPE)。

#### 数据集

HO3D, DexYCB

#### 对比方法

ObMan, iHOI, Graph-based methods

