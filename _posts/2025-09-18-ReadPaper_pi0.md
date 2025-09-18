---
title: pi0
date: 2025-09-18 15:31
category: 
author: 
tags: []
summary: 
math: true
image:
    path: /assets/img/2025-09-18-15-32-09.png
---

<style>
.figure-center {
    text-align: center;
    margin: 20px auto;
    max-width: 100%;
}
.figure-center img {
    display: block;
    margin: 0 auto;
    max-width: 100%;
    height: auto;
}
.figure-center figcaption {
    text-align: center;
    font-style: italic;
    margin-top: 8px;
    color: #666;
    font-size: 0.9em;
}
</style>


![](/assets/img/2025-09-18-15-32-09.png)

https://www.physicalintelligence.company/blog/pi0

## Before start

梳理了一下VLA的发展历程，理论上应当从RT-1, RT-2, RT-X系列开始阅读，但是考虑到24和25年相关新工作的大量出现，尽快入门更加重要。目前的阅读思路是pi0, OpenVLA, RDT, pi0.5

About **Generalist Robot Policies**(i.e., robot foundation model): 

>
> *Today’s robots are narrow specialists. Industrial robots are programmed for repetitive motions in choreographed settings, repeatedly making the same weld in the same spot on an assembly line or dropping the same item into the same box.*
> 

## pi0


> Perhaps the most tangible progress toward this kind of versatility in AI can be seen in large language- and visionlanguage models [1, 48]: systems that are pre-trained on large and very diverse corpora of images and text from the web, and then fine-tuned (“aligned”) using more carefully curated datasets meant to induce the desired pattern of behavior and responsiveness.

fine-tune 或者 align 都是指在更小的的专门数据集上进行优化。

相比于直接在专门任务的数据上进行训练，先在大规模通用数据上进行预训练，然后针对特定任务进行fine-tune或prompt会更有效。（来自NLP和CV领域大模型的经验）这样能解决鲁棒性和泛化性的问题。

Chanlenges:

1. a very large scale. 必须在大规模数据上进行训练
2. developing the right model architectures. 需要开发正确的模型架构，可以有效地利用不同的数据源
3. right training *recipe*. 训练策略，NLP和CV领域中的大模型都很依赖delicate strategies for curating pre-training and post-training data.

<figure class="figure-center">
  <img src="/assets/img/2025-09-18-16-56-18.png" alt="">
  <figcaption>framework of $\pi_0$ 0</figcaption>
</figure>

