---
layout: post
title:  "Hierarchical Attention Network for Image Captioning"
date:   2019-09-01 10:18:54
categories: 论文解读
tags:    caption attention 2019年 AAAI 
---

* content
{:toc}

----
近年来，注意机制在图像字幕中得到了成功的应用，但现有的注意方法仅建立在低级空间特征或高级文本特征上，限制了字幕的丰富性。 在这个 paper 中，我们提出了一个**分层注意网络 (HAN)** ，它使注意力能够同步地计算在特征的金字塔层次上。 金字塔层次结构由不同语义层次上的特征组成，它允许根据不同的特征预测不同的单词。 另一方面，由于特征的方式不同，提出了一种**多变量残差模块 (MRM)** 来学习特征的联合表示。 该 MRM 能够模拟预测并提取不同 features 之间的关系.此外，我们还引入了一个context gate ，以平衡不同特征的贡献。与现有方法相比，我们的方法应用层次特征，利用多种多模式集成策略，可显著提高性能。在Benchmark MSCOCO数据集中验证了HAN，实验表明，我们的模型优于现有技术的方法，达到80.9的Bleu1分数，在Karticy的测试中达到121.7的CIDEr分数。

>* 主要贡献是：
>
> 1、提出了一种分层的注意力网络(集成的方式，分别在**Text features **【语义强的文本特征】、**Patch features **【语义弱的Patch特征】、**Object features **【语义适中的特征】)从而更加细化特征。
>
> 2、**多变量残差模块** 整合不同层次的特征，其目的是将特征注入到一个统一的目标空间中，并探索不同源空间之间的内在关系。
>
> 3、引入了一个**context gate**机制，以自适应地平衡特征在不同层次上的贡献

----

# Methodology
> CNN-RNN机制：
>
>(1)CNN：提取4个不同的特征 global features V<sub>g</sub>, patch features V<sub>p</sub>, object features V<sub>o</sub> and text features V<sub>t</sub> 来建立层次特征。
>
>(2)RNN：指导生成attention 和 caption sequences.
>
>RNN包含 visual LSTM 和 language LSTM.
>
>visual LSTM：利用 image 的全局特征和不同的attention 机制生成不同的attention 特征 A<sub>p</sub>、A<sub>o</sub>、A<sub>t</sub>
>
>language LSTM：指导caption生成。
>
>(3)为了更好的利用attention的特征，我们使用 MRM 来提取不同特征之间的关系。

![blob](https://i.loli.net/2019/09/02/KxZTJYhdt5Vojpg.png)
![blob](https://i.loli.net/2019/09/02/9TYB6rAwWX75Rhe.png)

## Multivariate Residual Module（多变量残差模型）
包含两个部分projection part and a relation part。
![blob](https://i.loli.net/2019/09/02/gfDdSVLaI3tXnHA.png)
### projection part
> 学习输入数据和非线性残差函数之间的关系，而不是直接学习期望的映射。

* 是由两个独立的残差网络组成，分别处理patch attention feature 和 text attention feature
H<sub>p</sub> = A<sub>p</sub> + ReLU(W<sub>mp</sub> A<sub>p</sub>)
H<sub>t</sub> = A<sub>t</sub> + ReLU(W<sub>mt</sub> A<sub>t</sub>)
H = A<sub>o</sub> + H<sub>p</sub> + H<sub>t</sub>
H 总的预测特征
A<sub>o</sub>is the object attention feature.
### relation part
![blob](https://i.loli.net/2019/09/02/SAFGdlhPX1R3qxU.png)

> MRM 的输出：M= H + R
projection features H  and relation features R

## Context Gating
We project the two different features into the same space
目的是 balance the contribution of low-level context and high-level context。
![blob](https://i.loli.net/2019/09/02/sZSBoX86qHrhOYg.png)

## Experiment
![blob](https://i.loli.net/2019/09/02/Kl3GNPT1ho8UQZe.png)