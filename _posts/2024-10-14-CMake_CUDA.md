---
title: 不使用FindCUDA传递cuda路径
date: 2024-10-14 20:31
category: Net
author: winka9587
tags: [Net]
summary: Summary of the article ViT in MASt3r
math: true
---

需要在find_package(CUDA REQUIRED)时, 报错无法找到CUDA_TOOLKIT_ROOT_DIR

并非需要设定一个环境变量, 而是因为当前的cmake版本是3.30, 在3.27版本时就已经不需要find_package(CUDA)了, 而是使用enable_language(CUDA), 但是其并不会去寻找CUDA_TOOLKIT, 所以还需要使用find_package(CUDAToolkit REQUIRED).

但是在而是使用enable_language之前, 需要使用set(CMAKE_CUDA_ARCHITECTURES xx)来指定当前的架构, 说是架构, 其实就是当前gpu的算力(Compute Capability), 可以通过[GPUs_supported](https://en.wikipedia.org/wiki/CUDA#GPUs_supported)来查询

例如: nvidia-smi当前显卡4090

![](/assets/img/2024-10-14-20-36-38.png)

查询对应的Compute capability为8.9

![](/assets/img/2024-10-14-20-36-05.png)

因此使用

~~~
set(CMAKE_CUDA_ARCHITECTURES 89)
~~~

---

## No CMAKE_CUDA_COMPILER could be found.

~~~
CMake Error at AREngine/Rendering/CMakeLists.txt:11 (enable_language):
  No CMAKE_CUDA_COMPILER could be found.

  Tell CMake where to find the compiler by setting either the environment
  variable "CUDACXX" or the CMake cache entry CMAKE_CUDA_COMPILER to the full
  path to the compiler, or to the compiler name if it is in the PATH.
~~~

提示设置CUDACXX环境变量, 或者cmake变量CMAKE_CUDA_COMPILER来指定nvcc路径.

但是即便设置CUDACXX为which nvcc的路径也无济于事, 后来终于发现了[答案](https://stackoverflow.com/questions/68950463/cmake-cuda-compiler-flag-is-false-despite-cuda-being-found):

![](/assets/img/2024-10-14-21-28-34.png)

来自[link](https://stackoverflow.com/questions/6622454/cuda-incompatible-with-my-gcc-version/46380601#46380601):

![](/assets/img/2024-10-14-21-29-56.png)

检查我当前的nvcc版本为11.8, gcc版本为12.3, 确实超过了最高的支持版本(11).

使用后遇到了另一个问题, 导致CMAKE_CUDA_COMPILER依然无法找到

关于**sudo权限对环境变量的影响** 可以跳转到最后

发现编译时不使用sudo权限即可正常找到CUDAToolkit

include中CUDA_INCLUDE_DIRS要替换为${CUDAToolkit_INCLUDE_DIRS}

~~~
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMemFree_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuCtxPushCurrent_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMipmappedArrayDestroy'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuStreamSynchronize'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMipmappedArrayGetLevel'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuImportExternalMemory'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuWaitExternalSemaphoresAsync'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuCtxPopCurrent_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMemcpyDtoH_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuSignalExternalSemaphoresAsync'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuCtxCreate_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuDeviceGetUuid'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuDestroyExternalSemaphore'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuDeviceGet'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuDestroyExternalMemory'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMemcpy2D_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuStreamCreate'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMemAllocPitch_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuGetErrorName'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuDeviceGetCount'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuExternalMemoryGetMappedMipmappedArray'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMemcpy2DAsync_v2'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuInit'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuImportExternalSemaphore'
/usr/bin/ld: /path/to/project/libvsgRenderer.so: undefined reference to `cuMemAlloc_v2'
~~~

https://docs.nvidia.com/deploy/cuda-compatibility/

![](/assets/img/2024-10-14-22-20-59.png)

一般来说, undefined reference问题都是link出了问题

这里是没有link CUDA runtime, 也就是 CUDA:cudart

但是CUDA::cudart并不在toolkit中, 而是需要find_library(CUDA_DRIVER_LIBRARY cuda) *[注意是library而非package]*

## sudo权限对编译的影响

为什么root权限会影响编译？其实很好理解, 还是安全原因。sudo(**S**uper **U**ser **Do**)命令其实就是以另一个用户的身份来执行一条命令（类似于switch user, su xxx来切换到xxx用户），临时获取管理员权限。

既然是临时提升权限，那么原有用户的环境变量就不可能完全继承，不然无法保证安全。因此，有些用户的环境变量就会被过滤掉。

有意思的是：如果想使用 sudo echo $PATH来测试: 

~~~
echo $PATH
sudo echo $PATH
~~~

那么很遗憾, 两条命令的输出结果是一样的。这是因为，PATH变量依然是在当前shell中解析的, 而非在sudo环境中解析的。sudo只是运行了echo命令，PATH在运行echo之前就被解析并传入了。

如果想测试，可以使用下面的命令

~~~
env | grep PATH
sudo env | grep PATH
~~~