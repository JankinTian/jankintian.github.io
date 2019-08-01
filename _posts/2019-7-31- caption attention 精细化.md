---
layout: post
title:  "Deliberate Attention Networks for Image Captioning"
date:   2019-07-31 10:18:54
categories: 论文解读
tags: caption attention 精细化 2019年 AAAI 
---

* content
{:toc}
---
deliberate ：深思熟虑的，有意识的
attention在编码器的隐藏状态和视觉注意力是必不可少的一次性前进任务。但是缺少deliberate action。
The first-pass基于残差的关注层准备隐藏状态和视觉注意以生成字幕的初步版本，The second-pass 故意基于残差的关注层重新确定它们。

---
>* Deliberation network,包含两级解码器。
第一个生成粗粒度的句子和相应的隐藏状态。第二个解码器仔细的提炼句子(refines the sentence with **deliberation**)。在网络中，第二个解码器可以利用过去和未来部分的全局信息。
* Exposure bias
1、sampling mechanism ：以决定在训练期间使用生成的单词或ground truth中的单词。
2、GAN：强迫在训练和抽样过程中产生的隐藏状态分布彼此接近。这两种方法都旨在弥合培训和测试之间的差距。
* 强化学习
* 检索
检索是计算图像和句子之间的映射的任务。检索模型强制图像字幕模型生成判别语句。

模型组成：两个基于残差的attention layers 和一个强化模型。

* 第一个attention layeer：基于注意力的LSTM和residual shortcuts（剩余快捷方式） 的组合产生隐藏状态和参与图像特征。
------>>>>>>>>> 生成粗粒度的image caption
* the second **deliberate residual-based** attention layer:
重新定义了先前的隐藏状态，并参加了视觉特征以产生更好的句子。
------>>>>>>>>> 减轻了Exposure bias，可能产生更好的句子。
* 强化学习
------>>>>>>>>> 为了进一步生成判别句子（discriminative sentences）。

![整体模型结构图](https://i.loli.net/2019/07/31/5d41143ed184297096.png)

## 图像表示
使用预训练的ResNet101提取全局视觉特征，
Faster R-CNN生成边界框，再用预训练的ResNet-101提取L区域特征。
------>>>>>>>>>生成L+1个图像特征{v<sub>0</sub>,V}, v<sub>0</sub> 是全局特征，V=(v<sub>1</sub>,...,v<sub>L</sub>)是L个区域特征。

## Deliberate Attention Mechanism
该模块包括两个基于残差的Attention层。
第一层 aim to 以隐藏状态和视觉注意力生成字幕的初步版本，而第二层则用作校对过程以重新定义字幕。
这两层都是建立在基本的LSTM结构之上的，基本的LSTM如下图所示：
![基本的LSTM公式](https://i.loli.net/2019/07/31/5d4147ea55fb999207.png)

#### First 基于残差的Attention layer
* LSTM的输入 ：**x<sup>1</sup><sub>t</sub>=[v<sub>0</sub>,h<sup>2</sup><sub>t-1</sub>,w<sub>t</sub>]**
**v<sub>0</sub>：** global image visual feature
**h<sup>2</sup><sub>t-1</sub>：** the output of the second LSTM layer, at the t-1 time step
利用更精确的信息来指导 h<sup>1</sup><sub>t</sub> 的学习。
**w<sub>t</sub>：** the feature of the current word derived(源于) by embedding an one-hot word vector.
 ----> 基于全局的视觉特征，先前的隐藏状态 和 the t-th word 得到当前的隐藏状态。
* LSTM的输出： h<sup>1</sup><sub>t</sub>=LSTM[x<sub>t</sub>,h<sup>1</sup><sub>t-1</sub>]
* 提供the t-th高频词的additional word shortcut来进行有效的训练，
使用residual shortcut connetion来降低梯度消失的现象。
**<span style="text-decoration:overline;">h</span><sup>1</sup><sub>t-1</sub>=W<sub>rd</sub>[W<sub>t</sub>;h<sup>1</sup><sub>t-1</sub>]**
W<sub>rd</sub>：学习的参数
**[;]**连接操作
* Visual attention mechanism(视觉注意力机制)
![视觉注意力](https://i.loli.net/2019/08/01/5d427164d51b673724.png)
w<sub>z1</sub>，w<sub>v1</sub>,w<sub>h1</sub> 需要学习的参数
V=[V<sub>1</sub>,V<sub>2</sub>...V<sub>L</sub>] 代表L个区域特征
**α<sup>1</sup><sub>t</sub>** 代表Attention 的权重
----->>>用在区域特征上，来定位重要的视觉信息。
![attention作用在图像区域之后](https://i.loli.net/2019/08/01/5d4273311a98323370.png)

#### Second基于残差的Attention layer
通过将 softmax 和 第一个基于残差的注意力层的损失函数集成到一起
可以在每一步生成一个预备词。
* 设计了一个**视觉哨兵(Visual Sentinel)**,作为视觉特征的补充 ---> 即非视觉词(eq:the,of)
![视觉哨兵](https://i.loli.net/2019/08/01/5d4275361257c41649.png)
![attention](https://i.loli.net/2019/08/01/5d42762b2de7a69260.png)
![最后生成的单词](https://i.loli.net/2019/08/01/5d42774deddc945854.png)

### 损失层
训练过程分为两步：①使用MLE loss(最大似然估计)预训练这个模型；②使用强化学习细化这个模型
#### step1 MLE loss
![MLE loss](https://i.loli.net/2019/08/01/5d4279f718a3326786.png)
其中：
![](https://i.loli.net/2019/08/01/5d4279f72b74776363.png)
#### step2 Reinforcement Learning
>* Contrastive Loss (CL)对比损失
![相似性](https://i.loli.net/2019/08/01/5d4279f75a47597440.png)
![Contrastive Loss](https://i.loli.net/2019/08/01/5d4279f748ef551687.png)
* **[x]<sub>+</sub>**=max(x,0)
* **(c,I)**匹配的一对caption 和 image
* **（I,c<sup>'</sup>）** 是不匹配的，c<sup>'</sup>是I不正确的描述。
**（I<sup>'</sup>,c）** 是不匹配的，c 是 I<sup>'</sup>不正确的描述。
* α 是用来满足（c，I）和 （I<sup>'</sup>,c）的最小间距

>* CIDEr + CL
![CIDER+CL](https://i.loli.net/2019/08/01/5d427d1fda6fb68399.png)

## Experiments
![coco](https://i.loli.net/2019/08/01/5d427dfbe019586191.png)
![flickr](https://i.loli.net/2019/08/01/5d427dfbee55232789.png)
