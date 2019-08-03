---
layout: post
title:  "Stack-Captioning: Coarse-to-Fine Learning for Image Captioning"
date:   2019-08-1 10:18:54
categories: 论文解读
tags: caption  精细化 2018年 AAAI 
---

* content
{:toc}

---
本文主要介绍的是从粗到细生成caption的过程。
本文的主要结构是三层的LSTM。多层的模型可以生产丰富的细粒度描述；可以解决梯度消失的问题。

---
> 现在caption方法存在的三个主要问题：
1、很难生成丰富的细粒度描述。
因为，生成细粒度描述需要高度复杂的模型，梯度消失问题就会经常出现。
2、**exposure bias** between the training and the testing
为了解决这个问题**：预定采样(scheduled sampling)**【只能减缓不能解决】
randomly selecting between previous ground- truth words and previously generated words已经成为针对基于RNN的模型的当前主导训练过程。
3、loss-evaluation mismatch(不匹配)
语言模型最小化每个时间的交叉熵损失，在测试阶段句子评估时的评价指标不可微，不能直接用作损失。

![整体模型图.png](https://i.loli.net/2019/08/03/sXELS3KBc5QDUuO.png)
第一个LSTM生成粗粒度的图片描述
剩下的LSTM作为细粒度解码器

## 方法
### Image Encoding
V = CNN(I)
V = {V<sub>0</sub>,...,V<sub>k×k-1</sub>}
V 是空间的图像特征，
k×k 是the number of regions, each feature channel V<sub>i</sub> depicts a region of the image
d<sub>v</sub> 是每个区域特征的维度。

### Coare-to-Fine Decoding
![coare2fine.png](https://i.loli.net/2019/08/03/VEzFMOD2NK581bT.png)
第一个LSTM生成粗粒度的图片描述
剩下的LSTM作为细粒度解码器,根据图像特征和前一阶段的输出预测改进图像描述
特别地，我们使用前一阶段的注意权重来提供用于单词预测的区域的以下阶段信念（beliefs）。

#### Coarse Decoder
![Coarse.png](https://i.loli.net/2019/08/03/STMGxitQsHOVrXB.png)
#### Fine Decoder
![fine.png](https://i.loli.net/2019/08/03/du3DTl4PsNqyEkS.png)
#### Stacked（堆叠） Attention Model
![attention.png](https://i.loli.net/2019/08/03/KSNGAlzgXOdwtfs.png)

## Learning
交叉熵损失
![XE.png](https://i.loli.net/2019/08/03/mWVOFC8b6ynJcgA.png)
![reward.png](https://i.loli.net/2019/08/03/G7AP3qoZsB6LHSI.png)