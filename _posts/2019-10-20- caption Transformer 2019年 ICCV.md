---
layout: post
title:  "Attention on Attention for Image Captioning"
date:   2019-10-20 10:18:54
categories: 论文解读
tags:    caption Transformer 2019年 ICCV
---

* content
{:toc}

---
注意机制被广泛应用于当前基于Encoder/Decoder的image captioning框架中，其中在每个时间步骤上生成编码向量的加权平均值，以指导caption解码过程。然而，Decoder很少知道the attended vector 和the given attention query 是否或如何相关，这可能会使Decoder给出错误的结果。在本文中，我们提出了一个“Attention on Attention”（AOA）模块，它扩展了传统的注意机制，以确定Attention results与queries之间的相关性。
AoA首先利用注意结果和current context生成一个“**information vector**”和一个“**attention gate**”，然后通过对它们应用元素乘法来添加另一个注意，并最终获得“**attended information**”，即预期的有用知识。我们将AOA应用于我们的image caption模型的编码器和解码器，AoA Network（AoANet）。实验表明，AoANet优于所有先前发表的方法，并在MS COCO “Karpathy” offline test split 得到了129.8的CIDER-D 的得分和在the official online testing server 129.6CIDER-D（C40）的评分。
[源码地址](https://github.com/husthuaan/AoANet)
---

# 论文目的
### 问题
不知道 Attended Vector 和 Attention Query之间是否和如何相关 --> 导致Decoder的结果的出错

### 采取的方法
利用注意结果和current context生成一个“**information vector**”和一个“**attention gate**”，然后通过对它们应用元素乘法来添加另一个注意，并最终获得“**attended information**”。

# 论文思想
下图是传统attention和AoA模型的对比结果图以及AoA模型的基本介绍：
![AoA与传统attention对比图](https://i.loli.net/2020/02/26/5Phg2fLuUEKACHy.png)

- AoA模型在Encoder上的应用：
用在Encoder中的作用：是细化特征之间的关系
![encoder](https://i.loli.net/2020/02/26/cGX2bDnhypgaJZ5.png)
- AoA模型用在Decoder上：
作用是：更好的进行caption的预测
![Decoder](https://i.loli.net/2020/02/26/f7uptvB8IsnUek9.png)

# 实验结果
![实验结果图](https://i.loli.net/2020/02/26/J1Y73An42aeo5sb.png)
Table1是本文中的方法和其他方法的在“Karpathy” test split上的对比结果图。
Table2 是在online MS-COCO test server上的对比结果图。
![消融实验](https://i.loli.net/2020/02/26/FwZaHRc1DsWuzGY.png)
从中可以看出AoA的策略在base的基础上提升了3个点，还是比较有效的。