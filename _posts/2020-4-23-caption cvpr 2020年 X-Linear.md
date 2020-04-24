---
layout: post
title:  X-Linear Attention Networks for Image Captioning"
date:   2020-04-23 10:18:54
categories: 论文解读
tags:    caption cvpr 2020年
---

* content
{:toc}

----------
最近在细粒度视觉识别和视觉问题回答方面的进展具有双线性池的特点，它有效地模拟了跨多模态输入的二阶交互。然而，没有证据支持建立这种具有注意机制的相互作用的图像标题。在本文中，我们引入了一个统一的注意块 X-Linear注意块，它充分利用双线性池来选择性地利用视觉信息或进行多模态推理。从技术上讲，X-Linear注意块同时利用空间和信道双线性注意分布来捕获输入单模态或多模态特征之间的二阶相互作用。更高甚至无穷大的特征相互作用很容易通过叠加多个X-Linear注意块来建模，并分别以无参数的方式为该块配备指数线性单元(ELU)。 此外，我们还提出了X-Linear注意网络(称为x-lan)，它将X-Linear注意块集成到image encoder和句子解码器中，以利用高阶模态内和模态间的相互作用。在COCO基准上的实验表明，我们的 X-LAN在COCO Karpathy test split中获得了132.0%的最佳CIDEr性能。 When further endowing Transformer with X-Linear attention blocks, CIDEr is boosted up to 132.8%. 
代码链接：https://github.com/Panda-Peter/image-captioning
----------

#
