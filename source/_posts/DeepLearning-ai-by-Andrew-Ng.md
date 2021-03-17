---
title: DeepLearning.ai by Andrew Ng
date: 2020-09-29 19:34:29
tags: 
- deep learning
categories:
- learning
---

### 使用Word Embeding

利用已经训练好的WordEmbedding数据集可以进行迁移学习

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
EO_{6257} = e_{6257}
$$

$O_{6257}$是第6257号词的one-hot向量，$e_{6257}$是第6257号词的embedding向量，$E$是Embedding matrix，即词汇表中所有单词的embedding向量组合。

### Embedding Methods

朴素的：前几个单词的OneHot*Embedding matrix 作为参数，接入NN，然后由softmax函数输出字典中每个单词的可能性。

#### Word2Vec

