---
title: Transformer 论文精读
mathjax: true
index_img: /img/zb.jpg
categories:
  - 论文
date: 2024-08-01 23:15:11
tags:
---
# 论文题目:Attention Is All You Need

# 一、动机
传统的主导序列转导模型一般基于循环神经网络（RNN）和卷积神经网络（CNN）。由于二者本质上顺序处理方式，导致计算无法并行化，效率较低。此外，RNNs 在处理长序列和长距离依赖关系时表现不佳。为了化解这些局限性，本文提出了一种全新的模型-Transformer。Transformer 模型完全摒弃了传统的循环结构，转而使用全注意力机制。注意力机制能够并行处理输入序列的所有位置，从而显著提高了计算效率，而且这种设计不仅仅提升了模型的训练速度，还提高了长序列和捕捉长序列距离依赖关系的能力。