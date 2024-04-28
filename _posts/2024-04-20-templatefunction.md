---
layout: post
title: 模板函数,tpp与cmake
date: 2024-04-20 10:57
category: 
author: 
tags: []
summary: 
---

## 关于tpp

tpp文件通常用于存放模板类或者模板函数的实现，因为C++中的模板在编译时必须被完全实例化，所以实现和声明通常都会放在同一个文件中，或者在编译之前，应该对编译器可见。

以前的写法，都是在.h中写模板函数/类的声明，在.cpp中进行实现。

### class

~~~
template <class T>
template <typename T>
~~~

两者是相同的而且是可以相互替换的。