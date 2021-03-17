---
title: 程设Week3 作业
date: 2020-03-06 20:28:44
tags:
- 剪枝
- 贪心
categories: 
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

本周剪枝、贪心专题训练

<!--more-->

## T1[ 选数问题](https://vjudge.net/contest/359837#problem/A)

### 题目描述

给出n个数，从中选出和为S的K个数，问有几种方案？

#### 输入

第一行T表示数据组数；第二行n，K，S；第三行n个数；

$k<=n<=16$

#### 输出

对于每组输入，输出对应的方案数。

### 题目分析

(看数据的话直接暴力好像也能过的样子？)

- 剪枝专题当然得用剪枝。
- 可行性剪枝：如果当前状态还需要选k个，和为s，那么满足下列条件之一时：
  - $k\times Max \le s$   取k个剩下数的最大值都**达不到s**
  - $k\times Min \ge s$  取k个剩下数的最小值都会**比s大**

就可以被剪掉

- 小trick：如果动态求剩下的数的最小最大值的话，虽然最多只有16个数，但是复杂度也是O(n^2)级别，而且还得单独写函数。不如一开始就把这些数排下序，然后从左到右递归选或不选就好。这样*Min*就是当前的数，*Max*就可以取最后的数。

### 代码

```c++
int num[100],n,ans;
void dfs(int k,int s,int pos){
    if(s == 0 && k == 0){
        ans++;
        return;
    }
    if(s > k*num[n-1] || s < k*num[pos]){
        return;
    }
    dfs(k-1,s-num[pos],pos+1);
    dfs(k,s,pos+1);
}
int main(){
    int T;
    cin>>T;
    while(T--){
        int k,s;
        ans = 0;
        cin>>n>>k>>s;
        for(int i = 0;i < n;i++){
            cin>>num[i];
        }
        sort(num,num+n);
        dfs(k,s,0);
        cout<<ans<<"\n";
    }
    return 0;
}
```

## T2[ 区间选点](https://vjudge.net/contest/359837#problem/B)

### 题目描述

数轴上有 n 个闭区间 $[a_i, b_i]$。取尽量少的点，使得每个区间内都至少有一个点（不同区间内含的点可以是同一个）

#### 输入

第一行1个整数N（N<=100）
第2~N+1行，每行两个整数a,b（a,b<=100）

#### 输出

一个整数，代表选点的数目

### 题目分析

- 如果每个线段独立，那么最终结果显然是n；如果存在两个线段重合，那么结果是n-1；

- 先把每个线段按照右端点从小到大排序。（左端点排序也能做，稍微麻烦一点）
- 然后从第一个线段开始，每次把后边线段左端点在自己右端点左边的情况找出来，并且令答案-1；
- 单纯的上述处理结果是错误的，因为考虑到下面这种情况：

![image-20200306211717652](程设Week3-作业/image-20200306211717652.png)

结果应该是2，但是上述算法得出1.因为A在之前的B、C的遍历过程中被遍历到了2次。

那我们每次找到符合上述要求的线段之后，还要给他打上（已遍历）的标记，避免后续重复遍历。

### 代码

```c++
struct rec{
    int a,b;
}p[200];
bool vis[200];
bool cmp(const rec& a,const rec& b){
	return a.b < b.b;
}
int main(){
    int n ;
    cin>>n;
    for(int i = 1;i<=n;i++){
        cin>>p[i].a>>p[i].b;
    }
    sort(p+1,p+1+n,cmp);
    int ans = n;
    int i = 1;
    while(i<=n){
        if(!vis[i]){
            for(int j = i+1;j <= n;j++){
                if(!vis[j] && p[j].a<= p[i].b){
                    ans--;
                    vis[j] = 1;
                }
            }
        }
        i++;
    }
    cout<<ans;
    return 0;
}
```

## T3 [区间覆盖](https://vjudge.net/contest/359837#problem/C)

### 题目描述

数轴上有 $n (1\le n\le 25000)$个闭区间 $[ai, bi]$，选择尽量少的区间覆盖一条指定线段 $[1, t]$, 其中$1\le t\le 1,000,000$
覆盖整点，即$(1,2)+(3,4)$可以覆盖$(1,4)$
不可能办到输出-1

#### 输入

多组样例输入

每组样例第一行为$n,t$

然后输入n个区间

#### 输出

每次输出单独的一行，选择区间的数目，不能覆盖则输出-1

### 题目分析

- 贪心每次选取能向右拓展最长的线段即可。
- 先按照左端点排序（这次右端点排序不能做了），然后从第一个线段开始，找到后边所有线段中左端点在自己右端点左边的线段，从中取右端点离自己右端点最远的，将其作为下一个线段重复上述操作。
- 因为是按照**左端点排序**，因此在后续的线段中**只要找到左端点>自身右端点的线段就可以停止**，进入下一个线段继续找了。
- 无法覆盖的情况：
  - 所有线段中没有左端点<=1
  - 所有线段中没有右端点>=t（找到最后右端点不能再拓展时还是够不到 *t* ）

### 代码

```c++
struct rec{
    int a,b;
}p[25001];
bool cmp(const rec& a,const rec& b){
    return a.a < b.a;
}
int main(){
    int n,t;
    while(cin>>n>>t){
        int ans = 1;
        for(int i = 1;i<=n;i++){
            cin>>p[i].a>>p[i].b;
        }
        sort(p+1,p+1+n,cmp);
        int sel = 0;
        for(int i = 1;i<=n;i++){
            if(p[i].a == 1) {
                sel = i;
                break;
            }
        }
        if(sel == 0) {
            cout<<"-1\n";
        }else{
            int pos = p[sel].b;
            while(pos < t ){
                int initsel = sel;
                int nex = pos;
                for(int i = sel + 1;i <= n;i++){
                    if(p[i].a <= pos + 1) {
                        if(p[i].b > nex){
                            nex = p[i].b;
                            sel = i;
                        }
                    }else break;
                }
                pos = nex;
                if(sel == initsel) break;
                ans++;
                
            }
            if(pos >= t) cout<<ans<<"\n";
            else cout<<"-1\n";
        }
       
    }
    return 0;
}
```

## 反思

- 遇到线段这样的题拿手画画写写效率会高得多（雾

- T1果然直接暴力就能A啊。。