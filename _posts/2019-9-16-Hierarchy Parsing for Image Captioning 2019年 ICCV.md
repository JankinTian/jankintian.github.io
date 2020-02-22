---
layout: post
title:  "Hierarchy Parsing for Image Captioning"
date:   2019-09-16 10:18:54
categories: 论文解读
tags:    caption GCN 2019年 ICCV
---

* content
{:toc}

----------
人们一直认为，将图像解析成固定的视觉模式将有助于理解和表示图像。然而，没有证据支持关于用自然语言发言描述图像的想法。在本文中，我们引入了一种新的设计，以从instance level （分割）、region level （检测）to whole image对层次进行建模，从而深入到对字幕的全面的图像理解。具体地，我们提出了一种层次结构解析器(HIP)体系结构，它将分层结构新颖地集成到图像编码器中。技术上，将图像解码成一组区域，并且将一些区域重新求解成更精细的区域。然后，每个区域重新进入一个姿态，即区域的前景。这样的过程自然地构建分层树。然后采用树状结构的长短期存储器(tree-LSTM)网络来对分层结构进行帧间预测，并增强所有的实例级、区域级和图像级特征。我们的 HIP是有用的，因为它可插入任何神经标题模型。

----------
# Introduction
本文主要介绍了一种 层次分析结构(HIP) ；其中的关键部分是 top-down **hierarchical tree** （从整张图片到目标和实例）<br/> 每个实例都要求区域的区分前景。 <br/>![blob](https://i.loli.net/2019/09/16/KOT9AQHqDoWFfX6.png)
<br/>具体来说，我们设计了一个三级层次结构，其中一个图像首先被分解成一组区域，一个区域要么在这个层次上被表示，要么被进一步分割成更精细的区域。<br/>每个区域对应于一个实例，该实例对该区域的前景进行建模。<br/>在层次结构上，我们在区域或实例层次上构造有向边的语义图，其中顶点表示每个区域或实例，边缘表示每一对区域或实例之间的关系。利用图形卷积网络(GCN)丰富语义图中的可视化关系特征。

## Structured Scene Parsing (结构化场景解析)
结构化场景分析的任务超越了场景类型的一般识别(分类)或场景中对象的定位(Semantic标记或分割)，并考虑了对场景的更深层次的结构化理解。

# Approach
![Figure 2](https://i.loli.net/2019/09/16/nG9Hg46mYrtedRF.png)
把层次结构集成到image encoder部分 <br/> Tree-LSTM 利用上下文在instance level；region level; image level增强特征。<br/> HIP是一个特征细化的作用，输出rich and multi-level的图像特征。<br/> 在图2中的b 和 c 部分是两个不同的caption models


## Image Encoder with Tree-LSTM
![image Encoder](https://i.loli.net/2019/09/18/XxeWvAkFK5r836c.png)
<br/>
![blob](https://i.loli.net/2019/09/19/BsFAt2ZNaIbPW8k.png)
<br/>![blob](https://i.loli.net/2019/09/19/AmsH68gu5GN2jwt.png)

## Up-Down with Hierarchy Parsing
![blob](https://i.loli.net/2019/09/19/DWgmHoNLubXUkId.png)

## GCN-LSTM with Hierarchy Parsing
![blob](https://i.loli.net/2019/09/19/4kHQj6tCqJublxU.png)

# Experiments 
![blob](https://i.loli.net/2019/09/19/OFxkj7yUcrHSQXV.png)