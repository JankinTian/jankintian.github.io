---
layout: post
title:  "Context-Aware Visual Policy Network for Fine-Grained Image Captioning"
date:   2019-08-13 10:18:54
categories: 论文解读
tags: paragraph_caption   2019年  
---

* content
{:toc}

---
本文介绍了机器推理对语言学习的重要性。并提出：**将视觉上下文结合到顺序视觉推理中。**
深度强化学习也是机器推理在image caption 中的应用。

---

# 摘要
随着视觉检测技术的成熟，我们更加雄心勃勃地使用开放词汇表、细粒度和自由形式的语言来描述视觉内容，即图像字幕的任务。特别是，我们感兴趣的是生成更长、更丰富和更细粒度的句子和段落作为图像描述。图像字幕可以转化为给定视觉内容的顺序语言预测任务，输出序列用合理的语法构成自然语言描述。然而，现有的图像字幕方法仅侧重于语言策略，而不是视觉策略，因此无法捕捉视觉上下文，而视觉上下文对于构成推理(如“人骑马”)和视觉比较(例如“小(耳)猫”)至关重要。当生成较长的序列(如段落)时，此问题尤其严重。为了填补这一空白，我们提出了一种用于细粒度图像到语言生成的上下文感知视觉策略网络(CAVP)：图像句子字幕和图像段落字幕。在字幕过程中，CAVP显式地将先前的视觉注意视为上下文，并在当前视觉注意的情况下，决定上下文是否用于当前单词/句子的生成。与传统的只在每一步固定一个视觉区域的视觉注意机制相比，CAVP可以在一段时间内处理复杂的视觉成分。整个图像字幕模型-CAVP及其后续语言策略网络-可以使用actor-critic policy gradient方法对端到端进行有效的优化。我们通过在mMS-COCO和Stanfordd captioning datasets 上的state-of-the-art performances，使用各种度量标准和对定性视觉上下文的合理可视化，证明了CVAP的有效性。

###### 现有强化学习的缺点
- 忽略了将VP(visual policy)转化为决策。
Eg： VP的输入在每个步骤中都是相同的，这不遵守序列预测的性质，其中 historical 视觉动作（例如，previously attended regions）应当显着地影响当前的视觉策略。
- **LP**(Language policy)中的隐藏向量只记忆了语言模型的上下文信息，而没有提供视觉的上下文信息。
  可以解释为：视觉记忆回忆在推理中起着至关重要的作用。
![传统方法与本文方法的对比](https://i.loli.net/2019/08/14/P9xOjVvaAqMEbRc.png)

# 方法
视觉决策任务分为四个子网络，
每一个都是共享权重的LSTM并且产生一个 soft visual attention map。
![整体结构图.png](https://i.loli.net/2019/08/14/8kwALbU4Xx6Rscq.png)

>* 在每个时刻t中，CVAP将**图像 I** 和 **视觉上下文{v<sub>1</sub>, · · · v<sub>t−1</sub>}**作为输入来预测**视觉表示 v<sub>t</sub>**。
* 在LP中，**视觉表示 v<sub>t</sub>** 和前一个单词 **y<sub>t-1</sub>** 作为输入来预测下一个单词 **y<sub>t</sub>**。 

## 1、概述
将图像字幕任务制定为一个连续的决策过程(sequential decision-making process)，并对所提出的模型进行分析。

- *agent*和*environment*交互，然后执行一系列的*action*，优化*reward*来得到相应的*goal*。
>- *agent*：是由context-aware visual policy network (CAVP) 和 language policy network (LP)组成的caption 模型。
- *environment*:要生成caption的图像I。
- *action*：CVAP的action是生成视觉表示 v<sub>t</sub> ；
LP的action是预测单词y<sub>t</sub>。
- *reward*:ground truth和预测之间的任何评估度量分数。
- *goal*：生成给定图片的语言描述。
- 观察的*state*：image I, the visual context {v<sub>1</sub>, · · · v<sub>t−1</sub>}, and the predicted words {y<sub>1</sub>, · · · y<sub>t−1</sub>} 。


## 2、CVPR(Context-Aware Visual Policy Network)
* CVAP 分为4个子网络
>- single SP：获得正在进行的任务表示。
- context SP：推理与任务相关的上下文。
- composition SP：模拟上下文与正在进行的任务之间的关系。
- output SP：识别上下文的效果。

### 2.1 sub-policy network
![sub-policy.png](https://i.loli.net/2019/08/16/H879kwTviYGK1XJ.png)
#### Single Sub-policy Network
![single sp.png](https://i.loli.net/2019/08/16/B5nJUWvT2ztqQOD.png)
#### Context Sub-policy Network
![context sp.png](https://i.loli.net/2019/08/16/GQe4xY1nCLigyTa.png)
#### Composition Sub-policy Network
![composition 1.png](https://i.loli.net/2019/08/16/cjmIg6zWw7BbrLG.png)
![composition 2.png](https://i.loli.net/2019/08/16/yU31mjMEPD6CAub.png)
#### Output Sub-policy Network
![output.png](https://i.loli.net/2019/08/16/BLrgzkJTmR2uElo.png)
#### Weight Sharing
![weight share.png](https://i.loli.net/2019/08/16/1GS8xfs6HnzcE4u.png)


### 2.2 为段落caption设计的分层CVAP

#### Sentence-level CAVP
#### Word-level CAVP
## 3、Language Policy Network
![caption生成.png](https://i.loli.net/2019/08/15/ivQxMcmhsNt6WVK.png)
* 生成image paragraph caption的网络,分成两步
>- 1、生成一系列的 topic 向量
- 2、将每个topic vectors翻译成一句话(a sentence of words)
* 总的过程： 我们首先使用句子级CAVP-LP，它将图像 I 和视觉上下文{v<sub>1</sub>, · · · v<sub>t−1</sub>}作为输入，并产生视觉表示 v<sub>t</sub> ，topic vector t<sub>i</sub> 和停止概率 p<sub>stop</sub><sup>i</sup>。然后，将topic vector t<sub>i</sub> 注入到单词级LP中，以约束第i个句子的每个单词 y<sub>i,j</sub> 的生成。

### Sentence-level LP
### Word-level LP


















