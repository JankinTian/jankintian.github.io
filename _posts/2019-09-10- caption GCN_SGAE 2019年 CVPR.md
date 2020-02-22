---
layout: post
title:  "Auto-Encoding Scene Graphs for Image Captioning"
date:   2019-09-10 10:18:54
categories: 论文解读
tags:    caption GCN 2019年 CVPR
---

* content
{:toc}

---
> 我们提出了场景图自动编码器（SGAE），它将**语言归纳偏差**结合到编码器解码器图像字幕框架中，用于生成更类似人类的字幕。直觉上，我们人类使用归纳偏见来构成话语中的**搭配和语境推理**。例如，当我们看到“骑自行车的人”的关系时，很自然地用“骑”代替“开”并推断“在道路上骑自行车的人”，即使“道路”也不明显。因此，利用这种偏差作为语言先验被期望有助于传统的编码器 - 解码器模型不太可能超过数据集偏差并且专注于推理。具体而言，我们使用场景图 - 有向图（G），其中对象节点通过形容词节点和关系节点连接 - 以表示图像（I）和句子（S）的复杂结构布局。在文本域中，我们使用SGAE来学习字典（D），该字典有助于在S --> G --> D --> S 通道中重建句子，其中D先编码所需的语言先验;在视觉语言领域，我们使用共享D来指导 I --> G --> D --> S 通道中的编码器 - 解码器。 由于场景图表示和共享字典，原理上跨域传递了归纳偏差。

---

>* 一个无处不在的问题从未得到实质性的解决：<Br/>
当我们将一个看不见的图像场景输入到框架中时，我们通常会得到一个关于突出物体的简单而琐碎的标题。
* Scene Graph :<br/>
1) the objects(or entities)
2) their attributes
3) their relationship
* 句子自重构网络 S --> G --> D --> S，<br/> D是一个可训练的字典，用于节点特征的再编码<br/> S-->G 模块是一个固定的现成场景图形语言解析器。<br/>D → S is a trainable RNN-based language decoder
* in the encoder-decoder training pipeline 共享 : I → G → D → S  <br/> I → G 视觉场景图检测器 <br/> G → D 使用multi-modal GCN 补充由于视觉检测不完善而缺少的必要视觉线索。
* D 可以被视为working memory，它有助于将编码的节点从 I 或 S re-key 到具有较小域间隙的更一般的表示形式。


>- Encoder-Decoder
![Encoder-Decoder](https://i.loli.net/2019/09/11/DsxCi2evE8Uz4SF.png)<br/>
- 


# Auto-Encoding Scene Graphs 
我们提出了一个场景图自动编码器(SGAE)来学习字典D，它利用场景图将语言归纳偏差从句子到句子的自重构。
并提出如何通过**自重构句子** S 如何学习 D ？<br/>


>- 场景图自动编码
![blob](https://i.loli.net/2019/09/11/ygX7UDLFp2Ja5hi.png)<br/>


### 1.  **G <-- S**   【从句子到场景图】
 >- 一个场景图就是一个元组 G=（N，ε）<br/> N 和 ε 分别是节点和边的集合。<br/>
 - 这有三种类型的节点：对象节点；属性节点；关系节点 【每个节点是一个 d 维的向量，在本论文中 d=1000】<br/>【o<sub>i</sub> 是第i个对象；r<sub>i,j</sub> 是o<sub>i</sub> 和 o<sub>j</sub> 之间的关系；a<sub>i,l</sub> 是对象o<sub>i</sub>的第 L 个属性 】
 - 边的定义：<br/>
 1）如果对象 o<sub>i</sub>有属性 a<sub>i,l</sub>，则o<sub>i</sub>到a<sub>i,l</sub> 的有向边 <br/> 
 2）关系三元组 < o<sub>i</sub> -- r<sub>i,j</sub> -- o<sub>j</sub> >;两个有向边从 o<sub>i</sub> 到 r<sub>i,j</sub> 和 从 r<sub>i,j</sub> 到 o<sub>j</sub>
 ![undefined](https://i.loli.net/2019/09/11/SbYI8PlF4Xyj12z.png)

 
### 2.  **X <-- G**   【如何将原始节点embedding入转换为一组新的 **上下文感知** embedding】
 - X 包含三个d维的embeddings：relationship embedding x<sub>r<sub>i,j</sub></sub>；object embedding x<sub>o<sub>i</sub></sub>；attribute embedding x<sub>a<sub>i</sub></sub>
 - Relationship Embedding x<sub>r<sub>i,j</sub></sub>
 ![blob](https://i.loli.net/2019/09/11/RlOzSnrext7Yfw6.png)
 - Attribute Embedding x<sub>a<sub>i</sub></sub>
 ![blob](https://i.loli.net/2019/09/11/nYCp2lfg9HdA4cy.png)
 - Object Embedding x<sub>o<sub>i</sub></sub> <br/>
 在图中 o<sub>i</sub> 可以表示 subject 或者 object， 
 ![object](https://i.loli.net/2019/09/11/gIbdjPYZqEhpsOr.png)
 
#### Q：object 和subject 的概念不是很懂
object 主语 <br/>
subject 宾语

### 3.  x<sup>^</sup><--R(X;D)  【如何学习字典D，并使用字典D进行重新编码得到 x<sup>^</sup>】
>- 我们的**核心思想**是利用工作记忆来保留运行时推理的动态知识库。
- 我们的**目标**是在语言合成中嵌入语言归纳偏差。
![blob](https://i.loli.net/2019/09/11/kghbMzItH3GPE6S.png)
![blob](https://i.loli.net/2019/09/11/34hfjy2STLtENAW.png)

 
