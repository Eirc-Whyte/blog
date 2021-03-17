---
title: 程设Week4-作业
date: 2020-03-20 00:51:45
tags:
- 二分
- 贪心
categories: 
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

## 程设Week4-作业

本周贪心、二分专场

<!-- more-->

### [A - DDL 的恐惧](https://vjudge.net/problem/HDU-1789)

#### 题目描述

ZJM 有 n 个作业，每个作业都有自己的 DDL，如果 ZJM 没有在 DDL 前做完这个作业，那么老师会扣掉这个作业的全部平时分。

所以 ZJM 想知道如何安排做作业的顺序，才能尽可能少扣一点分。

请你帮帮他吧！

##### 输入

输入包含T个测试用例。输入的第一行是单个整数T，为测试用例的数量。

每个测试用例以一个正整数N开头(1<=N<=1000)，表示作业的数量。

然后两行。第一行包含N个整数，表示DDL，下一行包含N个整数，表示扣的分。

##### 输出

对于每个测试用例，您应该输出最小的总降低分数，每个测试用例一行。

#### 题目分析

- 倒序枚举天数，每到某天就把ddl为那一天的任务全都加到大根堆里，如此保证**大根堆里的任务ddl>=当前天**
- 在上述条件下，采取贪心策略：**能选价值大的必然不会选价值小的**，毕竟大家都还没到ddl。
- 正确性：假设某天选择了价值次大的，必然不会是ddl>最大价值的任务的（否则，考虑a任务的价值高且ddl近，b任务的价值小且ddl远，先做哪一个？）。但是根据我们的策略，此时这个任务应该还没被压到堆中，因此必然会选择最大的。
- 需要注意的是，任务个数$\neq$天数

#### 代码

```c++
struct rec{
    int ddl,sc;
}task[10010];
priority_queue<int> q; 
bool cmp(const rec &a,const rec &b){
    return a.ddl > b.ddl;
}
int main()
{
    int T;
    cin>>T;
    while(T--){
        int n;
        cin>>n;
        int maxs = 0;
        for(int i = 0;i < n;i++){
            cin>>task[i].ddl;
            maxs = max(task[i].ddl,maxs);
        }
        
        int tot = 0;
        while(!q.empty()) q.pop();
        for(int i = 0;i < n;i++){
            cin>>task[i].sc;
            tot += task[i].sc;
        }
        sort(task,task+n,cmp);
        int cnt = 0;
        
        for(int i = maxs;i>=1;i--){
            while(cnt < n && task[cnt].ddl == i){
                q.push(task[cnt++].sc);
            }
            if(!q.empty()){
                tot -= q.top();
                q.pop();
            }
        }
        cout<<tot<<"\n";
    }
    return 0;
}
```

### [B - 四个数列](https://vjudge.net/problem/POJ-2785)

#### 题目描述

ZJM 有四个数列 A,B,C,D，每个数列都有 n 个数字。ZJM 从每个数列中各取出一个数，他想知道有多少种方案使得 4 个数的和为 0。

**当一个数列中有多个相同的数字的时候，把它们当做不同的数对待。**

##### 输入

第一行：n（代表数列中数字的个数） **（1≤n≤4000）**

接下来的 n 行中，第 i 行有四个数字，分别表示数列 A,B,C,D 中的第 i 个数字（数字不超过 2 的 28 次方）

##### 输出

输出不同组合的个数。

#### 问题分析

- 暴力枚举过不了，考虑拆成$2\times n^2$的复杂度
- 枚举集合$X = A_i+ B_j$，那么对于X中的每个数，在集合$Y = C_i + D_j$中找到其**相反数**即可
- 复杂度排序$O(2n^2logn)$，二分查找$O(2n^2 logn)$
- 对应的相反数可能有很多，需要找到上界和下界，才能算出对应的区间大小

#### 代码

```c++
int a[4010],b[4010],c[4010],d[4010];
vector<int> s;
int main()
{
    int n;
    cin>>n;
    for(int i = 0;i<n;i++){
        cin>>a[i]>>b[i]>>c[i]>>d[i];
    }
    for(int i = 0;i<n;i++){
        for(int j = 0;j<n;j++){
            s.push_back(a[i]+b[j]);
        }
    }
    sort(s.begin(),s.end());
    int ans = 0;
    for(int i = 0;i<n;i++){
        for(int j = 0;j<n;j++){
            int k = c[i]+d[j];
            vector<int>::iterator low = lower_bound(s.begin(),s.end(),-k);
            vector<int>::iterator up = upper_bound(s.begin(),s.end(),-k);
            ans += (up-low);
        }
    }
    cout<<ans<<"\n";
    return 0;
} // namespace std;
```

### [C - TT 的神秘礼物](https://vjudge.net/problem/POJ-3579)

#### 题目描述

给定一个 N 个数的数组 `cat[i]`，并用这个数组生成一个新数组 `ans[i]`。新数组定义为对于任意的 i, j 且 i != j，均有 `ans[] = abs(cat[i] - cat[j])`，1 <= i < j <= N。试求出这个新数组的中位数，中位数即为排序之后 (len+1)/2 位置对应的数字，'/' 为下取整。

##### 输入

多组输入，每次输入一个 N，表示有 N 个数，之后输入一个长度为 N 的序列 cat， cat[i] <= 1e9 , 3 <= n <= 1e5

##### 输出

输出新数组 ans 的中位数

#### 问题分析

- 先给数组排个序，就可以把绝对值去掉。ans数组总长度为$\frac{n\times(n-1)}{2}$
- 假设中位数为p，那么满足$cat_j-cat_i < p$，$j>i$ 的数对$(i,j)$占新数组总数的一半以下，变换一下即$cat_i<cat_j+p$
- 那么我们通过枚举i再二分就可在$O(nlogn)$复杂度下算出新数组中小于p的数的个数num。
- 如果$num>(len(ans)+1)/2$，那么说明p比实际中位数大，应该取小一些；否则说明p比实际中位数小。会发现p也满足二分性质。
- 那么我们只要再二分p即可。复杂度$(nlog^2n)$
- 注意时间卡的比较死，用scanf或快读

#### 代码

```c++
int n,cat[300010];
int check(int k){
    int tot = 0;
    for(int i = 1;i<n;i++){
        int l=i+1,r=n;
        int ans = n;
        while(l<=r){
            int mid = (l+r)>>1;
            if(cat[mid] >= k+cat[i]){
                r = mid - 1;
            }else {
                l = mid + 1;
            }
        }
        if(r>i) tot += r-i;
    }
    return tot;
}
int read(){
    int c= getchar();
    while(!isdigit(c)){
        c = getchar();
    }
    int ans = 0;
    while(isdigit(c)){
        ans = ans * 10 + (c-'0');
        c = getchar();
    }
    return ans;
}
int main()
{
    while(~scanf("%d",&n)){
        for(int i = 1;i<=n;i++){
            cat[i] = read();
        }
        sort(cat+1,cat+n+1);
        int l=0,r=1e9+10;
        int num = (n*(n-1)/2+1)/2;
        while(l<=r){
            int mid = (l+r)>>1;
            if(check(mid)>=num){
                r = mid - 1;
            }else {
                l = mid + 1;
            }
        }
        cout<<r<<"\n";
    }
    return 0;
}
```

### 二分特点

- 使用 l<=r / mid = (l+r)>>1 / r = mid - 1 &  l = mid + 1的前提下，

- 答案分布：

  R | L ---------- 答案区间 ------------- R | L