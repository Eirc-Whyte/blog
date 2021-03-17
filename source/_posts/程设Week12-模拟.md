---
title: 程设Week12-模拟
date: 2020-05-06 18:57:03
tags:
- 思维题
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

花式分段，各种分段

<!--- more-->

### A

记录一个pre，每当输入一个now，跟pre进行比较，如果不一样则cnt++，pre=now，最后cnt即为答案

### B

#### 题目描述

游戏在一个包含有n行m列的棋盘上进行，棋盘的每个格子都有一种颜色的棋子。当一行或一列 上有连续三个或更多的相同颜色的棋子时，这些棋子都被消除。当有多处可以被消除时，这些地 方的棋子将同时被消除。 一个棋子可能在某一行和某一列同时被消除

##### 输入

输入第一行包含两个整数n,m，表示行数和列数 接下来n行m列，每行中数字用空格隔开，每个数字代表这个位置的棋子的颜色。数字都大于0.

##### 输出

输出n行m列，每行中数字用空格隔开，输出消除之后的棋盘。（如果一个方格中的棋子被消除， 则对应的方格输出0，否则输出棋子的颜色编号。）

#### 题目分析

- 我的思路：先按行分段，记录每段的长度，把长度>=3的段打上tag，输出的时候为0；列同理；
- zyh的思路：直接按行遍历，`i-1``i``i+1`三个位置相同则打上tag；
- 我的思路60行，zyh思路30行

#### 代码

```c++
#include<iostream>
using namespace std;
int mp[40][40];
bool tag[40][40];
int cnt[40],now;
int n,m;
int main(){
    cin>>n>>m;
    for(int i = 0;i< n;i++){
        for(int j = 0;j< m;j++){
            cin>>mp[i][j];
        }
    }
    for(int i = 0;i<n;i++){
        int pre = mp[i][0];
        now = 0;
        cnt[0] = 1;
        for(int j = 1;j<m;j++){
            if(pre != mp[i][j] ){
                cnt[++now] = 1;
                pre = mp[i][j];
            }else cnt[now]++;
        }
        int sum = 0;
        for(int j = 0;j<=now;j++){
            if(cnt[j] >= 3){
                for(int z = 0;z < cnt[j];z++){
                    tag[i][sum+z] = 1;
                }
            }
            sum += cnt[j];
        }
    }
    for(int i = 0;i< m;i++){
        int pre = mp[0][i];
        now = 0;
        cnt[0] = 1;
        for(int j = 1;j<n;j++){
            if(pre != mp[j][i] ){
                cnt[++now] = 1;
                pre = mp[j][i];
            }else cnt[now]++;
        }
        int sum = 0;
        for(int j = 0;j<=now;j++){
            if(cnt[j] >= 3){
                for(int z = 0;z < cnt[j];z++){
                    tag[sum+z][i] = 1;
                }
            }
            sum += cnt[j];
        }
    }
    for(int i = 0;i<n;i++){
        if(tag[i][0]) cout<<0;
        else cout<<mp[i][0];
        for(int j = 1;j<m;j++){
            if(tag[i][j]) cout<<" "<<0;
            else cout<<" "<<mp[i][j];
        }
        cout<<"\n";
    }
    return 0;

}
```

### C

#### 题目描述

给定一个串，求有多少子串是delicious的

Delicious定义：对于一个字符串，我们认为它是Delicious的当且仅当它的每一个字符都属于一个 **大于1**的回文子串中。

##### 输入

输入第一行一个正整数n，表示字符串长度。接下来一行，一个长度为n**只由大写字母A、B构成的字符串**。

##### 输出

输出仅一行，表示符合题目要求的子串的个数。

**数据范围n<=3e5**

#### 题目分析

- 首先来说，单个字符的子串是不合法的；
- 也就是说，合法子串的长度length>=2；总共由n(n-1)/2个这样的子串
- 突破口很明显在“只由AB构成的字符串”
- 我们考虑不合法的串
  - AA合法，BB合法
  - AAB不合法，BAA不合法
  - 会发现只要存在\*A/B\*形式，必然合法
  - 会发现**只有AB* 和*AB形式的子串是不合法的**；
- 那么所有的这些不合法子串的长度为n*2-头尾段的长度
- **想到这，不禁直呼巧妙！！**

#### 代码

```c++
#include<iostream>
using namespace std;
long long sum[500000],cnt,n;
int main(){
    cin>>n;
    string s;
    cin>>s;
    long long ans = n*(n-1)/2;
    char pre = s[0];
    for(int i = 0;i<n;i++){
        if(pre != s[i]){
            pre = s[i];
            sum[++cnt] = 1;
        }else sum[cnt]++;
    }
    cout<<ans-n*2+cnt+sum[0]+sum[cnt];
    return 0;
}
```

