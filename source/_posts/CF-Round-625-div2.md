---
title: 'CF Round# 625 (div2)'
date: 2020-03-02 23:36:35
tags: 
- CodeForces
categories:
- 刷题
thumbnail: /gallery/cf.png
---

跟kkgg的cf康复赛（1/n）

多年不打cf了，依稀记得上次打还是高中某个不写作业的晚上顶着困到空白的脑子怼英文题面。

<!--more-->

### T1 [Contest for Robots](https://codeforces.com/contest/1321/problem/A)

#### 题目描述

两个机器人答题，已知n场比赛机器人的胜负情况，通过安排每场比赛的分数可以让机器人"Robo-Coder Inc."恰好胜过机器人"BionicSolver Industries"。（“恰好”的定义为R比B的最终得分多1分）问这样安排中一场比赛分数可能的最小的最高分数能安排多少。

#### 思路描述

在某场比赛上：

- R赢B输，则R比B多挣这场比赛的分数
- R输B赢，则R比B少挣这场比赛的分数
- RB同输同赢，不影响最终比赛结果。

记 
$$
Score_{R} = R赢B输的比赛分数和\\
Score_{B} = B赢R输的比赛分数和
$$
那么只要控制$Score_{R} = Score_{B}+ 1$  即可恰好使R胜利。

因为要求最小，所以$Score_{B}$的各场比赛分数必然是1分，那么$Score_{R} = \lceil \frac{Score_{B}+1}{Bwin} \rceil = \frac{Score_{B}}{Bwin}+1$

### T2 [Journey Planning](https://codeforces.com/contest/1321/problem/B)

#### 题目描述

有n个城市，每个城市的下标为$c_{i}$，并且每个城市有一个beauty值，每到达一个城市之后选择下一个城市必须满足约束条件：$c_{i+1}-c_{i} = b_{c_{i+1}}-b_{c_{i}}$才能到达。问如何选择才能获得最大的总beauty值，并输出最大值。

#### 思路描述

按着题目看了半天才看懂意思，其实就是一个简单的公式变换题目，跟顺序并无卵关系。

把约束条件稍微变一下就成了$c_{i+1}-b_{c_{i+1}} = c_{i}-b_{i}$

意思就是：把每个城市下标减去beauty值相同的扔一个桶里，最后找到最大的就可。

当然，可能会减出负数，注意补上beauty的最大值。

### T3 [Remove Adjacent](https://codeforces.com/contest/1321/problem/C)

#### 题目描述

给定一个字符串$s$，其中字符$s_{i}$可以被移除当且仅当相邻的字符是$s_{i}$的*previous letter*。例如b的*previous* *letter* 是a，c的*previous letter*是b以此类推。第一个和最后一个字符只有一个相邻的字符，其他位置的字符均有两个相邻字符。问给定的字符串最多可以移除多少个字符。

#### 思路描述

一开始以为是个搜索，仔细想了想并不是，因为*previous letter*的定义决定了从z到a移除一定是最佳的。

如果从中间开始移除，例如在abc里先移除b，那么c显然移除不了。所以直接贪心写就可。

需要注意的是，当在串中找某个字符并移除后，可能有相同的字符依然符合移除条件，那么就继续移除直到不能移除为止，再进行下一个字符的寻找。

### T4 [Navigation System](https://codeforces.com/contest/1321/problem/D)

tag：最短路

### T5 [World of Darkraft: Battle for Azathoth](https://codeforces.com/contest/1321/problem/E)

tag：two pointers 、前缀和

### T6 [Reachable Strings](https://codeforces.com/contest/1321/problem/F)

tag：hash



### 最终结果（3/6）

### 错误小结：

- 写了if到底写不写else，这是一个问题

- 减法下标溢出，cmp里不开long long