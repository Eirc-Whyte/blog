---
title: 程设Week7-作业
date: 2020-04-12 21:37:34
tags: 
- 最短路
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

本周各种最短路

<!-- more-->

### [A - TT 的魔法猫](https://vjudge.net/problem/HDU-1704)

#### 题目描述

有一张游戏胜负表，上面有 N 个人以及 M 个胜负关系，每个胜负关系为 A B，表示 A 能胜过 B，且胜负关系具有传递性。即 A 胜过 B，B 胜过 C，则 A 也能胜过 C。

TT 不相信他的小猫咪什么比赛都能预测，因此他想知道有多少对选手的胜负无法预先得知

##### 输入

```
第一行给出数据组数。

每组数据第一行给出 N 和 M（N , M <= 500）。

接下来 M 行，每行给出 A B，表示 A 可以胜过 B。
```

##### 输出

```
对于每一组数据，判断有多少场比赛的胜负不能预先得知。注意 (a, b) 与 (b, a) 等价，即每一个二元组只被计算一次。
```

#### 题目分析

- 因为有传递性，所以符合图的连通性。
- 因此可以直接01 floyd判断联通就可
- 要注意数据范围较大，考虑显然的剪枝：a如果无法到达k，那么就不用考虑k是否能到达b了

#### 代码

```c++
#include<iostream>
using namespace std;
bool t[510][510];
int main(){
    ios::sync_with_stdio(false);
    int T;
    cin>>T;
    while(T--){
        int n,m;
        cin>>n>>m;
        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=n;j++){
                t[i][j] = 0;
            }
        }
        for(int i = 1;i<=m;i++){
            int x,y;
            cin>>x>>y;
            t[x][y] = 1;
        }
        for(int k = 1;k<=n;k++)
            for(int i = 1;i<=n;i++){
                if(!t[i][k]) continue; // 可行性剪枝
                for(int j = 1;j<=n;j++){
                    if(t[i][k] && t[k][j]){ 
                        t[i][j] = 1;
                    }
            }
        }
        int ans = 0;
        for(int i = 1;i<=n;i++){
            for(int j = i+1;j<=n;j++){
                if(!t[i][j] && !t[j][i])ans++;
            }
        }
        cout<<ans<<"\n";
    }
    return 0;
}
```

### [B - TT 的旅行日记](https://vjudge.net/problem/UVA-11374)

#### 题目描述

TT 从家里出发，准备乘坐猫猫快线前往喵星机场。猫猫快线分为经济线和商业线两种，它们的速度与价钱都不同。当然啦，商业线要比经济线贵，TT 平常只能坐经济线，但是今天 TT 的魔法猫变出了一张商业线车票，可以坐一站商业线。假设 TT 换乘的时间忽略不计，请你帮 TT 找到一条去喵星机场最快的线路，不然就要误机了！

##### 输入

输入包含多组数据。每组数据第一行为 3 个整数 N, S 和 E (2 ≤ N ≤ 500, 1 ≤ S, E ≤ 100)，即猫猫快线中的车站总数，起点和终点（即喵星机场所在站）编号。

下一行包含一个整数 M (1 ≤ M ≤ 1000)，即经济线的路段条数。

接下来有 M 行，每行 3 个整数 X, Y, Z (1 ≤ X, Y ≤ N, 1 ≤ Z ≤ 100)，表示 TT 可以乘坐经济线在车站 X 和车站 Y 之间往返，其中单程需要 Z 分钟。

下一行为商业线的路段条数 K (1 ≤ K ≤ 1000)。

接下来 K 行是商业线路段的描述，格式同经济线。

所有路段都是双向的，但有可能必须使用商业车票才能到达机场。保证最优解唯一。

##### 输出

对于每组数据，输出3行。第一行按访问顺序给出 TT 经过的各个车站（包括起点和终点），第二行是 TT 换乘商业线的车站编号（如果没有使用商业线车票，输出"Ticket Not Used"，不含引号），第三行是 TT 前往喵星机场花费的总时间。

**本题不忽略多余的空格和制表符，且每一组答案间要输出一个换行**

#### 题目分析

目前来说 一共有三种做法

- 先从起点终点开始分别跑两遍最短路，然后枚举所有商业线，并记录dis[s] + dis[e] + w 与不使用商业线的最小值。
- dis数组加一维0/1，如果到当前节点使用了商业线则是1，未使用则是0.1则接下来只能向走非商业线连接的节点转移，如果是0则商业线和非商业线连接的节点都可以走。

- 分层图；两层图，所有商业线是第一层到第二层图的连接线，跑一边即可。

- 细节：
  - 因为是**万恶的多组数据**，而且**卡PE**（可以说是十分恶心了），因此输出要十分注意不要多输出空格；
  - 还要记得每组数据计算完之后清空各种数组、队列；
  - 还要记得方法1的两段路径输出方式不一样;
  - 还要记得dis初始值inf不能太大否则会溢出，推荐好用的`0x3f3f3f3f`；
  - 总之十分十分难调，从方法二换到方法一，从SPFA换到DJi

#### 代码

```c++
#define inf 30000000
using namespace std;
const int maxx = 510;
vector<pair<pair<int,int>,int> > node[maxx];
vector<pair<int,pair<int,int> > > come;
int n,s,e,m,k,business;
bool inq[maxx];
int dis[maxx][2];
int pre[maxx][2];
void output(int i,int t,int end){
    if(i == end || pre[i][t] == 0){
        cout<<i<<" ";
    }else{
        output(pre[i][t],t,end);
        cout<<i<<" ";
    }
    return;
}
void output1(int i){
    if(i == 0){
        return;
    }else {
        cout<<i<<" ";
        output1(pre[i][1]);
    }
}
void output0(int i){
    if(i == 0){
        return;
    }else {
        output0(pre[i][0]);
        cout<<i<<" ";
    }
}
void spfa(int s,int t){
    for(int i = 0;i<=n;i++) inq[i] = 0;
    queue<int> q;
    q.push(s);
    dis[s][t]= 0;
    while(!q.empty()){
        int h = q.front();
        q.pop();
        inq[h] = 0;
        for(auto &i : node[h]){
            int v = i.first.first,w = i.first.second;
            if(dis[h][t] + w < dis[v][t]){
                pre[v][t] = h;
                dis[v][t] = dis[h][t] + w;
                if(!inq[v]){
                    q.push(v);
                    inq[v] = 1;
                }
            }
        }
    }
}
void dji(){
    business = 0;
    dis[s][0] = dis[s][1] = 0;
    priority_queue<pair<int,int> > q;
    q.push(make_pair(0,s));
    while(!q.empty()){
        int h = q.top().second;
        q.pop();
        if(inq[h]) continue;
        inq[h] = 1;
        for(auto &i : node[h]){
            int type = i.second,v = i.first.first,w = i.first.second;
            if(type == 0){
                for(int t = 0;t<=1;t++){
                    if(dis[h][t] + w < dis[v][t]){
                        pre[v][t] = h;
                        dis[v][t] = dis[h][t] + w;
                        q.push(make_pair(-dis[v][t],v));
                    }
                } 
            }
            else {
                if(dis[h][0] + w < dis[v][1]){
                    pre[v][1] = h;
                    dis[v][1] = dis[h][0] + w;
                    business = h;
                    q.push(make_pair(-dis[v][1],v));
                }
            }
        }
        
    }
    if(dis[e][0] > dis[e][1] && business){
        output(pre[business][0],0,s);
        output(e,1,business);
        cout<<"\n";
        cout<<business<<"\n"<<dis[e][1]<<"\n\n";
    } else {
        output(e,0,s);
        cout<<"\n";
        cout<<"Ticket Not Used\n"<<dis[e][0]<<"\n\n";
    }
}
int main(){
    ios::sync_with_stdio(false);
    while(cin>>n>>s>>e){
        for(int i = 0;i<=n;i++){
            dis[i][0] = dis[i][1] =  inf;
            node[i].clear();
            pre[i][0] = pre[i][1] = 0; 
        }
        cin>>m;
        for(int i = 0;i < m;i++){
            int x,y,z;
            cin>>x>>y>>z;
            node[x].push_back(make_pair(make_pair(y,z),0));
            node[y].push_back(make_pair(make_pair(x,z),0));
        }
        cin>>k;
        for(int i = 0;i < k;i++){
            int x,y,z;
            cin>>x>>y>>z;
            // node[x].push_back(make_pair(make_pair(y,z),1));
            come.push_back(make_pair(x,make_pair(y,z)));
            // node[y].push_back(make_pair(make_pair(x,z),1));
        }
        spfa(s,0);
        spfa(e,1);
        int ans = dis[e][0] , l = 0 ,r = 0;
        for(auto &i : come){
            int u = i.first;
            int v = i.second.first;
            int w = i.second.second;
            if(dis[u][0] + dis[v][1] + w < ans ){
                ans = dis[u][0] + dis[v][1] + w;
                l = u,r = v;
            }
            if(dis[v][0] + dis[u][1] + w < ans){
                ans = dis[v][0] + dis[u][1] + w;
                l = v,r = u;
            }
        }
        if(ans != dis[e][0]){
            output0(l);
            output1(r);
            cout<<"\n";
            cout<<l<<"\n"<<ans<<"\n\n";
        }else{
            output0(e);
            cout<<"\n";
            cout<<"Ticket Not Used\n"<<ans<<"\n\n";
        }
        
        
        // dji();
    }
    return 0;
}
```

### [C - TT 的美梦](https://vjudge.net/problem/LightOJ-1074)

#### 题目描述

```
喵星上共有 M 条有向道路供商业城市相互往来。但是随着喵星商业的日渐繁荣，有些道路变得非常拥挤。正在 TT 为之苦恼之时，他的魔法小猫咪提出了一个解决方案！TT 欣然接受并针对该方案颁布了一项新的政策。

具体政策如下：对每一个商业城市标记一个正整数，表示其繁荣程度，当每一只喵沿道路从一个商业城市走到另一个商业城市时，TT 都会收取它们（目的地繁荣程度 - 出发地繁荣程度）^ 3 的税。

TT 打算测试一下这项政策是否合理，因此他想知道从首都出发，走到其他城市至少要交多少的税，如果总金额小于 3 或者无法到达请悄咪咪地打出 '?'。 
```

##### 输入

```
第一行输入 T，表明共有 T 组数据。（1 <= T <= 50）

对于每一组数据，第一行输入 N，表示点的个数。（1 <= N <= 200）

第二行输入 N 个整数，表示 1 ～ N 点的权值 a[i]。（0 <= a[i] <= 20）

第三行输入 M，表示有向道路的条数。（0 <= M <= 100000）

接下来 M 行，每行有两个整数 A B，表示存在一条 A 到 B 的有向道路。

接下来给出一个整数 Q，表示询问个数。（0 <= Q <= 100000）

每一次询问给出一个 P，表示求 1 号点到 P 号点的最少税费。
```

##### 输出

```
每个询问输出一行，如果不可达或税费小于 3 则输出 '?'。
```

#### 题目分析

- 可能存在负环的最短路问题，直接用SPFA记录某个节点是否入队n次即可；
- 某个点入队n次-> 这个点所在连通块有负环-> 负环上所有点均不可到达-> dfs该点记录一下；
- 输出描述有些问题，应该是每组数据前都要加一个Case x:

#### 代码

```c++
#define inf 0x3f3f3f3f
using namespace std;
int c[300];
vector<pair<int,int> > node[300];
int n,dis[300],vis[300],inq[300];
int v[300];
void dfs(int x){
    if(v[x]) return;
    v[x] = 1;
    for(auto &i:node[x]){
        dfs(i.first);
    }
}
void spfa(){
    queue<int> q;
    q.push(1);
    for(int i=1;i<=n;i++) dis[i] = inf,v[i] = vis[i] = inq[i] =  0;
    dis[1] = 0;
    while(q.size()){
        int h = q.front();
        vis[h] = 0;
        q.pop();
        for(auto &i:node[h]){
            if(!v[i.first] && dis[h] + i.second < dis[i.first]){
                dis[i.first] = dis[h] + i.second;
                inq[i.first]++;
                if(inq[i.first] >= n) {
                    dfs(i.first);
                }
                if(!vis[i.first]){
                    vis[i.first] = 1;
                    q.push(i.first);
                }
            }
        }
    }
}
int main(){
    ios::sync_with_stdio(false);
    int T,cnt=0;
    cin>>T;
    while(T--){
        cnt++;
        cin>>n;
        for(int i = 1;i<=n;i++){
            cin>>c[i];
            node[i].clear();
        }
        int m;
        cin>>m;
        for(int i = 1;i<=m;i++){
            int a,b;
            cin>>a>>b;
            node[a].push_back(make_pair(b,pow(c[b]-c[a],3)));
        }
        spfa();
        int q;
        cin>>q;
        cout<<"Case "<<cnt<<":\n";
        while(q--){
            int p;
            cin>>p;
            if(!v[p] && dis[p] >=3 && dis[p]!=inf)
            cout<<dis[p]<<"\n";
            else cout<<"?\n";
        }
    }
}
```

