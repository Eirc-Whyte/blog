---
title: 程设Week8-作业
date: 2020-04-12 22:25:31
tags:
- 差分约束
- 拓扑排序
- Kosaraju
- 缩点
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

DAG以及把不是DAG的题抽象成DAG

<!--more-->

### 前置知识

#### 差分约束

- 什么是差分约束系统：

  > - 一种特殊的 n 元一次不等式组，它包含 n个变量以及m个约束条件。 
  >
  > - 每个约束条件是由两个其中的变量做差构成的，形如$𝑥_i−𝑥_j≤𝑐_k$， 其中$c_k$是常数（可以是非负数，也可以是负数）。
  > - 我们要解决的问题是：求一组解$𝑥_1=𝑎_1,𝑥_2=𝑎_2,…,𝑥_n=𝑎_n$，使得所有的约束条件得到满足，否则判断出无解

- 如何求解？

  > - 对于差分约束中的每一个不等式约束$𝑥_i−𝑥_j≤𝑐_k$都可以移项变形为 $𝑥_i≤𝑐_k+𝑥_j$如果令$𝑐_k=𝑤(𝑖,𝑗)，𝑑𝑖𝑠_𝑖=𝑥_i，𝑑𝑖𝑠_𝑗=x_j$，那么原式变为$𝑑𝑖𝑠_𝑖≤ 𝑑𝑖𝑠_𝑗+𝑤(𝑖,𝑗)$，与最短路问题中的松弛操作相似
  > - 我们可以把变量$𝑥_i$看作图中的一个结点，对于每个不等式约束$𝑥_i− 𝑥_j≤𝑐_k$，从结点𝑗到节点𝑖连一条长度为$c_k $的有向边。在图上求最短路即可求得最大解 （$\textbf{x} \leq \textbf{c}$中的 $ \textbf{x} $为上界）。

- 出现负环？

  > - 如果路径中存在负环则表现为最短路的无限小，即最短路不存在
  > - 在不等式约束上表现为$𝑥_i−𝑥_j≤𝑐_k$中的T为无限小 
  > - 得出的结论就是$x_i$的结果不存在 
  > - 可以通过Bellman算法或者队列优化的Bellman算法（SPFA）来判 断负环是否存在

- 点`i`不可达？

  > - 说明`i`与1之间没有约束关系，$x_i$可以是任意值。

### A-[区间选点 II](https://vjudge.net/contest/367400#problem/A)

#### 题目描述：

给定一个数轴上的 n 个区间，要求在数轴上选取最少的点使得第 i 个区间 [ai, bi] 里至少有 ci 个点

使用差分约束系统的解法解决这道题

##### 输入

输入第一行一个整数 n 表示区间的个数，接下来的 n 行，每一行两个用空格隔开的整数 a，b 表示区间的左右端点。1 <= n <= 50000， 0 <= ai <= bi <= 50000 并且 1 <= ci <= bi - ai+1。

##### 输出

输出一个整数表示最少选取的点的个数

#### 题目分析

- 可以对数轴上点分布的前缀和数组`r`做差分约束：

  $r[b_i] - r[a_i-1] \geq c_i$
  
- 同时，每个点最多放一个点，最少放0个点：

  $r[i] - r[i-1] \leq 1$

  $r[i] - r[i-1] \geq 0$

- 直接跑最短路，并输出`dis[max(bi)]`即可

- 亿些细节：
  - 注意给定的左右区间不一定是从1开始的，要自己记录；
  - 注意先统一不等号；
  - 这波又卡vector，还是老实用前向星

#### 代码

```c++
using namespace std;
const int maxx = 5e4+10;
const int inf = 0x3f3f3f3f;
int n,dis[maxx];
bool inq[maxx];

struct rec{
    int v,w,next;
}edge[maxx*4];
int head[maxx],p;
void add(int a,int b,int c){
    p++;
    edge[p].v = b;
    edge[p].w = c;
    edge[p].next = head[a];
    head[a] = p;
}
void spfa(int s){
    for(int i = 1;i<=n;i++){
        dis[i] = inf;
        inq[i] = 0;
    }
    dis[s] = 0;
    queue<int> q;
    q.push(s);
    inq[s] = 1;
    while(q.size()){
        int h = q.front();
        inq[h] = 0;
        q.pop();
        for(int i = head[h];i;i = edge[i].next){
            int v = edge[i].v;
            int w = edge[i].w;
            if(dis[h] + w < dis[v]){
                dis[v] = dis[h] + w;
                if(!inq[v]){
                    inq[v] = 1;
                    q.push(v);
                }
            }
        }
    }
}
int main(){
    ios::sync_with_stdio(false);
    int cnt = 0,s = inf;
    n = 0;
    cin>>cnt;
    for(int i = 1;i<=cnt;i++){
        int a,b,c;
        cin>>a>>b>>c;
        add(a-1,b,-c);
        n = max(n,b);
        s = min(s,a-1);
    }
    for(int i = s;i<n;i++){
        add(i+1,i,1);
        add(i,i+1,0);
    }
    spfa(s);
    cout<<-dis[n];
    return 0;
}
```

### B-[猫猫向前冲](https://vjudge.net/contest/367400#problem/B)

#### 题目描述

拓扑排序

##### 输入

输入有若干组，每组中的第一行为二个数N（1<=N<=500），M；其中N表示猫猫的个数，M表示接着有M行的输入数据。接下来的M行数据中，每行也有两个整数P1，P2表示即编号为 P1 的猫猫赢了编号为 P2 的猫猫。

##### 输出

给出一个符合要求的排名。输出时猫猫的编号之间有空格，最后一名后面没有空格!

其他说明：符合条件的排名可能不是唯一的，此时要求输出时编号小的队伍在前；输入数据保证是正确的，即输入数据确保一定能有一个符合要求的排名。

#### 题目分析

- 就是简单粗暴的拓扑排序，只记录边，直接用堆排序编号就可。每次将入度为0的点压进去，弹出来堆顶的节点去更新与其相连的点，并把更新之后入度变成0的节点压进堆。
- 多组数据，每次记得清空。
- 不支持c++11，不能用auto有点难受。

#### 代码

```c++
using namespace std;
int n,m;
vector<int> edge[600];
int d[600];
int main(){
    n,m;
    while(cin>>n>>m){
        priority_queue<int> q;
        for(int i = 1;i<=m;i++){
            int a,b;
            cin>>a>>b;
            edge[a].push_back(b);
            d[b]++;
        }
        for(int i = 1;i<=n;i++){
            if(d[i] == 0) q.push(-i);
        }
        int cnt = 0;
        while(q.size()){
            if(cnt == n-1)break;
            int u = -q.top();
            cout<<u<<" ";
            q.pop();
            cnt++;
            int len = edge[u].size();
            for(int i = 0;i<len;i++){
                d[edge[u][i]]--;
                if(d[edge[u][i]] == 0){
                    q.push(-edge[u][i]);
                }
            }
        }
        cout<<-q.top()<<"\n";
        for(int i = 0;i<=n;i++){
            d[i] = 0;
            edge[i].clear();
        }
    }
}
```

### C-[班长竞选](https://vjudge.net/contest/367400#problem/C)

#### 题目描述

大学班级选班长，N 个同学均可以发表意见 若意见为 A B 则表示 A 认为 B 合适，意见具有传递性，即 A 认为 B 合适，B 认为 C 合适，则 A 也认为 C 合适 勤劳的 TT 收集了M条意见，想要知道最高票数，并给出一份候选人名单，即所有得票最多的同学，你能帮帮他吗？

##### 输入

本题有多组数据。第一行 T 表示数据组数。每组数据开始有两个整数 N 和 M (2 <= n <= 5000, 0 <m <= 30000)，接下来有 M 行包含两个整数 A 和 B(A != B) 表示 A 认为 B 合适。

##### 输出

对于每组数据，第一行输出 “Case x: ”，x 表示数据的编号，从1开始，紧跟着是最高的票数。 接下来一行输出得票最多的同学的编号，用空格隔开，不忽略行末空格！

#### 题目分析

- 关键句： A 认为 B 合适，B 认为 C 合适，则 A 也认为 C 合适。那么同一连通块里的人可以获得：
  - 其所在连通块中的所有人的票
  - 连到该连通块的所有其他连通块的票
- 那么直接将连通块缩为点，点权即为该连通块的大小。
- 如何求最多的？
  - 直接遍历？实操比较麻烦，因为可能会有多个入度为0的点，这些点可能会有相同的后代，因此还要判断某连通块的票数是否已经加过balabala。。。
  - 不难想到**获得票数最多的连通块必然是没有出边的**。因为如果有出边，那么该边连到的另一连通块的票数必然比当前连通块多；
  - 那么不如直接构造缩点之后的**反向图**，那就可以从所有**入度**为0的节点（可能成为答案的节点）起遍历而不用考虑重复问题了，因为这样一次就可以考虑到**所有能到达候选点的点**。直接记录点权和即可算出某一候选点的最终票数。
- 亿点点细节：
  - 多组数据记得清空
  - 依旧卡vector
  - 输出按照字典序排列
  - scnt是缩点的个数，n是点的个数，dfn是点对应dfs逆后序的编号不是点的编号，莫要搞混

#### 代码

```c++
using namespace std;
const int N  = 5010;
int c[N],dfn[N],vis[N],dcnt,scnt,ct[N],d[N],vote[N];
int n,m;
struct rec{
    int v,next;
}G1[N*10],G2[N*10],G[N*10];
int head[N],head1[N],head2[N],p,p1,p2;
bool v[N];
void add(int u,int v,rec g[],int& pp,int hd[]){
    pp++;
    g[pp].v = v;
    g[pp].next = hd[u];
    hd[u] = pp;
}
void dfs1(int x){
    vis[x] = 1;
    for(int e = head1[x];e;e = G1[e].next){
        int y = G1[e].v;
        if(!vis[y]) dfs1(y);
    }
    dfn[++dcnt] = x;
}
void dfs2(int x){
    c[x] = scnt;
    ++ct[scnt];
    for(int e = head2[x];e;e = G2[e].next){
        int y = G2[e].v;
        if(!c[y]) dfs2(y);
    }
}
void kosaraju(){
    dcnt = scnt = 0;
    memset(c,0,sizeof(c));
    memset(vis,0,sizeof(vis));
    for(int i = 0;i<n;i++){
        if(!vis[i]) dfs1(i);
    }
    for(int i = n-1;i>=0;i--){
        if(!c[dfn[i]]) ++scnt,dfs2(dfn[i]);
    }
}
int dfs(int x){
    v[x] = 1;
    int sum = ct[x];
    for(int e = head[x];e;e = G[e].next){
        int y = G[e].v;
        if(!v[y]) sum += dfs(y);
    }
    return sum;
}
int main(){
    int T,k=0;
    cin>>T;
    while(k++<T){
        cin>>n>>m;
        for(int i = 1;i<=m;i++){
            int a,b;
            cin>>a>>b;
            add(a,b,G1,p1,head1);
            add(b,a,G2,p2,head2);
        }
        kosaraju();
        for(int i = 0;i<n;i++){
            for(int e = head1[i];e;e = G1[e].next){
                int y = G1[e].v;
                if(c[y] != c[i]){
                    add(c[y],c[i],G,p,head);
                    d[c[i]]++;
                }
            }
        }
        int ans = 0;
        for(int i = 1;i<=scnt;i++){
            if(d[i] == 0){
                memset(v,0,sizeof(v));
                vote[i] = dfs(i);
                ans = max(ans,vote[i]);
            }
        }
        cout<<"Case "<<k<<": "<<ans-1<<"\n";
        vector<int> anses;
        for(int i = 0;i<n;i++){
            if(vote[c[i]] == ans){
                anses.push_back(i);
            }
        }
        cout<<anses[0];
        for(int i = 1;i < anses.size();i++) cout<<" "<<anses[i];
        cout<<"\n";
        for(int i = 0;i <= scnt;i++) ct[i] = d[i] = vote[i] = 0 ;
        for(int i = 0;i<= n;i++) head[i] = head1[i] = head2[i] = 0;
        p = p1 = p2 = 0;
    }
}
```

