---
title: 
date: 2025-06-05 15:17
category: 
author: 
tags: []
summary: 
---

## Env

win10 +RTX 3080, 使用CUDA 11.8 + TensorRT 8.5 GA + Python 3.10

### CUDA >= 11.4

https://developer.nvidia.com/cuda-downloads

![](/assets/img/2025-06-05-15-19-06.png)

~~~
>nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Wed_Sep_21_10:41:10_Pacific_Daylight_Time_2022
Cuda compilation tools, release 11.8, V11.8.89
Build cuda_11.8.r11.8/compiler.31833905_0
~~~

### TensorRT >= 8.4

https://developer.nvidia.com/nvidia-tensorrt-8x-download

![](/assets/img/2025-06-05-17-58-46.png)

添加环境变量

~~~
...\TensorRT-8.5.1.7\lib
~~~

### CUDNN

https://developer.nvidia.com/rdp/cudnn-archive

添加dll所在的bin路径到环境变量

### 转换yolov8模型为engine

按TensorRT官方教程, trtexec使用g++或visual studio编译下。只要前面的环境变量配置没问题即可。

![](/assets/img/2025-06-05-21-33-28.png)


### 运行

~~~
A:\WorkPlace\YOLOv8-TensorRT>python infer-pose.py --engine ./checkpoints/yolov8s-pose.engine --imgs data --show --out-dir outputs --device cuda:0
~~~