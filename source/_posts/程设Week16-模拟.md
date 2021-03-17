---
title: 程设Week16-模拟
date: 2020-06-09 17:44:15
tags:
- 枚举
- 区间dp
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

T1忘记longlong，难受

<!-- more-->

### TT数鸭子

#### 题目描述

这一天，TT因为疫情在家憋得难受，在云吸猫一小时后，TT决定去附近自家的山头游玩。 TT来到一个小湖边，看到了许多在湖边嬉戏的鸭子，TT顿生羡慕。此时他发现每一只鸭子都不 一样，或羽毛不同，或性格不同。TT在脑子里开了一个map<鸭子，整数> tong，把鸭子变成了 一些数字。现在他好奇，有多少只鸭子映射成的数的数位中不同的数字个数小于k。

##### 输入

输入第一行包含两个数n,k，表示鸭子的个数和题目要求的k。 接下来一行有n个数,$a_i$，每个数表示鸭子被TT映射之后的值。

##### 输出

输出一行，一个数，表示满足题目描述的鸭子的个数。 无行末空格

**$a_i < =10^{15}$！！！**

#### 题目分析

- 按位拆分判断就星，观察到k>=10的情况存在，就不用判断了，直接输出数个数就星
- 用long long /wx

#### 代码

````c++
#include <cstdio>
#include <algorithm>
#include <iostream>
#include <bitset>
#include <set>
#include <sstream>
#include <string>
#include <cstring>
#include <stack>
#include <cmath>
#include <queue>
#include <cctype>
#include <map>
#include <cstdlib>
#include <vector>
#define ll long long
#define INF 0x3f3f3f3f
using namespace std;

ll read(){
    ll x=0,f=1;char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}
    while(ch>='0' && ch<='9'){x=x*10+ch-'0';ch=getchar();}
    return x*f;
}
bool v[10];
int main(){
    int n = read(),k=read(),tot = 0 ;
    if(k > 10) cout<<n;
    else {
        for(int i = 1;i<=n;i++){
            long long num = read(),cnt = 0;
            memset(v,0,sizeof(v));
            while(num){
                int lb = num%10;
                num /= 10;
                if(!v[lb]) {
                    v[lb] = 1;cnt++;
                }
                if(cnt >= k) break;
            }
            if(cnt < k) tot++;
        }
        cout<<tot;
    }
    return 0;
}
````

### ZJM要抵御宇宙射线

#### 题目描述

据传，2020年是宇宙射线集中爆发的一年，这和神秘的宇宙狗脱不了干系！但是瑞神和东东忙 于正面对决宇宙狗，宇宙射线的抵御工作就落到了ZJM的身上。假设宇宙射线的发射点位于一个 平面，ZJM已经通过特殊手段获取了所有宇宙射线的发射点，他们的坐标都是整数。而ZJM要构 造一个保护罩，这个保护罩是一个圆形，中心位于一个宇宙射线的发射点上。同时，因为大部分 经费都拨给了瑞神，所以ZJM要节省经费，做一个最小面积的保护罩。当ZJM决定好之后，东东 来找ZJM一起对抗宇宙狗去了，所以ZJM把问题扔给了你~

##### 输入

输入 第一行一个正整数N，表示宇宙射线发射点的个数 接下来N行，每行两个整数X,Y，表示宇宙射线发射点的位置

##### 输出

输出包括两行 第一行输出保护罩的中心坐标x,y 用空格隔开 第二行输出保护罩半径的平方 （所有输出保留两位小数，如有多解，输出x较小的点，如扔有多解，输入y较小的点）无行末空格

#### 题目分析

- 观察到n<=1000，所以直接n^2枚举所有点之间的距离，答案就是**每个点距离其他点最大值（要全部覆盖到）的最小值（最小面积）**
- 坑点：
  - 最后输出的是**半径的平方**
  - “如有多解，输出x较小的点，如扔有多解，输入y较小的点” 要求排序后再枚举

#### 代码

```c++
#include <cstdio>
#include <algorithm>
#include <iostream>
#include <bitset>
#include <set>
#include <sstream>
#include <string>
#include <cstring>
#include <stack>
#include <cfloat>
#include <cmath>
#include <queue>
#include <cctype>
#include <map>
#include <cstdlib>
#include <vector>
#define ll long long
#define INF 0x3f3f3f3f
using namespace std;

ll read(){
    ll x=0,f=1;char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}
    while(ch>='0' && ch<='9'){x=x*10+ch-'0';ch=getchar();}
    return x*f;
}
struct node{
    double x,y;
}nd[2000];
double dis = LDBL_MAX;
int ans;
inline double getDis(int i,int j){
    return (nd[i].x-nd[j].x)*(nd[i].x-nd[j].x) + (nd[i].y-nd[j].y)*(nd[i].y-nd[j].y);
}
bool cmp(node &a,node &b){
    if (a.x == b.x){
        return a.y < b.y;
    }else return a.x < b.x;
}
int main(){
    int n =read();
    for(int i = 1;i<=n;i++){
        cin>>nd[i].x>>nd[i].y;
    }
    sort(nd+1,nd+n+1,cmp);
    for(int i = 1;i<=n;i++){
        double diss = 0.0;
        for(int j = 1;j<=n;j++){
            if(i != j){
                diss = max(diss,getDis(i,j));
            }
        }
        if(dis > diss){
            dis = diss;
            ans = i;
        }
    }
    printf("%.2lf %.2lf\n%.2lf",nd[ans].x,nd[ans].y,dis);
    return 0;
}
```

### 宇宙狗的危机

#### 题目描述

在瑞神大战宇宙射线中我们了解到了宇宙狗的厉害之处，虽然宇宙狗凶神恶煞，但是宇宙狗有一 个很可爱的女朋友。 最近，他的女朋友得到了一些数，同时，她还很喜欢树，所以她打算把得到的数拼成一颗树。 这一天，她快拼完了，同时她和好友相约假期出去玩。贪吃的宇宙狗不小心把树的树枝都吃掉 了。所以恐惧包围了宇宙狗，他现在要恢复整棵树，但是它只知道这棵树是一颗二叉搜索树，同 时任意树边相连的两个节点的gcd(greatest common divisor)都超过1。 但是宇宙狗只会发射宇宙射线，他来请求你的帮助，问你能否帮他解决这个问题。

##### 输入

输入第一行一个t，表示数据组数。 对于每组数据，第一行输入一个n，表示数的个数 接下来一行有n个数$a_i$，输入保证是升序的。

##### 输出

每组数据输出一行，如果能够造出来满足题目描述的树，输出Yes，否则输出No。 无行末空格。

数据范围n<=700

#### 题目分析

- 首先判断gcd预处理出可以连接在一起的数字
- `dp[i][j]`表示区间`[i,j]`是否可行
- 对于某段区间`[i,j]`，枚举k，判断
  - k与j+1可以连接，那么区间`[i,j+1]`可行
  - k与i-1可以连接，那么区间`[i-1,j]`可行
- 因为是**二叉搜索树**，所以应该**区分左右子树**
  - 若区间`[i,j]`可以转移到`[i,j+1]`显然`[i,j]`是j+1的左子树
  - 若区间`[i,j]`可以转移到`[i-1,j]`显然`[i,j]`是i-1的右子树
  - 那么状态应该改为`dp[i][j][0/1] `0表示左子树1表示右子树
- btw，区间`[i,j]`可以通过k向左右转移的条件是：`dp[i][k][0]&&dp[k][j][1]`

#### 代码

```c++
#include <cstdio>
#include <algorithm>
#include <iostream>
#include <bitset>
#include <set>
#include <sstream>
#include <string>
#include <cstring>
#include <stack>
#include <cmath>
#include <queue>
#include <cctype>
#include <map>
#include <cstdlib>
#include <vector>
#define ll long long
#define INF 0x3f3f3f3f
using namespace std;

ll read(){
    ll x=0,f=1;char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}
    while(ch>='0' && ch<='9'){x=x*10+ch-'0';ch=getchar();}
    return x*f;
}
int a[750];
bool p[750][750];
bool dp[750][750][2];
int gcd(int a,int b){return b == 0 ? a : gcd(b,a%b);}
int main(){
    int t = read();
    while(t--){
        memset(a,0,sizeof(a));
        memset(p,0,sizeof(p));
        memset(dp,0,sizeof(dp));
        int n = read();
        for(int i = 1;i<=n;i++){
            a[i] = read();
            dp[i][i][0] = dp[i][i][1] = 1;
        }
        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=n;j++){
                if(i!=j) p[i][j] = gcd(a[i],a[j]) > 1;
            }
        }
        bool flag = 0 ;
        for(int l = 1;l <= n;l++){
            for(int i = 1;i+l-1<=n;i++){
                int j = i+l-1;
                for(int k = i; k<=j ;k++){
                    if(dp[i][k][0] && dp[k][j][1]){
                        if(i == 1 && j == n) flag = 1;
                        if(p[k][j+1]) dp[i][j+1][0] = 1;
                        if(p[i-1][k]) dp[i-1][j][1] = 1;
                    }
                }
            }
        }
        if(flag)cout<<"Yes\n";
        else cout<<"No\n";
    }
}
```

