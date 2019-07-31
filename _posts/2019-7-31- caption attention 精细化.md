---
layout: post
title:  "Deliberate Attention Networks for Image Captioning"
date:   2019-07-31 10:18:54
categories: 论文解读
tags: caption attention 精细化 
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
------>>>>>>>>>生成L+1个图像特征{v<sub>0</sub>,V}, ‘v_0’ 是全局特征，$V=(v_1,...,v_L)$是L个区域特征。