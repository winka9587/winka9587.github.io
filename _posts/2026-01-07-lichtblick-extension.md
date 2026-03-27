---
title: extension of lichtblick
date: 2025-01-07 10:43
category: 
author: 
tags: []
summary: 
image:
    path: 
math: True
---

## 参考


## 目标

更新数据集打标工具
基于lichtblick，创建插件，实现打标+导出json，以mcap为原始文件。json为人工裁剪/标注信息，附带一个额外的python脚本，读取mcap数据和json数据，将其转换为lerobot数据。
可能的问题：数据同步问题，mcap的数据是否已经同步？暂时忽略？

### TODO

| 图标 | 说明 |
|----------|----------|
| :white_check_mark: | **基本完成** 不排除有新增任务的可能 |
| :black_square_button: | **待做** |
| :heavy_check_mark: | 可预见的未来 **不需要进行任何修改** |
| :construction: | 近期（2周内）**正在处理** |
| :warning: | **实现有待讨论** |


| 状态 | 任务 |
|----------|----------|
| :white_check_mark: | 基础环境配置 |
| :black_square_button: | 考虑官方提供的custom panel? |
| :black_square_button: | 加载测试mcap数据 |
| :black_square_button: | 添加交互 |
| :black_square_button: | 导出json |
| :black_square_button: | python脚本加载mcap和json文件，导出lerobot数据 |
| :black_square_button: | |

## 步骤

### 基础

#### 生成

~~~
npm init lichtblick-extension@latest my-extension-name
~~~

生成结构 主要关心
~~~
.
├── CHANGELOG.md
├── LICENSE
├── node_modules
├── **package.json**
├── package-lock.json
├── README.md
├── **src**
│   ├── **ExamplePanel.tsx**
│   └── **index.ts**
└── **tsconfig.json**
~~~

index.ts定义入口, panel的主体在对应的tsx文件中。


~~~
npm run package

npm run local-install　# 直接安装, 重启lichtblick即可
~~~

要实现对主要结构的修改，添加新的功能，需要参考API：https://lichtblick-suite.github.io/docs/docs/extensions/extension-api/introduction/

## Lerobot split

为了加速数据集的生成

修改自: lerobot:0cf864870cf29f4738d3ade893e6fd13fbd7cdb5

