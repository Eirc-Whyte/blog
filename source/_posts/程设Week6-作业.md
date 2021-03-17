---
title: Week6-作业+周模拟题
date: 2020-03-30 16:44:43
tags: 
- 并查集
- 树的直径
- 最小生成树
- 最小生成森林
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

本周并查集专题

<!-- more-->

然而A题并不是并查集.jpg

### [A - 氪金带东](https://vjudge.net/contest/363991#problem/A)

#### 题目描述

给出一棵树，求距离每个点最远的点的距离。

##### 输入

输入文件包含多组测试数据。对于每组测试数据，第一行一个整数N (N<=10000)，接下来有N-1行，每一行两个数，对于第i行的两个数，它们表示与i号电脑连接的电脑编号以及它们之间网线的长度。网线的总长度不会超过10^9，每个数之间用一个空格隔开。

##### 输出

对于每组测试数据输出N行，第i行表示i号电脑的答案 (1<=i<=N).

#### 题目分析

- 因为是树所以存在直；
- 到某个点最远距离的点必然为直径两端点中的一个点；
- 题目转为：求树上直径两端点，并分别求两端点到所有点的距离；
- 那么只需要进行三次遍历：
  - 第一次：从任一点开始，找到距离最远的点记为`L`，`L`为直径的一个端点；
  - 第二次：从`L`点开始，找到距离最远的点记为`R`，`R`为直径的另一端点，顺便求出`L`到所有点的距离；
  - 第三次：从`R`点开始，找到`R`到所有点的距离。

#### 代码

```c++
struct Edge{
    int from,to,w;
    Edge(int f,int t,int w):from(f),to(t),w(w){}
};
vector<Edge> G[10001];
bool v[10001];
int ld[10001],rd[10001];
int n;
int bfs(int f,int dis[]){
    for(int i = 0;i<=n;i++) {dis[i] = 0;v[i] = 0;}
    queue<int> q;
    q.push(f);
    v[f] = 1;
    while(!q.empty()){
        int h = q.front();
        q.pop();
        for(auto &e:G[h]){
            if(!v[e.to]){
                v[e.to] = 1;
                q.push(e.to);
                dis[e.to] = dis[h] + e.w;
            }
        }
    }
    int last = 1;
    for(int i = 1;i<=n;i++){
        if(dis[i] > dis[last]){
            last = i;
        }
    }
    return last;
}
int dfs(int f,int dis[]){
    v[f] = 1;
    int pos = f;
    for(auto &e:G[f]){
        if(!v[e.to]){
            dis[e.to] = dis[f] + e.w;
            int s = dfs(e.to,dis);
            pos = dis[pos] > dis[s] ? pos : s;
        }
    }
    return pos;
}
int main(){
    while(cin>>n){
        for(int i = 1;i<=n;i++) G[i].clear();
        for(int v = 2;v<=n;v++){
            int u,w;
            cin>>u>>w;
            G[u].emplace_back(u,v,w);
            G[v].emplace_back(v,u,w);
        }
        for(int i = 0;i<=n;i++) {rd[i] = 0;v[i] = 0;}
        int r = dfs(1,rd);
        for(int i = 0;i<=n;i++) {rd[i] = 0;v[i] = 0;}
        int l = dfs(r,rd);
        for(int i = 0;i<=n;i++) {ld[i] = 0;v[i] = 0;}
        dfs(l,ld);
        // cout<<l<<" "<<r<<"\n";
        for(int i = 1;i <= n;i++){
            // cout<<rd[i]<<" "<<ld[i]<<"\n";
            cout<<max(rd[i],ld[i])<<"\n";
        }
    }
    return 0;
}
```

### [B - 戴好口罩](https://vjudge.net/contest/363991#problem/B)

#### 题目描述

新型冠状病毒肺炎（Corona Virus Disease 2019，COVID-19），简称“新冠肺炎”，是指2019新型冠状病毒感染导致的肺炎。
如果一个感染者走入一个群体，那么这个群体需要被隔离！
小A同学被确诊为新冠感染，并且没有戴口罩！！！！！！
危！！！
时间紧迫！！！！
需要尽快找到所有和小A同学直接或者间接接触过的同学，将他们隔离，防止更大范围的扩散。
众所周知，学生的交际可能是分小团体的，一位学生可能同时参与多个小团体内。
请你编写程序解决！戴口罩！！

##### 输入

多组数据，对于每组测试数据：
第一行为两个整数n和m（n = m = 0表示输入结束，不需要处理），n是学生的数量，m是学生群体的数量。0 < n <= 3e4 ， 0 <= m <= 5e2
学生编号为0~n-1
小A编号为0
随后，m行，每行有一个整数num即小团体人员数量。随后有num个整数代表这个小团体的学生。

##### 输出

输出要隔离的人数，每组数据的答案输出占一行

#### 题目分析

- 将每个小团体加入同一个并查集，如果多个小团体之间的成员有重合则应该合并为同一个小团体；
- 最终查找一遍与0号在同一个小团体的人数即可。

#### 代码

```c++
int father[500000];
int find(int x){
    return father[x] == x ?  x : father[x] = find(father[x]);
}
void merge(int a,int b){
    father[find(a)] = find(b);
    return ;
}

int main (){
    int n,m;
    while(cin>>n>>m){
        if(n==0&&m==0)break;
        for(int i = 0;i<=n;i++) father[i] = i;
        for(int i = 0;i<m;i++){
            int k,first,other;
            cin>>k>>first;
            for(int j = 1;j < k;j++){
                cin>>other;
                if(find(first) != find(other)){
                    merge(first,other);
                }
            }
        }
        int fa = find(0);
        int ans = 0;
        for(int i = 0;i<n;i++){
            if(find(i) == fa){
                ans++;
            }
        }
        cout<<ans<<"\n";
    }
    
    return 0;
}
```

### [C - 掌握魔法的东东](https://vjudge.net/contest/363991#problem/C)

#### 题目描述

东东在老家农村无聊，想种田。农田有 n 块，编号从 1~n。种田要灌水
众所周知东东是一个魔法师，他可以消耗一定的 MP 在一块田上施展魔法，使得黄河之水天上来。他也可以消耗一定的 MP 在两块田的渠上建立传送门，使得这块田引用那块有水的田的水。 (1<=n<=3e2)
黄河之水天上来的消耗是 Wi，i 是农田编号 (1<=Wi<=1e5)
建立传送门的消耗是 Pij，i、j 是农田编号 (1<= Pij <=1e5, Pij = Pji, Pii =0)
东东为所有的田灌水的最小消耗

##### 输入

第1行：一个数n
第2行到第n+1行：数wi
第n+2行到第2n+1行：矩阵即pij矩阵

##### 输出

东东最小消耗的MP值

#### 题目分析

- 对于每块田，有两种选择：黄河之水天上来；或者从最近的有水的田引水；
- 枚举一下情况：
  - 一块田$i$黄河之水天上来，剩下的全都直接或间接引水；那么代价就是最小生成树+$w_i$
  - 两块田$i、j$黄河之水天上来，剩下的全都直接或间接引水；那么就变成了两棵树，每棵树符合上述情况；
  - 以此类推

- 可以将每个田的“天”和其连一条边，然后和田与田之间的边共同参与最小生成森林的构造，最小生成森林最终应该有n条边；
- 注意：将两个已经分别连到天的树再连起来是**没有意义的**，合并的时候应该直接跳过，所以从天上引水的并查集要打上标记。

#### 代码

```c++
const int maxx = 2e5+10;
vector<pair<int,pair<int,int> > > edge;
int father[maxx];
bool v[maxx];
int find(int x){
    return father[x] == x ?  x : father[x] = find(father[x]);
}
void merge(int a,int b){
    int fa = find(a);
    int fb = find(b);
    father[fa] = fb;
    if(v[fa] || v[fb]) v[fa] = v[fb] = 1;
    return ;
}
bool cmp(const pair<int,pair<int,int> >& a,const pair<int,pair<int,int> >& b) {
    return a.first < b.first;
}
int main(){
    int n,w,p;
    cin>>n;
    for(int i = 1;i<=n;i++){
        cin>>w;
        father[i] = i;
        father[i+n] = i+n;
        v[i+n] = 1;
        edge.push_back(make_pair(w,make_pair(i+n,i)));
    }
    for(int i = 1;i<=n;i++){
        for(int j = 1;j<=n;j++){
            cin>>p;
            if(i<=j)continue;
            else {
                edge.push_back(make_pair(p,make_pair(i,j)));
            }
        }
    }
    sort(edge.begin(),edge.end(),cmp);
    int cnt = 0 ,ans = 0;
    for(auto &i:edge){
        int u = i.second.first;
        int t = i.second.second;
        int w = i.first;
        int fu = find(u);
        int ft = find(t);
        if(!(v[fu]&&v[ft]) && fu != ft  ){
            merge(u,t);
            ans += w;
            // cout<<u<<" "<<t<<" "<<w<<"\n";
            cnt++;
        }
        if(cnt == n)break;
    }
    cout<<ans;

    return 0;
}
```

### [D - csp201812-4数据中心](https://vjudge.net/contest/363991#problem/D)

#### 题目描述

求最小生成树的最大边

##### 输入

一个无向图

##### 输出

最小生成树的最大边值

#### 题目分析

- 直接Kruskal求出最小生成树的同时记录最大边值即可

- 注意：题目中给的root完全没用，原题的意思是：以root为根构造一棵树，求**每一层**的**最大边值**中的**最大值**

  这不就是最小生成树的最大边值🐎，嗯？阅读理解题？

#### 代码

```c++
const int maxx = 2e5+10;
vector<pair<int,pair<int,int> > > edge;
int father[maxx];
int find(int x){
    return father[x] == x ?  x : father[x] = find(father[x]);
}
void merge(int a,int b){
    father[find(a)] = find(b);
    return ;
}
bool cmp(const pair<int,pair<int,int> >& a,const pair<int,pair<int,int> >& b) {
    return a.first < b.first;
}
int main(){
    int n,p,r,m;
    cin>>n>>m>>r;
    for(int i = 1;i<=n;i++) father[i] = i;
    for(int i = 1;i<=m;i++){
        int v,u,w;
        cin>>v>>u>>w;
        edge.push_back(make_pair(w,make_pair(u,v)));
    }
    sort(edge.begin(),edge.end(),cmp);
    int cnt = 0 ,ans = 0;
    for(auto &i:edge){
        int u = i.second.first;
        int v = i.second.second;
        int w = i.first;
        if( find(u) != find(v)  ){
            // cout<<u<<" "<<v<<" "<<w<<"\n";
            merge(u,v);
            ans = w;
            cnt++;
        }
        if(cnt == n-1) break;
    }
    cout<<ans;

    return 0;
}
```

### [周模拟题 - 掌握魔法的东东2](https://vjudge.net/contest/364699#problem/A)

#### 题目描述

从瑞神家打牌回来后，东东痛定思痛，决定苦练牌技，终成赌神！
东东有 ***A* × *B* 张扑克牌**。每张扑克牌有一个大小(整数，记为a，范围区间是 0 到 *A* - 1）和一个花色（整数，记为b，范围区间是 0 到 *B* - 1。
扑克牌是互异的，也就是独一无二的，也就是说**没有两张牌大小和花色都相同**。
“一手牌”的意思是你手里有5张不同的牌，这 5 张牌**没有谁在前谁在后的顺序之分**，它们可以形成一个牌型。 我们定义了 9 种牌型，如下是 9 种牌型的规则，我们用“低序号优先”来匹配牌型，即这“一手牌”从上到下满足的第一个牌型规则就是它的“牌型编号”（一个整数，属于1到9）：

1. 同花顺: 同时满足规则 2 和规则 3.
2. 顺子 : 5张牌的大小形如 *x*, *x* + 1, *x* + 2, *x* + 3, *x* + 4
3. 同花 : 5张牌都是相同花色的.
4. 炸弹 : 5张牌其中有4张牌的大小相等.
5. 三带二 : 5张牌其中有3张牌的大小相等，且另外2张牌的大小也相等.
6. 两对: 5张牌其中有2张牌的大小相等，且另外3张牌中2张牌的大小相等.
7. 三条: 5张牌其中有3张牌的大小相等.
8. 一对: 5张牌其中有2张牌的大小相等.
9. 要不起: 这手牌不满足上述的牌型中任意一个.

现在, 东东从*A* × *B* 张扑克牌中**拿走了 2 张牌**！分别是 (*a*1, *b*1) 和 (*a*2, *b*2). （其中a表示大小，b表示花色）
现在要从剩下的扑克牌中再随机拿出 3 张！组成一手牌！！
其实东东除了会打代码，他业余还是一个魔法师，现在他要预言他的未来的可能性，即他将拿到的“一手牌”的可能性，我们用一个“牌型编号（一个整数，属于1到9）”来表示这手牌的牌型，那么他的未来有 9 种可能，但每种可能的方案数不一样。
现在，东东的阿戈摩托之眼没了，你需要帮他算一算 9 种牌型中，每种牌型的**方案数**。

##### 输入

第 1 行包含了整数 *A* 和 *B* (5 ≤ *A* ≤ 25, 1 ≤ *B* ≤ 4).

第 2 行包含了整数 *a*1, *b*1, *a*2, *b*2 (0 ≤ *a*1, *a*2 ≤ *A* - 1, 0 ≤ *b*1, *b*2 ≤ *B* - 1, (*a*1, *b*1) ≠ (*a*2, *b*2)).

##### 输出

输出一行，这行有 9 个整数，每个整数代表了 9 种牌型的方案数（按牌型编号从小到大的顺序）

#### 题目分析

- 因为已经抽走了两张牌，所以直接枚举剩下的三张牌就好，然后每次进行判断是那种牌型；
- 看数据范围没有问题
- 一些细节问题：
  - 三带二、两对、三条、一对、炸弹都是要求统计**数字相同的牌的数量**，所以可以开大小为25（$5\leq A\leq25$）的**数字桶**，再套上大小为4（$1\leq B\leq4$）的**数量桶**，就可以非常方便地进行统计；
  - 判断顺子/同花顺的时候需要对数组进行排序，这里应该是取数组的复制而不是直接排序原数组，否则会影响之后的枚举。

#### 代码

```c++
int a[3],b[3];
int num[26],cnt[5];
int A,B;
int ans[10];
vector<pair<int,int> > card,hand;

bool cmp(pair<int,int>& a,pair<int,int>& b){
    if(a.first == b.first){
        return a.second < b.second;
    }else {
        return a.first < b.first;
    }
}

int jud(vector<pair<int,int> > hand){
    sort(hand.begin(),hand.end(),cmp);
    bool flag1 = 0;
    // shunzi
    for(int i = 1;i<5;i++){
        if(hand[i-1].first == hand[i].first-1){
            flag1 = 1;
        }else {flag1 = 0;break;}
    }
    bool flag2 = 0 ;
    // tonghua
    int color = hand[0].second;
    for(auto &i:hand){
        if(color == i.second){
            flag2 = 1;
        }else{
            flag2 = 0;break;
        }
    }
    if(flag1 && flag2) return 1;
    if(flag1) return 2;
    if(flag2) return 3;
    for(int i = 0; i<=30;i++) num[i] = 0;
    for(auto &i:hand){
        num[i.first]++;
    }
    for(int i = 0;i<=10;i++) cnt[i] = 0;
    for(int i = 0 ;i<=30;i++) 
        cnt[num[i]]++;
    if(cnt[4] == 1) return 4;
    if(cnt[3] == 1 && cnt[2] == 1) return 5;
    if(cnt[2] == 2) return 6;
    if(cnt[3] == 1) return 7;
    if(cnt[2] == 1) return 8;
    return 9;
}
void dfs(int n,int pos){
    if(n == 3){
        ans[jud(hand)]++;
        return;
    }
    int size = card.size();
    for(int i = pos;i<size;i++){
        hand.push_back(card[i]);
        dfs(n+1,i+1);
        hand.pop_back();
    }
}
int main(){
    cin>>A>>B;
    cin>>a[1]>>b[1]>>a[2]>>b[2];
    for(int i = 0;i<A;i++){
        for(int j = 0;j<B;j++){
            if((i == a[1] && j == b[1]) || (i == a[2] && j== b[2])) {
                hand.push_back(make_pair(i,j));
            }else card.push_back(make_pair(i,j));
        }
    }
    dfs(0,0);
    for(int i = 1;i<=9;i++){
        cout<<ans[i]<<" ";
    }
    return 0;
}
```

