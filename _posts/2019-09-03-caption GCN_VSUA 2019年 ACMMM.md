---
layout: post
title:  "Aligning Linguistic Words and Visual Semantic Units for Image Captioning"
date:   2019-09-03 10:18:54
categories: 论文解读
tags:    caption GCN 2019年 ACMMM
---

* content
{:toc}

---
图像标题试图生成一个由几个语言单词组成的句子，用于描述图像中的**对象、属性和交互**，在本文中表示为视觉语义单位。基于这一观点，我们提出了一种基于图形卷积网络(GCNS)的**语义和几何对象交互显式建模**方法，并充分利用了语言词与视觉语义单元之间的一致性。特别地，我们构造了一个**语义图**和一个**几何图**，其中每个节点对应于一个视觉语义单元，即一个对象、一个属性或两个对象之间的语义(几何)交互。因此，每个单元的语义(几何)上下文感知嵌入是通过相应的GCN学习处理器获得的。在每一时间步骤中，上下文门控attention模块将视觉语义单元的嵌入作为输入，首先决定当前单词的视觉语义单元(对象、属性或交互)的类型，然后在该类型下找到最相关的视觉语义单元，从而对当前单词与这些单元进行分层对齐。

---

> 本文的主要贡献：
>
>* 1、提出来视觉语义单元(visual senmantic units)【VSUs】 作为综合表达视觉内容，利用结构化图(structured graphs)，即语义图(semantic graph)和几何图(geometry graph)，和GCNs对其进行统一表示.
>
> VSUs包含三个部分**object units, attribute units, 和 relationship units**.
>
> 句子包含syntactic units（句法单元）描述**对象**(e.g. nouns phrase)，**属性**(e.g. adjectives)，和**关系**(e.g. verb, prepositions)
>
>* 2、视觉语言的关联和设计是一种对语言单词和视觉语义单元进行分层排列的文本门控注意力模块。

# Approach
![总体结构图](https://i.loli.net/2019/09/04/1NBrQ4mKLX2swdt.png)

本文认为视觉内容由以下三个部分组成：

- Object units(O)【对象检测】：图像中的个别对象实例。
- Attribute units(A)【属性分类】：每个对象的特性。
- Relationship units(R)【关系检测】：对象对之间的关系

语义图和几何图是通过将每个 VSU 作为节点和两个 VSU 之间的连接作为边缘来构造的。

GCNs用来学习每个节点/vsus的context-aware embeddings。

context gated attention fusion module，将每个单词与嵌入的vsus分层对齐。

## 视觉语义单元的图形表示
### Visual Semantic Units Detection
- object detector：使用Faster R-CNN作为目标检测器。
- attribute classifier：使用多层感知机 multi-layer perceptron(MLP)作为属性分类
- relationship detector：使用MOTIFNET作为语义关系检测

最后我们获得一系列的对象、属性和关系。

* O<sub>i</sub> 表示the i-th object
* a<sub>i,k</sub>  表示O<sub>i</sub>的第 k 个属性
* r<sub>i,j</sub>  表示O<sub>i</sub>和O<sub>j</sub>之间的关系
* < O<sub>i</sub>，r<sub>i,j</sub>，O<sub>j</sub> > 代表的意思是<subject, predicate, object>

### Graph Construction
每一个属性节点 a<sub>i,j</sub> 都会和对象节点进行有向连接（ o<sub>i</sub> —> a<sub>i,j</sub> ）表示“对象 o<sub>i</sub> 具有属性a<sub>i,j</sub>”

然而将关系表示为节点或者边，在图中没有具体确定。

- 传统的方法是把关系表示为对象对之间的边的连接二，这种方式只学习每个节点（对象）的向量表示，并以GCN参数的形式隐式编码边缘（关系）信息。而关系信息不能直接建模。
- 关系信息应该和对象信息一样，应为解码器中多做的决策提供信息。
- 本文的方法：将关系信息作为节点，建立一个双向节点（ o<sub>i</sub> -> r<sub>i,j</sub> -> o<sub>j</sub> ）如图3所示

![blob](https://i.loli.net/2019/09/04/xmypOasBd1oX3Zh.png)
![blob](https://i.loli.net/2019/09/04/GQfv6eIPyNADn3B.png)
### Semantic Graph and Geometry Graph
两种类型的视觉关系：语义关系--> semantic graph和几何关系--> geometry graph。

- 语义关系：揭示两个对象之间的内在行为和相互关系。 eg"woman riding horde"
- 几何关系：将两个regions 连接到一起。  eg""

* 无向相对空间关系< o<sub>i′</sub>, r<sub>i′j′</sub>, o<sub>j′</sub> >,  
o<sub>i′</sub>是o<sub>i</sub>和o<sub>j</sub>中最大的一个。
o<sub>j′</sub>是o<sub>i</sub>和o<sub>j</sub>中最小的一个。
如果不使用完全连接的图形，则在给定阈值内，如果它们的交点超过(IOU)和相对距离，我们将指定两个对象之间的几何关系。

## Embedding Visual Semantic Units with GCN
### Node Features
> 我们整合了三种内容线索：（1）visual appearance cue【视觉外观线索】（2）semantic embedding cue【语义嵌入线索】（3）geometry cue【几何图形线索】

![visual appearance cue](https://i.loli.net/2019/09/04/Wyc4gib9IsCxhUG.png)
![semantic embedding cue](https://i.loli.net/2019/09/04/uvwprnsg9ZDOU1h.png)
![geometry cue](https://i.loli.net/2019/09/04/tw7gKXHxrBTZieu.png)
![blob](https://i.loli.net/2019/09/04/xaMro7dtZKXSlOw.png)

### Node Embedding with GCNs
![undefined](https://i.loli.net/2019/09/04/nSyuDwZVUb8A1Es.png)

## 将文本单词与视觉语义单位对齐
平衡目标、属性和关系三种特征。