---
layout: post
title:  "Aligning Linguistic Words and Visual Semantic Units for Image Captioning"
date:   2019-09-02 10:18:54
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
>* 1、提出来视觉语义单元(visual senmantic units)【VSUs】 作为综合表达视觉内容，利用结构化图，即语义图和几何图，和GCNs对其进行统一表示.
>
>VSUs包含三个部分**object units, attribute units, 和 relationship units**.
>
>句子包含syntactic units（句法单元）描述**对象**(e.g. nouns phrase)，**属性**(e.g. adjectives)，和**关系**(e.g. verb, prepositions)
>
>* 2、视觉语言的关联和设计是一种对语言单词和视觉语义单元进行分层排列的文本门控注意力模块。

