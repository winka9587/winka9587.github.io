---
title: 3dgs env problem collection
date: 2025-01-10 11:08
category: 3dgs
author: winka9587
tags: [3dgs]
summary: Summary of the article
math: true
---

### ERROR: Failed building wheel for diff-gaussian-rasterization

~~~
python 3.10.14
nvcc 11.8
cmake 3.24.2
~~~

该错误最大的特征是**几个编译中间文件报错No such file or directory**

~~~
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/conv.o: No such file or directory
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/adam.o: No such file or directory
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/backward.o: No such file or directory
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/forward.o: No such file or directory
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/rasterizer_impl.o: No such file or directory
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/ext.o: No such file or directory
      ...submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/rasterize_points.o: No such file or directory
~~~

#### 解决方案

该问题90%情况下都是cuda版本问题
~~~bash
conda list|grep cuda
~~~
检查环境

~~~
gaussian_splatting/submodules/diff-gaussian-rasterization$ pip install .
Processing /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization
  Preparing metadata (setup.py) ... done
Building wheels for collected packages: diff-gaussian-rasterization
  Building wheel for diff-gaussian-rasterization (setup.py) ... error
  error: subprocess-exited-with-error
  
  × python setup.py bdist_wheel did not run successfully.
  │ exit code: 1
  ╰─> [28 lines of output]
      running bdist_wheel
      running build
      running build_py
      creating build
      creating build/lib.linux-x86_64-cpython-38
      creating build/lib.linux-x86_64-cpython-38/diff_gaussian_rasterization
      copying diff_gaussian_rasterization/__init__.py -> build/lib.linux-x86_64-cpython-38/diff_gaussian_rasterization
      running build_ext
      /home/cxx/App/anaconda3/envs/gaussian_splatting/lib/python3.8/site-packages/torch/utils/cpp_extension.py:813: UserWarning: The detected CUDA version (11.8) has a minor version mismatch with the version that was used to compile PyTorch (11.3). Most likely this shouldn't be a problem.
        warnings.warn(CUDA_MISMATCH_WARN.format(cuda_str_version, torch.version.cuda))
      /home/cxx/App/anaconda3/envs/gaussian_splatting/lib/python3.8/site-packages/torch/utils/cpp_extension.py:820: UserWarning: There are no /usr/bin/g++-9 version bounds defined for CUDA version 11.8
        warnings.warn(f'There are no {compiler_name} version bounds defined for CUDA version {cuda_str_version}')
      building 'diff_gaussian_rasterization._C' extension
      creating /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38
      creating /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer
      Emitting ninja build file /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/build.ninja...
      Compiling objects...
      Allowing ninja to set a default number of workers... (overridable by setting the environment variable MAX_JOBS=N)
      1.10.2
      /usr/bin/g++-9 -pthread -shared -B /home/cxx/App/anaconda3/envs/gaussian_splatting/compiler_compat -L/home/cxx/App/anaconda3/envs/gaussian_splatting/lib -Wl,-rpath=/home/cxx/App/anaconda3/envs/gaussian_splatting/lib -Wl,--no-as-needed -Wl,--sysroot=/ /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/conv.o /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/adam.o /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/backward.o /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/forward.o /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/rasterizer_impl.o /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/ext.o /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/rasterize_points.o -L/home/cxx/App/anaconda3/envs/gaussian_splatting/lib/python3.8/site-packages/torch/lib -L/usr/local/cuda-11.8/lib64 -lc10 -ltorch -ltorch_cpu -ltorch_python -lcudart -lc10_cuda -ltorch_cuda_cu -ltorch_cuda_cpp -o build/lib.linux-x86_64-cpython-38/diff_gaussian_rasterization/_C.cpython-38-x86_64-linux-gnu.so
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/conv.o: No such file or directory
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/adam.o: No such file or directory
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/backward.o: No such file or directory
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/forward.o: No such file or directory
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/cuda_rasterizer/rasterizer_impl.o: No such file or directory
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/ext.o: No such file or directory
      g++-9: error: /data3/cxx/workspace/match/gs_submodules/gaussian_splatting/submodules/diff-gaussian-rasterization/build/temp.linux-x86_64-cpython-38/rasterize_points.o: No such file or directory
      error: command '/usr/bin/g++-9' failed with exit code 1
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
  ERROR: Failed building wheel for diff-gaussian-rasterization
  Running setup.py clean for diff-gaussian-rasterization
Failed to build diff-gaussian-rasterization
ERROR: Could not build wheels for diff-gaussian-rasterization, which is required to install pyproject.toml-based projects
~~~


### TypeError: \__new__() got an unexpected keyword argument 'antialiasing'

参考: https://github.com/graphdeco-inria/gaussian-splatting/issues/994

diff-gaussian-rasterization没有更新, 记得切换到3dgs_accel分支后重新安装.

**2025.1.10**

https://github.com/graphdeco-inria/gaussian-splatting/issues/1080

3dgs_accel分支存在错误, 切换至main分支

---

但我的错误并非此原因引起的（虽然报错信息一致）, 因为经过排查, 同一段代码在colmap的数据输入下是可行的。

应该是因为在转换mast3r代码生成colmap格式的输入时, 在images.bin文件中, 存储的每个图像对应的Image对象时, 其xys与point3D_ids生成空导致的错误。

在之前分析3dgs的输入时, 分析过colmap的输入格式:

>
> xys: 图像中特征点的二维坐标(需要注意其为浮点数), 分别表示x和y
>
> point3D_ids:特征点在三维空间中的对应点的id, 用于将xys与三维点云建立映射关系。
> 

get_dense_pts3d返回的pts3d, depthmaps, confs

~~~
pts3d: list, [(w1*h1, 3), ...]
depthmaps: list, [(w1*h1, 3), ...]
confs: list, [(w1, h1, 3), ...]
~~~

可以根据