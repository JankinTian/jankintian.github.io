---
layout: post
title:  "Group Normalization"
date:   2019-08-17 10:18:54
categories: 论文解读
tags:    2018年  
---

* content
{:toc}

----
Batch Normalization(BN)进行归一化会带来一些问题——由于批处理统计估计不准确，当批处理尺寸变小时，BN的误差会迅速增大。
GN的计算与BN无关，并且在各种BN大小下都很稳定。
Group Normalization：建议将GN作为一个层，将通道分成组，并在每组内计算标准化的均值和方差。
GN可以从预训练转到微调。
GN在训练序列模型（RNN/LSTM）或者是生成模型（GANs）是非常有效的。

----
Layer Normalization (LN)： 沿着通道层工作。
Instance Normalization (IN)：执行类似 BN 的计算，但只对每个样本执行。

# Group Normalization
SIFT、HOG和GIST的经典特征是group-wise表示的by design，其中each group of channels 是由一些直方图组成的。
![各种Normalization的对比](https://i.loli.net/2019/09/01/IoDJZ4PjqthWdxU.png)

## 一般的计算公式
![总公式](https://i.loli.net/2019/09/01/KxZUtqEfaLTburX.png)
> X 是计算的特征
µ 是均值
σ 是标准差

![均值和方差](https://i.loli.net/2019/09/01/jQntC6PHAbDwYWc.png)
>* 各种类型的特征正则化的区别是 S<sub>i</sub> 

### Batch Norm
![blob](https://i.loli.net/2019/09/01/CsEdg9WfiLBFjhz.png)
i<sub>c</sub> 代表的是在C 轴上的索引。
在（N,H,W）轴上计算均值和方差。
### Layer Norm
![blob](https://i.loli.net/2019/09/01/QM1khNJFrsOv5Zd.png)
在（C,H,W）轴上计算均值和方差。
### Instance Norm
![blob](https://i.loli.net/2019/09/01/rbtYuH7iwQSNjBG.png)
对于每个样本和每个通道，沿着（H,W）轴计算均值和方差。

>* BN,LN,和IN所有的方法学习的是每个通道的线性变换，来补偿可能丧失的代表能力。
![blob](https://i.loli.net/2019/09/01/jmknSBiWFO61K9N.png)
γ ：可训练的缩放。
β ：可训练的位移。

### Group Norm
![blob](https://i.loli.net/2019/09/01/IQu6XcW5awANGdY.png)
G ：Group的数量，预先定义的超参。
C/G :是每组有多少个通道。
计算均值和方差，**沿着（H,W）轴和一组C/G的通道**
>* GN的两个极端情况相当于 LN 和 IN。
Layer Normalization：如果我们设定G为1，则GN变成LN。
LN假设一层中的所有通道都有“相似的贡献”
GN比LN具有更高的灵活性，在每一组学习不同的分布

![blob](https://i.loli.net/2019/09/01/Mpvb6VgIPeJ8GNF.png)