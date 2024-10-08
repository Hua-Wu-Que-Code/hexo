---
title: SoftMax回归
mathjax: true
index_img: /img/zb.jpg
categories:
  - 深度学习
date: 2024-07-23 23:26:23
tags:
---
在线性回归中,说明了回归可以用于预测的问题,但事实上,回归也可以用于分类问题.

通常，机器学习实践者用分类这个词来描述两个有微妙差别的问题： 1. 我们只对样本的“硬性”类别感兴趣，即属于哪个类别； 2. 我们希望得到“软性”类别，即得到属于每个类别的概率。 这两者的界限往往很模糊。其中的一个原因是：即使我们只关心硬类别，我们仍然使用软类别的模型。

# 分类问题
我们从一个图像分类问题开始。 假设每次输入是一个$2 \times 2$的灰度图像。 我们可以用一个标量表示每个像素值，每个图像对应四个特征$x_1,x_2,x_3,x_4$。 此外，假设每个图像属于类别“猫”“鸡”和“狗”中的一个。接下来，我们要选择如何表示标签。 我们有两个明显的选择：最直接的想法是选择$y \in {1,2,3}$其中整数分别代表狗猫鸡。 这是在计算机上存储此类信息的有效方法。 如果类别间有一些自然顺序， 比如说我们试图预测婴儿儿童青少年青年人中年人老年人， 那么将这个问题转变为回归问题，并且保留这种格式是有意义的。
 
但是一般的分类问题并不与类别之间的自然顺序有关。 幸运的是，统计学家很早以前就发明了一种表示分类数据的简单方法：独热编码（one-hot encoding）。 独热编码是一个向量，它的分量和类别一样多。 类别对应的分量设置为1，其他所有分量设置为0。 在我们的例子中，标签$y$将是一个三维向量， 其中$(1,0,0)$对应于“猫”、$(0,1,0)$对应于“鸡”、$(0,0,1)$对应于“狗”：
$$
y \in {(1,0,0),(0,1,0),(0,0,1)} \tag{1}
$$

# 网络架构
为了估计所有可能类别的条件概率，我们需要一个有多个输出的模型，每个类别对应一个输出。 为了解决线性模型的分类问题，我们需要和输出一样多的仿射函数（affine function）。 每个输出对应于它自己的仿射函数。 在我们的例子中，由于我们有4个特征和3个可能的输出类别， 我们将需要12个标量来表示权重（带下标的$w$）， 3个标量来表示偏置（带下标的$b$）。 下面我们为每个输入计算三个未规范化的预测（logit）：$o_1,o_2,o_3$.
$$
\begin{align}
o_1 = x_1 w_{11} + x_2 w_{12} + x_3 w_{13} + x_4 w_{14} + b_1, \\
o_2 = x_1 w_{21} + x_2 w_{22} + x_3 w_{23} + x_4 w_{24} + b_2, \tag{2} \\
o_3 = x_1 w_{31} + x_2 w_{32} + x_3 w_{33} + x_4 w_{34} + b_3,  
\end{align}
$$
我们可以用神经网络图1来描述这个计算过程。 与线性回归一样，softmax回归也是一个单层神经网络。 由于计算每个输出$o_1,o_2,o_3$取决于所有输入$x_1,x_2,x_3,x_4$所以softmax回归的输出层也是全连接层。
![图 1 softmax回归是一种单层神经网络](https://raw.githubusercontent.com/Hua-Wu-Que-Code/picture/main/uPic/mV9rtQ.png)

为了更简洁地表达模型，我们仍然使用线性代数符号。 通过向量形式表达为$\mathbf{o} = \mathbf{W} \mathbf{x} + \mathbf{b}$， 这是一种更适合数学和编写代码的形式。 由此，我们已经将所有权重放到一个$3\times4$矩阵中。 对于给数据样本的特征$x$， 我们的输出是由权重与输入特征进行矩阵-向量乘法再加上偏置$b$得到的。

# sotfmax运算
现在我们将优化参数以最大化观测数据的概率。 为了得到预测结果，我们将设置一个阈值，如选择具有最大概率的标签。

我们希望模型的输出$\hat{y}_j$可以视为属于类$j$的概率， 然后选择具有最大输出值的类别$argmax_j y_j$作为我们的预测。 例如，如果$\hat{y}_1 \hat{y}_2 \hat{y}_3$分别为0.1、0.8和0.1， 那么我们预测的类别是2，在我们的例子中代表“鸡”。

然而我们能否将未规范化的预测$o$直接视作我们感兴趣的输出呢？ 答案是否定的。 因为将线性层的输出直接视为概率时存在一些问题： 一方面，我们没有限制这些输出数字的总和为1。 另一方面，根据输入的不同，它们可以为负值。 

要将输出视为概率，我们必须保证在任何数据上的输出都是非负的且总和为1。 此外，我们需要一个训练的目标函数，来激励模型精准地估计概率。 例如， 在分类器输出0.5的所有样本中，我们希望这些样本是刚好有一半实际上属于预测的类别。 这个属性叫做校准（calibration）。

社会科学家邓肯·卢斯于1959年在选择模型（choice model）的理论基础上 发明的softmax函数正是这样做的： softmax函数能够将未规范化的预测变换为非负数并且总和为1，同时让模型保持 可导的性质。 为了完成这一目标，我们首先对每个未规范化的预测求幂，这样可以确保输出非负。 为了确保最终输出的概率值总和为1，我们再让每个求幂后的结果除以它们的总和。如下式：
$$
\hat{\mathbf{y}} = softmax(\mathbf{o}) 其中 \hat{y}_j = \frac{exp(o_j)} {\sum_k exp(o_k)} \tag{3}
$$
这里，对于所有的$j$总有$0 \leq \hat{y}_j \leq 1$。 因此，$\hat{y}$可以视为一个正确的概率分布。 softmax运算不会改变未规范化的预测$o$之间的大小次序，只会确定分配给每个类别的概率。 因此，在预测过程中，我们仍然可以用下式来选择最有可能的类别。
$$
\arg\max_j \hat{y}_j = \arg\max_j \hat{o}_j \tag{4}
$$ 
尽管softmax是一个非线性函数，但softmax回归的输出仍然由输入特征的仿射变换决定。 因此，softmax回归是一个线性模型（linear model）.

# 小批量样本的矢量化
为了提高计算效率并且充分利用GPU，我们通常会对小批量样本的数据执行矢量计算。 假设我们读取了一个批量的样本$\mathbf{X}$,其中特征维度（输入数量）为$d$，批量大小为$n$。 此外，假设我们在输出中有$q$个类别。 那么小批量样本的特征为$\mathbf{X} \in \mathbb{R}^{n\times d}$， 权重为$\mathbf{W} \in \mathbb{R}^{d\times q}$,偏置为$\mathbf{b} \in \mathbb{R}^{1\times q}$.softmax回归的矢量计算表达式为：
$$
\begin{align}
O &= XW + n  \tag{5} \\
\hat{\mathbf{Y}} &= softmax(\mathbf{O}).
\end{align}
$$
相对于一次处理一个样本， 小批量样本的矢量化加快了$\mathbf{X Y}$的矩阵-向量乘法。 由于$\mathbf{X}$中的每一行代表一个数据样本， 那么softmax运算可以按行（rowwise）执行： 对于$\mathbf{O}$的每一行，我们先对所有项进行幂运算，然后通过求和对它们进行标准化。 在 (5)中，$\mathbf{ O = XW + n}$的求和会使用广播机制， 小批量的未规范化预测$\mathbf{O}$和输出概率$\hat{\mathbf{Y}}$都是形状为$n\times q$的矩阵。

# 损失函数

## 对数似然
softmax函数给出了一个向量$\hat{y}$， 我们可以将其视为“对给定任意输入$x$的每个类的条件概率”。 例如，$\hat{y}_1 = P(y=猫|x)$。 假设整个数据集${\mathbf{X,Y}}$具有$n$个样本， 其中索引$i$的样本由特征向量$x^{(i)}$和独热标签向量$y^{(i)}$组成。 我们可以将估计值与实际值进行比较：
$$
P(Y|X) = \prod_{i=1}^n P(y^{(i)}|x^{(i)}) \tag{6}
$$

根据最大似然估计,我们最大化P($\mathbf{Y|X}$),相当于最小化负对数似然:
$$
-logP(\mathbf{Y|X}) = \sum_{i=1} ^n \sum_{i=1}^n - logP(y^{(i)}|x^{(i)})  = \sum_{i=1}^n l(y^{(i)}|\hat{y}^{(i)}) \tag{7}
$$

其中,对于任何标签 y 和模型预测$\hat{y}$,损失函数为
$$
l(y,\hat{y}) = - \sum_{j=1}^q y^{(i)} log\hat{y}^{(i)}. \tag{8}
$$
(8)中的损失函数 通常被称为交叉熵损失（cross-entropy loss）。 由于$y$是一个长度为$q$的独热编码向量， 所以除了一个项以外的所有项$j$都消失了。 由于所有\hat{y^}^{(i)}都是预测的概率，所以它们的对数永远不会大于0。 因此，如果正确地预测实际标签，即如果实际标签$P(y|x) = 1$， 则损失函数不能进一步最小化。 注意，这往往是不可能的。 例如，数据集中可能存在标签噪声（比如某些样本可能被误标）， 或输入特征没有足够的信息来完美地对每一个样本分类。


## softmax 及其导数
由于softmax和相关的损失函数很常见， 因此我们需要更好地理解它的计算方式。 将 (3)代入损失 (8)中。 利用softmax的定义，我们得到：
$$
\begin{align}
l(y,\hat{y}) &= -\sum_{j=1}^q y_j log\frac{exp(o_j)}{\sum_{k=1}^{q} exp(o_k)} \tag{9} \\
&= \sum_{j=1}^q y_j3,  
\end{align}
$$


# 模型预测和评估
在训练softmax回归模型后，给出任何样本特征，我们可以预测每个输出类别的概率。 通常我们使用预测概率最高的类别作为输出类别。 如果预测与实际类别（标签）一致，则预测是正确的。 在接下来的实验中，我们将使用精度（accuracy）来评估模型的性能。 精度等于正确预测数与预测总数之间的比率。
