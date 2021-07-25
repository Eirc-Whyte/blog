---
title: DeepLearning.ai by Andrew Ng
date: 2020-09-29 19:34:29
tags: 
- deep learning
categories:
- 深度学习
---

### 使用Word Embeding

利用已经训练好的WordEmbedding数据集可以进行迁移学习

<!--more-->

Transfer learning and word embeddings：

1. 自己训练（网上下载）embedding
2. 将embedding迁移到需要训练的数据集上
3. 选择：如果新数据集很大，可以继续使用新数据调整embedding。

广泛用于基本的NLP任务

relation to face encoding：

- face encoding ：test是不可预知的
- word embedding ： test是已知的（词汇表中的所有单词）

### 词嵌入的特性

通过两词向量之差距离判断相似
$$
e_{man} - e_{woman} = \left[
\begin{array}[l] 
-2 \\
0\\
0\\
\vdots
\end{array}
\right] \approx e_{king} - e_{queen}
$$
其实是在**高维空间形成的向量相似**

那么就是要找一个单词$w$
$$
argmax_w\ \ similarity(e_w,e_{king}-e_{man}+e_{women})
$$
cosine similarity：
$$
similarity(u,v) = \frac{u^Tv}{||u||_2||v||_2}
$$

### Embedding matrix

$$
E\cdot O_{6257} = e_{6257}
$$

$O_{6257}$是第6257号词的one-hot向量，$e_{6257}$是第6257号词的embedding向量，$E$是Embedding matrix，即词汇表中所有单词的embedding向量组合。

但在实际情况下一般不这么做。因为 one-hot 向量维度很高，且几乎所有元素都是 0，这样做的效率太低。因此，实践中直接用专门的函数查找矩阵 E 的特定列。

### Embedding Methods

朴素的：前几个单词的OneHot*Embedding matrix 作为参数，接入NN，然后由softmax函数输出字典中每个单词的可能性。

### Word2Vec

#### skip-gram model

给定一个中心词$c$，要预测另一个单词（背景词$t$）在它附近出现的概率：

$$
p(t|c) = \frac{exp(\theta_t^T e_c)}{\sum_{j\in V}exp(\theta_j^T e_c)}
$$

$\theta_t$是与​背景词有关的参数（背景词的词向量），$e_c$是中心词的词向量，这里通过softmax函数计算条件概率，即：在$c$为中心词的条件下，$c$附近出现的所有单词中出现$t$的概率。

如果上下文窗口设置为$m$，则对上下文的单词集合求最大似然即可：
$$
\prod_{-m \leq j \leq m,\ j \neq 0} P((c+j) \mid c)
$$
对于一个长度为T的文本序列来说，似然函数应该是：
$$
\prod_{c=1}^{T}\prod_{-m \leq j \leq m,\ j \neq 0} P((c+j) \mid c)
$$
最大化似然函数，对于一个单词可以得到它的中心词向量和背景词向量，一般采用**中心词向量**作为词的表征向量。

#### 负采样

给定一对词语，判断他们两个是否接近。

转化成分类问题：

将规模巨大的softmax问题转化成根据1个正样本和k个随机负样本进行k+1分类的分类问题；

如何选取负样本？

根据频率、随机抽取、启发式抽取：根据词频的3/4次幂抽样

#### GloVe

最优化目标：
$$
minimize \ (\theta^T_ie_j - logX_{ij})^2
$$
where, j 是中心词，i是背景词 $X_{ij}$是所有中心词$j$附近$i$出现的次数。

为了避免$X_{ij}=0$导致函数值->$-\infin$的问题，需要在前面加一个权值项$f(X_{ij})$，使得当$X_{ij}=0$时整体值为0。同时我们还可以通过控制权重项来给常用词和不常用词一个合适的权重.

此时$\theta_i$和$e_i$的数学意义上是对称的（不是一个背景词向量一个中心词向量了）就可以直接取平均值来作为最后词嵌入的结果。

<待续..>
