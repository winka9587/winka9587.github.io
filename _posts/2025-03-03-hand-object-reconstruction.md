---
title: 手持物体重建相关工作汇总(截止至2025.3)
date: 2025-03-03 10:11
category: 
author: 
tags: []
summary: 
---

## Related Work

### 分类

| Branch | Name | From | Input | Output |
|--------|------|------|-------|--------|
| Hand-object 3D recon, object-agnostic | HOSt3R | arXiv 2025 | two RGB image| mask, **pointcloud (hand & obj)**|
| Hand-held Objects Reconstruction | HORT | arXiv 2025 | single RGB image| mask, **pointcloud(obj), mesh(hand)**|
| Hand-object 3D recon, category-agnostic | HOLD | CVPR 2024 | RGB images | mesh |

#### HOSt3R

现有的**RGB序列**重建主要是两阶段pipeline: 

1.hand-object 3D tracking
2.multi-view 3D reconstruction

现有的方法依赖关键点检测技术, SfM和hand-keypoint optimization. 对于不同模型形状、弱纹理和hand-object互遮挡情况难以处理

#### HORT

使用coarse-to-fine策略, 从图像中生成稀疏点云并refine为pixel-aligned image feature. 使用图像特征和3D手几何特征预测物体的点云和相对于手的pose. 

#### HOLD

## 可能会用到相关代码

Zhe Cao, Ilija Radosavovic, Angjoo Kanazawa, and Jitendra Malik. Reconstructing hand-object interactions in the wild. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 12417–12426, 2021. 2, 4
Yana Hasson, G ̈ul Varol, Cordelia Schmid, and Ivan Laptev. Towards unconstrained joint hand-object reconstruction from rgb videos. In 2021 International Conference on 3D Vision (3DV), pages 659–668. IEEE, 2021. 4

Hand-held Object Reconstruction from RGB Video with Dynamic Interaction中提到这两个里面有代码，渲染物体为mask。
> 3.2: Previous works [5, 15] render 2D mask Mrender from the 3D mesh and compare it with the input object mask M for optimization:


汇总近几年的相关工作. 

## 重新梳理

如果按prior/template来进行分类, 多数工作假设能够获得交互物体的预扫描模板。这就导致难以泛化（因为实际场景中对每一个物体都扫描是困难的）

而无需先验的方法如果训练数据较少，泛化性能依然不够。[Ye et al.](#diffusion-guided) 在6个物体类别的数据集上进行了训练，并使用该训练的先验来重建hand&object，受到训练数据的限制。

还有一组方法使用单目视频来进行in-hand object scanning, 使用多视角重建技术来整合不同视角下的观察结果。[CVPR23](#in-hand-scanning)， [SIGGRAPH](#Zhou2022)(相机固定不动)，[Color-NeuS](#Color-NeuS). HOLD中提到这些方法不考虑手的关节（因为在输入的单目视频中手并非刚体，无法参与多视角重建环节）

## 论文

### 单目扫描(monocular video scanning)

#### (CVPR 2023) In-hand 3d object scanning from an rgb sequence.{#in-hand-scanning}

#### (SIGGRAPH Asia 2022) Reconstructing hand-held objects from monocular video.{#Zhou2022}

#### (3DV 2024) Color-NeuS: Reconstructing neural implicit surfaces with color. {#Color-Neus}

### template-based recon

#### Diffusion-guided reconstruction of everyday handobject interaction clips.{#diffusion-guided}




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

该方法利用注意力引导的图神经网络进行手部和物体的联合重建。

#### Metrics

测试的指标包括: 形状重建精度 (Chamfer Distance, CD)，F-score，手部姿态误差 (MPJPE)。

#### 数据集

HO3D, DexYCB

#### 对比方法

ObMan, iHOI, Graph-based methods

---


## 其他的/或许相关工作

### (WACV 2025) DN-Splatter: Depth and Normal Priors for Gaussian Splatting and Meshing [[page](https://maturk.github.io/dn-splatter/)]

引入深度和法向先验来得到更好的3dgs与meshing

![](/assets/img/2025-03-08-15-50-14.png)


### (CVPR 2025) https://github.com/facebookresearch/fast3r [[page](https://github.com/facebookresearch/fast3r)]

![](/assets/img/2025-03-08-16-28-22.png)

添加了全局fusion来加速原本的匹配过程。

### (arXiv 2024) [Spann3R] 3D Reconstruction with Spatial Memory [[page](https://hengyiwang.github.io/projects/spanner)]

![](/assets/img/2025-03-08-17-18-15.png)

