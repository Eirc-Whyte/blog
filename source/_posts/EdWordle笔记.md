---
title: EdWordle笔记
date: 2021-03-31 10:27:23
tags:
- 可视化
- 项目开发
- 词云
categories:
- 可视化
---

Edwordle项目主要算法学习笔记

<!--more-->

## 摘要

Edwordle允许用户在保持与其他单词的邻居关系时移动编辑单词，提出了可保持邻居关系的Wordle算法，并将其与带约束的刚体力学系统结合，来构造更加紧密的词云布局。

## EdWordle

> After the wordle is loaded, we first apply a customized rigid body dynamics approach, which helps us to make the layout more compact while preserving the neighborhood relationships

首先使用`customized rigid body dynamics`方法将零散的单词进行紧密化。

> To do so, after each step the rigid body dynamics step is automatically invoked again

在用户移动完某个单词之后，重新使用该方法保持单词的紧密性。

> At any time, the result can be further improved by performing a Re-Wordle

使用`local-wordle-layout`方法进行优化。

> All steps are based on our two-level box representation for the words, which allows us to create compact representations without words squeezing in between characters of other larger words.

所有的操作都基于`two-level box representation`两级矩形表示法来构建单词刚体，使得编辑过程中可以保持单词位置的连贯性以及无重叠。

## Rigid Body Dynamic Based Layout

> By representing each word as a rigid object, rigid body dynamics allow us to avoid word overlapping by enforcing non-penetration constraints

将每个单词抽象成一个刚体，有效防止单词重叠。

> Rigid bodies cannot penetrate each other, so their motion is simulated using two major components: unconstrained and constrained dynamics.

单词互相之间无法穿透，因此受到两个力：非约束力和约束力的影响。

> The former updates position and velocity in response to (outer) forces, while the latter detects collisions between bodies and creates corresponding responses

前者决定了单词的位置（和速度?），后者检测单词碰撞并产生回应。

每个刚体的状态用一个状态方程表示：
$$
Y(t) = (x(t),R(t),P(t),L(t))
$$
分别代表某时刻的位置$x$，角度$R$，线性动量$P$​和角动量$L$

#### Two-level box representation

之前的做法有两种：

- word为单位的bounding box —— 造成不紧凑
- letter为单位的bounding box —— 造成单词重叠

> The width of our word-level box is the width of the bounding box of the whole word and the height is the minimal height of all letters. 

宽度取word的宽度，高度取letter的最小高度

为了减小计算代价，我们仅用该方法计算word大小大于最大word的0.5倍的words；其他words我们仍采用直接计算word为单位的bounding box

#### Customized external forces approach

> To produce a compact layout while preserving the neighborhood, we apply two external forces to the objects: neighboring forces and central forces

为了紧凑布局同时保持邻居关系，我们采用两种力：邻居力和中心力

> While the former pushes neighboring words close to each other, the latter drags all words to the canvas center.

前者将相邻的单词相互拉近，后者将所有单词拉向canvas中心

- 邻居判定

> The body center of each word is connected to the centers of all other words. If the line segment connecting two words does not intersect a third word, these two words are considered to be neighbors. 

先将中心word连向每个word，如果连线没有穿过任何其他词，则两个词相邻

- 邻居力

$$
F^{neigh}_i =\Sigma^{n_f}_{j=1}mi\times mj/r^2_{i, j}
$$

越近力越大

- 中心力

$$
F_{i}^{c e n t}=m_{i} \times M \times r_{i, c}^{2}
$$

越远力越大

- 合成

$$
F_{i}(t)=F_{i}^{n e i g h}(t)+\alpha F_{i}^{c e n t}(t)
$$

过大的中心力可能在拉动时会破坏邻居关系，因此在给中心力加权到$\alpha = 0.1$时比较平衡

- 防抖

$$
F_{i}^{d a m p}(t)=F_{i}(t) \times g(t) \\
v_{i}(t)=v_{i}(t) \times \lambda
$$

为了防止抖动，引入两个随迭代次数逐渐衰减的函数来控制力和速度的衰减

设置迭代次数上限：80次

## local Wordle layout

经过上述流程后，可能会有些方向上的word会堆成一坨比较突出，某些方向上会有较大的空白。

<img src="https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210725161546623.png" alt="image-20210725161546623" style="zoom: 50%;" />

此时我们再做一次wordle，给突出的词找到新的位置。

- 判定离群词

> We compute the width and height of an axis-aligned bounding box b of the current word cloud and then construct a circle centered at the center of the layout and with a radius β ∗ min(widthb,heightb). All words that lie outside of this circle need to be re-placed

计算词云的bounding box，取长宽最小值乘一系数$\beta$​​​​，以此为圆心做圆，任何中心点在圆外的word都被判定为离群词，并按word大小（词频？）排序放入队列。

![image-20210725162125114](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210725162125114.png)

- 选取新位置

> we define its initial position as the midpoint of the line segment that connects the mass centers of the current word and the center of the entire word cloud.
>
> Then we find k candidate positions along the spiral and select the one that preserves the largest number of neighborhood relations on its new position. If more than one position preserves the same number of neighborhoods, we pick the one that is found first, because it is closer to the word cloud center.

以词云中心点与待调整词的重心的中点位置出发做螺旋线，选取$k$​​个候选点，从中挑出能够保持**最多**原来的邻居关系的点作为新位置；如果有多个这样的点，我们选取编号最小的点，因为它更靠近中心。

论文中$\beta = 0.8, k = 20$​，允许用户自定义。

