---
layout: post
title: CMakeLists include 问题
date: 2024-05-21 10:59
category: cmake
author: 
tags: [cmake, CMakeLists]
summary: 
---

# include_directories与target_include_directories

**include_directories**并不会将include继续向外层传播, 导致最外层的executable在编译时无法找到模块中的头文件**
