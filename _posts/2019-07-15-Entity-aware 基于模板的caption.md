---
layout: post
title:  "Entity-aware Image Caption Generation"
date:   2019-07-15 22:14:54
categories: 论文解读
tags: 基于模板的caption 2018年 EMNLP
---
* content
{:toc}

>* **解决的问题：**生成的描述缺少特殊信息。Language model 不会产生特殊的背景信息。
>* **input：**输入图像和主题标签（hashtags）
>* **策略：**
（1）使用CNN和LSTM 根据输入图像生成模板caption
（2）使用基于Knowledge Grape的集体推理算法，在模板中填充通过主题标签检索的特定命名实体。
>* **方法：**
    ①：生成带槽的模板句子（用实体类型替换标题中的实体）
    ②：给定图像的关联标签（应用EDL【实体应用和发现】提前特定实体）
    ③：根据实体类型和频率为每个槽选择合适的candidates

槽用来合并特定信息。
我们将插槽定义为具有相同类型的实体的占位符。在使用细粒度来命名插槽

>*本文结构图
![](https://i.loli.net/2019/07/15/5d2c5fa36859144590.png)

## Template Caption Generation
>* 数据标签预处理，得到模板标签
![](https://i.loli.net/2019/07/15/5d2c5fa17dd2712978.png)

### 标签预处理(preprocessing)
（1）删除括号中的单词，因为它们通常表示辅助信息，并且不与视觉概念对齐在图像中; 
（2）如果标题包含多个句子，我们选择较长的句子。根据我们的观察，较短的句子通常起到背景介绍的作用，与图像中的关键内容不一致; 
（3）删除少于10个令牌的字幕，因为它们往往没有足够的信息。新闻图片标题的平均长度为37个令牌。

### 数据压缩(compression)
（1）在预训练的caption上使用Stanford dependency parser (De Marn- effe and Manning, 2008)
（2）我们使用广度优先搜索（breadth-first search），通过<governor, grammatical relations, dependent>三元组（统治者、语法关系、依赖）从根开始遍历解析树。
我们决定根据它与governor的语法关系来维持它的依附性或不依附性。

### 泛化(generalization)
1）我们将Stanford CoreNLP名称标记符应用于caption，以提取以下类型的实体提及：人员，位置，组织和杂项。
（2）我们使用英语实体链接算法，将实体提及链接到DBpedia并检索它们的细粒度类型。（choose the higher level）
如果实体没有连接到DBpedia，则使用粗粒度(coarse-grained)实体类型。
>* 模板生成结构图 CNN+LSTM
![](https://i.loli.net/2019/07/15/5d2c5fa04de0f63585.png)

## Entity -->slot
通过上下文信息选择候选实体
候选实体的选择：采用Quantified Collective Validation (QCV)[量化集体验证]算法，该算法构造了许多候选图并对这些候选图执行集体验证，以便为这些候选图选择合适的实体。
>* EDL结构图的**QCV方法**
![](https://i.loli.net/2019/07/15/5d2c5fa10d98d58444.png)
>* ①候选实体检索（retrieval）
标签(tags)：event-related（与事件相关的标签）
   entity-related（与实体相关的标签）
   topic-related（与主题相关的标签）
具体相同标签可共享实体
因此，给定图像及其标签，我们基于照片的拍摄日期通过具有相同标签的窗口大小来检索来自Flickr的图像，然后利用伴随检索到的图像的文本信息作为上下文。
last：根据上下文中的频率对candidate进行排序，选择top5
>* ②量化集体验证 -->(实体链接的问题)
目的：从给出的候选实体中选择实体
利用QCV算法为给定的一组slot构建多个候选图，其中替换到slot中的候选实体的每个组合产生不同的图。
Last：计算总的边缘概率，选择最大的候选组合
>* ③后处理
将图片中的时间或者地点信息添加到生成的标题中作为后期处理，
对于那些不能通过名称填充的插槽，我们使用通用词来替换它们

* 原始caption 、粗粒度模板 和细粒度模板结果
![](https://i.loli.net/2019/07/15/5d2c5fa284b4e16334.png)
>* 原始caption 、粗粒度模板 和细粒度模板结果对比表格
![](https://i.loli.net/2019/07/15/5d2c5fa05d86d77354.png)
根据结果对比表格可以明显的看出粗粒度的结果优于细粒度的结果
* 粗粒度优于细粒度的原因：
    ①：精简模板依赖于EDL，错误链接导入噪声; 
    ②：命名实体通常有多种类型，但我们只在泛化期间选择一种。


>* entity-aware 结果图
![](https://i.loli.net/2019/07/15/5d2c5fa38135620914.png)
由结果图中可以看出，
A：是比较好的结果
B：出现填充实体的关系错误的现象
C：出现生成Template 错误的现象

## 出现的问题及改进方法
* （1）填充实体的关系错误
解决方法：合并当模型在具有实体的槽中填充时的关系信息。
* （2）生成的Template 错误
解决方法：将来自相关标签的信息（例如标签的数量和与标签相关的命名实体类型）合并为模板标题生成期间的特征，以使生成的模板根据上下文动态地改变。

