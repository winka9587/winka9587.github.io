---
title: VLA 入门记录
date: 2025-09-18 15:22
category: 
author: 
tags: []
summary: just make a record
---

记录自己从0入门VLA(Vision-Language-Action)

**只是记录而非教程**

| 图标 | 说明 |
|----------|----------|
| :white_check_mark: | **基本完成** 不排除有新增的可能 |
| :black_square_button: | **尚未完成** |
| :heavy_check_mark: | 可预见的未来 **不需要进行任何修改** |
| :construction: | 近期（1周内）**正在做** |
| :warning: | **有问题没解决** |

## Read Paper

|Paper|Time|finished your reading|Note|数据格式| 
|---|---|---|---|---|
|pi0|2024|:construction:||Lerobot 2.0 ([ALOHA等都是转换后的](https://github.com/Physical-Intelligence/openpi/tree/main/examples/aloha_real#training-on-your-own-aloha-dataset))|
|pi0.5||:black_square_button:|(其中有关于VLA中的L部分的分析)
|OpenVLA||:black_square_button:
|RDT||:black_square_button:
|Diffusion Policy||:black_square_button:
|OpenVLA||:black_square_button:
|Octo||:black_square_button:

## Dataset

| dataset | paper | traj&scene | task | size | DOF | camera & view |
|:--|:--|:--|:--|:--|:--|:--|
| **[DROID](https://droid-dataset.github.io/)** | Khazatsky et al., 2024 | about **76K traj**，564 场景 | 多物体、多任务桌面操作 | ≈ **1.7 TB** （part of full need？～3.4T） | 7-DoF 关节 + 抓取器宽度 | 外部 + 手腕 RGB-D |
| **[Mobile ALOHA](https://tonyzhaozh.github.io/aloha/)** | Fu et al., 2024 | about **1K+ traj** | 双臂操作与移动任务 | ≈ **2 TB** | 14-DoF 双臂 + 基座 | 3–4 第一人称 RGB |
---
| dataset | paper | traj&scene | task | size | DOF | camera & view |
|:--|:--|:--|:--|:--|:--|:--|
| **RT-1** | Brohan et al., 2022 | about **130K traj**，13 种机器人 | 多任务、多环境（家庭、办公） | ≈ **17 TB** | 6D EEF + 抓取器 + 基座移动 | 单外部 RGB 相机 |
| **RH20T** | Fang et al., 2023 | about **110K traj**，140 任务 | 多类型操作任务（长/短混合） | ≈ **9 TB** | 混合 6/7-DoF + 抓取器 | 第三人称 RGB-D（三视角） |
| **RoboSet** | Kumar et al., 2024 | about **36K traj**，34 任务 | 桌面操作、多物体交互 | ≈ **3 TB** | 7-DoF + 抓取器 | 多角度 RGB-D |
| **BridgeData V2** | Walke et al., 2023 | about **350K traj**，70+ 任务 | 多样机器人与环境 | ≈ **22 TB** | 6-DoF EEF + 抓取器 | 单外部 RGB |
| **Open X-Embodiment** | Collaboration et al., 2023 | ≈ **1M+ traj**（22+ 实验室） | 跨机器人、多环境任务 | **100 TB+** | 6–14 DoF（多构型） | 混合（RGB / RGB-D / 第三人称） |

---

CLIP, SigLIP, Llama 2, Qwen-VL这些VLM模型是通过互联网规模的数据训练的，现在在机器人领域即便最大的数据集droid等(1.8T)也不过100K~1M的数据量（采集和标注成本很高，对任务的多样性也有要求）

机器人领域的ImageNet：
1. 必定是sim dataset，因为real场景的布置和测试成本，可信度都不够
2. 规模够大
3. 足够好的real2sim

## VLA roadmap（past）


RT-2, OpenVLA, TinyVLA使用自回归离散化（autoregressive discretization）以类似text token的方式表示action.（来自openpi0中概括）

属于VLM+AR

openpi属于VLM+flow matching, 从VLM+Diffusion发展而来，属于单独分支。

~~~
--- VLM + AR

--- VLM + diffusion  --- VLM + diffusion
                     --- VLM + flow matching
~~~

## VLA主流结构

### VLM + AR

使用自回归来将VLM输出的action token映射回action

### VLM + diffuison

diffusion 经过 Diffusion Policy 和 Flow matching之后，从非确定性转向了学习确定性的流。

### VLM + flow matching

## 关心的问题

有没有高质量方便的real2sim方案？重建场景，物体，材质，并在仿真中获得摩擦力系数等数值。

数据采集方案，目前大多数集中于室内小范围。能否应用VR？最好是方便扩大规模的同时，保证精度，成本低