---
title: BERT 论文精读
date: 2024-07-17 00:08:21
tags:
  - 
mathjax: true
index_img: /img/zb.jpg
categories:
  - 论文
---

# 论文题目：BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding

# 一、动机
传统语言模型在理解语言时往往是单向的，即它们只能从文本的一个方向（要么从左到右，要么从右到左）来理解信息，这种单向性限制了模型对文本整体含义的理解能力；在预训练阶段和微调阶段使用的目标函数和数据分布可能不一致，导致预训练得到的知识不能很好的迁移到下游任务。为了克服这些问题，BERT 通过深度双向 Transformer 结构来同时考虑输入文本的左侧和右侧上下文，生成更加全面和细致的语言表示；并且，采用了统一的框架进行预训练和微调，减少了预训练和微调阶段的差异，使得预训练得到的知识能够更有效的迁移到各种上下游工作[^1]。

# 二、模型
BERT 模型基于 Transformer 架构，通过注意力机制捕捉文本中的长距离依赖关系，它包含多层注意力头，能够并行处理信息，实现深度双向的语言表示。

模型的主要创新点都在pre-train方法上，即用了Masked LM和Next Sentence Prediction两种方法分别捕捉词语和句子级别的representation。

![alt text](img/Bert.jpeg)
BERT 提供了简单和复杂两个模型，对应的超参数分别如下：
- BERT<sub>BASE</sub>: L = 12, H=768, A = 12, 参数总量 110M
- BERT<sub>LARGE</sub>: L = 24, H=1024, A = 16, 参数总量 110M
- L:网络层数，即Transformer blocks的数量
- A:表示Multi-Head Attention中self-Attention的数量
- H：filter的尺寸

# 三、具体步骤

## 1、预训练任务
BERT 是一个多任务模型，它的任务由两个自监督任务组成，即 MLP 和 NSP

### 1、Task 1:Masked Language Model
Masked Language Model(MLM)和核心思想取自Wilson Taylor在1953年发表的一篇论文。
所谓MLM是指在训练的时候随机从输入语料中mask掉一些单词，然后通过上下文进行预测该单词。
该任务非常像我们在中学时期经常做的完形填空。正如传统的语言模型算法和RNN匹配那样，MLM的这个性质和Transformer的结构是非常匹配的。

在BERT的实验中，15%的WordPiece Token会被随机Mask掉。
在训练模型时，一个句子会被多次喂到模型中用于参数学习，但是Google并没有在每次都mask掉这些单词，而是在确定要Mask掉的单词之后，80%的时候会直接替换为[MASK],10%替换为其他任意单词，10%的时候保留原始Token。

### 2、Task 2：Next Sentence Prediction
Next Sentence Prediction(NSP)的任务是判断句子B是否是句子A的下文。
如果是输出"IsNext"，否则输出"NotNext"。
训练数据的生成方式是从平行语料中随机抽取的连续两句话，其中50%保留抽取的两句话，它们符合IsNext关系，另外50%的第二句话随机从语料中提取，它们的关系是NotNext。这个关系保存在图4中的[CLS]符号

## 2、输入表示
BERT的输入的编码向量(长度是512)是3各嵌入特征的单位和，如图4，这三个词嵌入的特征是

WordPiece 嵌入：WordPiece是指将单词划分成一组有限的公共子词单元，能在单词的有效性和字符的灵活性之间取得一个这种的平衡。图4示例中的playing拆成play和ing
位置嵌入(Position Embedding):位置嵌入是将单词的位置信息编码成特征向量，位置嵌入是向模型中引入单词位置关系的至关重要的一环。
分割嵌入(Segment Embedding)：用于区分两个句子，例如B是否是A的下文(对话场景，问答场景等)。对于句子对，第一个句子的特征值是0，第二个句子的特征值是1.
最后，说明一下图4中的两个特殊符号[CLS]和[SEP]，其中[CLS]表示该特征用于分类模型，对非分类模型，该符合可以省去。[SEP]表示分句符号，用于断开输入语料中的两个句子。
![alt text](img/Longshot-20240722164440.png)

## 3、微调
在海量语料训练完BERT之后，便可以将其应用到NLP的各个任务中。
对应于NSP任务来说，其条件概率表示为P=softmax(CW<sup>T</sup>),其中C是BERT输出中的[CLS]符号，W是可学习的权值矩阵,对于其他任务来说，我们也可以根据BERT的输出信息做出相应的预测。
![alt text](img/Longshot-20240722164715.png)
# 四、实验
论文在实验部分对 BERT 在 GLUE（General Lanuage Understanding Evaluation），SQuAD v1.1 （Stanford Question Answering Dataset），SQuAD v2.0等数据集上做了 11 项 NLP 任务，均展示出了 BERT 的优越性。
![alt text](img/bert-4.png)
# 五、总结
BERT 的创新之处在于其深度双向预训练的策略，使得模型在理解语言时能够同时考虑左右两侧的上下文信息。BERT 不仅提升了语言表示的质量，还简化了预训练模型到下游任务的迁移流程。
# 六、参考
[^1]:在 NLP 中，上游任务（Upstream Tasks）：通常是基础的、提供语言理解和处理能力的NLP任务，如分词、词性标注、句法分析，它们为更复杂的NLP应用打下基础；下游任务（Downstream Tasks）：依赖于上游任务输出的更高级的NLP任务，如情感分析、机器翻译、问答系统等，它们利用上游任务的输出来执行特定应用。