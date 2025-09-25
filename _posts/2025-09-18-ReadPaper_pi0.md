---
title: pi0
date: 2025-09-18 15:31
category: 
author: 
tags: []
summary: 
math: true
image:
    path: /assets/img/2025-09-18-17-08-58.png
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

$\pi_0$分别处理了这3个问题:

### Diverse data sources

为了整合不同的数据源，利用预训练的视觉语言模型（Vision-Language-Model）来导入Internet-scale的经验。也就是说: $\pi_0$模型建立在VLM的基础上，继承LM和VLM的一般知识、语义推理和解决问题的能力。然后进一步训练模型，使其包含机器人动作**action**，转化为VLA model.

*cross-embodiment training* 来整合不同的机器人数据源。因为不同的机器人类型可能有不同的配置和动作表示。（single, dual-arm system, mobile manipulators）

> in order to make it possible to perform highly dexterous and intricate physical tasks, we use an action chunking architecture with flow matching (a variant of diffusion) to represent complex continuous action distributions

*action chunking architecture with flow matching*来表示复杂连续的动作分布(complex continuous action distribution)，能够实现灵活和复杂的物理任务。模型能够在50Hz的速度控制机器人来完成叠衣服等工作。 为了将flow matching与VLM结合，使用了一个novel *action expert* 使用flow-based outputs来增强standard VLM

(初步猜测, action expert是负责将VLM的输出解释为特定格式或类型的描述？能够与action对应？或者描述action？)

**training recipe** pre-training/post-traininig separation. 与NLP和CV中的大模型一样，先在large and diverse corpus上进行预训练，然后在more narrow and more carefully curated data上进行fine-tune来获得期望的行为模式（灵活性、效率和鲁棒性）。模型不仅能只在高质量的数据集上训练，这样无法教会模型如何从错误中恢复，也不能只在低质量数据集上训练，这样模型无法学会如何高效稳健地完成任务。需要将二者结合，尽可能尝试类似于高质量数据的行为，但仍然有一系列的回复和纠错措施，能够处理错误情况。

contribution包含两条: 、

1. 基于VLM预训练和flow matching的通用机器人策略架构
2. 机器人基础模型的pre-training/post training训练策略的实证研究(empirical investigation)

评估维度分三类：

1. 零样本语言指令执行(out of the box with language commands)
2. 针对特定任务的微调(fine-tuning to downstreams tasks)
3. 配合high-level semantic policy输出intermediate language commands来执行complex and temporally extended tasks(将high-level命令拆分成 中间语言命令，来执行复杂任务)

## autoregressive discretization

自回归离散化，将连续的机器人动作（关节角、末端速度、抓取开合值等）量化成离散的"action token"，再像语言模型那样按时间顺序一步步预测下一个action token(自回归)

**以类似text token的方式表示action**

## flow matching

a variant of diffusion.

优势: 能够处理高频action chunks(up to 50Hz)和高灵巧任务


## Dataset

使用了10000小时的数据集和另一个开源OXE数据集。

## Framework

![](/assets/img/2025-09-20-17-03-26.png)

第一部分：pre-training mixture 包含:
 
1. 灵巧操作数据集 $\pi$ dataset（7中不同的机器人配置 + 68种不同任务）
2. OXE dataset（22个机器人的数据）
3. language labels，包含任务名称和分段注释（子轨迹的fine-grained标签，通常2s）

预训练阶段的目的是训练一个base model具有广泛的能力和通用性。但不一定专门对于某一项任务具有high performance.

这个base model能够follow language commands并执行多样的任务(at rudimentary proficiency)

对于复杂且灵巧的任务，采用post-training procedure. 使用high-quality curated data使模型适应特定下游任务。

$\pi_0$模型在PaliGemma VLM的基础上训练，使用data mixture对其进行进一步训练。 添加了flow matching来获得action outputs.

## 