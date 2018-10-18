---
title: KBQA Semantic parse方法
comments: true
mathjax: true
categories:
  - KBQA 
tags:
  - Semantic parse
  - KBQA
  - entity link+CNN
 
---
### 参考文献：Yih S W, Chang M W,He X, et al. Semantic parsing via staged query graph generation: Questionanswering with knowledge base[J]. 2015.
- 学习笔记：https://cloud.tencent.com/developer/article/1086383
- 该paper来自微软，ACL 2015年的Outstanding paper，目前KB-QA效果最好的paper之一 
# 解决的问题
为KBQA问题提出一个语义解析框架，即staged query graph generation
# 创新点
1. staged
2. logical form query = KB中的query graph generation
3. 使用CNN得到word representation
# 相关知识
### KB-freebase
![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/KB.png)
- CVTs(Compound Value Types): Represent complex data where each entry consists of multiple fields. CVTs can be thought of as a topic that does not require you to make a display name.
### Query Graph
![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/logicform.png)
- 确定的实体（gounded entity）：知识库中实际存在的实体
- 存在变量 (existential variable)：知识库中未指定的一个或一组实体，可以看成是中间变量
- 变量(lambda variable)：知识库中未指定是哪个的一个或一组实体，即问题最终的答案
- 聚合函数(aggregation function)：对实体的一些属性进行数值运算，以达到筛选答案的作用
# Semantic parsing framework
![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/staged.png)
### 1. 选择topic entity : entity linking system
> Q: Who first voiced [Meg] on [Family Guy]?

![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/se.png)
### 2. 识别核心推理链：CNN
![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/sp.png)
&nbsp;&nbsp;要评估{cast-actor, writer-start, genre}这三个序列哪个最接近问题中"Family Guy"和"Who"的关系。这个问题可以约减为求语义相似性的神经网络。
![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/cnn.png)
> 首先使用词哈希将一个词变成以"#"字母三元组,作为分割的三元组，如"Who"可变成"#-w-h","w-h-o","h-o-#",然后使用卷积层将字母三元组投影成具有上下文信息的特征向量，然后使用池化将特征变为固定长度，最后通过一个卷积层输出非线性语义特征。

这相比于embedding方法有两个优势。
1. 词哈希控制了输入的长度，可扩展到大规模词汇量;
2. 有卷积和最大池化的深度网络有更好的表达能力更好。
### 3. Add constraints/aggregations
- constraints:question中提到的其他实体
- aggregations：“最新”“第一”等，手写规则
# reward fuction
![Aaron Swartz](https://raw.githubusercontent.com/wyy0206/Markdown-photos/master/semantic%20parsing/reward.png)
### 三种CNN
> PatChain& QuesEP：learned using pairs of the question and the inferential chain of the parse in the training data
1. PatChain：pattern V.S. predicate sequence；
2. QuesEP：topic entity + predicate sequence V.S. question；
> ClueWeb： learned using the Freebase annotation of ClueWeb corpora. 
For two entities in a sentence that can be linked by one or two predicates, we pair the sentences and predicates to form a parallel corpus to train the CNN model.

3. ClueWeb: pattern V.S. predicate sequence
# result

