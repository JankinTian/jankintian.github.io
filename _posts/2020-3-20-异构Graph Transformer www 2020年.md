---
layout: post
title:  Heterogeneous Graph Transformer
date:   2020-03-20 10:18:54
categories: 论文解读
tags:    Graph Transformer www 2020年
---

* content
{:toc}

----------
## 摘要
近年来，图神经网络（GNNs）在图数据分析领域取得了巨大的成功。然而，大多数的 GNN 都是针对同构图设计的。在同构图中，所有的节点和边都属于同一种类型。
然而实际生活中，我们可能会处理更复杂的图数据，其节点与边会有不同的类别，学术界称这类图数据为异构图。
比如学术网络、知识图谱，乃至物联网等。例如，下图中的开放学术网络包含五种类型的节点:论文、作者、机构、会议和领域，以及它们之间不同类型的关系。
![学术网络.png](https://i.loli.net/2020/04/24/5ivEgufRCI63xFJ.png)
在这篇论文中，我们提出了一种处理异构图的 GNN 模型，Heterogeneous Graph Transformer (HGT)。该模型可以对亿数量级节点的异构动态图进行建模。

论文来源：WWW 2020
论文链接：https://arxiv.org/pdf/2003.01332.pdf
代码链接：https://github.com/acbull/pyHGT

----------
# 模型
为了处理图的异构性，我们将每条边的模型参数分解为三个矩阵相乘。其分解根据每条边的三元组 <初始节点类型，边类型，目标节点类型>来定义。具体来说，我们使用这些三元组来对权重矩阵参数化，以计算每条边上的注意力以及信息传递。

因此，HGT不需要手动设计元路径 (meta path) ，也可以自动、隐式地学习和提取对不同下游任务重要的元路径。在此分解基础上，整体的计算仿照Transformer，因此相较于传统图模型有更强的表征能力。
![image.png](https://i.loli.net/2020/04/24/aIjh5GeQ3VfXAcL.png)

为了处理图数据的动态特性，我们引入了相对时间编码（Relative Temporal Encoding）。相较于传统做法，即根据不同的时间戳把初始图分割成若干个图，我们采用将发生在不同时间的边全部放在一个图里。

我们计算两两节点相互之间的时间差，并对每一个时间差以一个编码，加在初始节点表征上。这样，在学习过程中，HGT 可以学习出图中的时间依赖关系，同时发生在不同时间的节点间也可以进行信息传递。
![image.png](https://i.loli.net/2020/04/24/yiGVFAzfp3hvc6g.png)

为了处理网络规模的图形数据，我们设计了针对异构图的采样算法 HGSampling。它的主要思想是样本异构子图中不同类型的节点以相同的比例，并利用重要性采样降低采样中的信息损失。

通过 HGSampling，所有的 GNN 模型，包括我们提出的 HGT，都可以在任意大小的异构图上进行训练和推理。
![image.png](https://i.loli.net/2020/04/24/lBawS5cIus94kQ1.png)

# 实验

我们在开放学术图谱（OAG）上进行试验。该数据集包含 1.79 亿个节点和 20 亿个边组成，时间跨度从 1900 年到 2019 年。实验结果表明，与传统的 GNNs 和异构图模型相比，在下游任务中 HGT 可以显著提高 9-21%。
![image.png](https://i.loli.net/2020/04/24/G8eCELzS7kTIqQb.png)

同时，利用我们提出的相对时间编码（RTE），我们可以动态地计算出任意一个年份的节点标准。例如，我们可以观测出每个会议在不同时间其相似会议的变化。如下图所示，WWW 在 2020 年与一些网络、数据库的会议更接近，而在 2020 年却与一些数据挖掘的会议更接近。
![image.png](https://i.loli.net/2020/04/24/6SBiqE34NleFyfQ.png)

同时，我们还验证了 HGT 可以隐性地抽取出对下游任务重要的元路径，而不需要人为定义。例如下图中的 （paper, is_published_at, venue, is_published_at-1, paper） 路径就有着最高的重要性。
![image.png](https://i.loli.net/2020/04/24/TpOK2BY5slu9vR6.png)


