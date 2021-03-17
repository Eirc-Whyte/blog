---
title: 程设Week12-作业
date: 2020-05-15 11:29:28
tags:
- 区间dp
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

dp令人头秃

<!---more-->

## [A - 必做题 - 1](https://vjudge.net/problem/HDU-1029)

#### 题目描述

给出n个数，zjm想找出出现至少(n+1)/2次的数， 现在需要你帮忙找出这个数是多少？

#### 题目分析

- 至少出现(n+1)/2次——中位数
- 排序，取（n+1）/2位置的数输出即可

#### 代码

```c++
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
int ans;
int a[1999999];
int main(){
    int n;
    while(cin>>n){
        for(int i = 1;i<=n;i++){
            cin>>a[i];
        }
        sort(a+1,a+n+1);
        cout<<a[(n+1)/2]<<"\n";
    }
}
```

## [B - 必做题 - 2](https://vjudge.net/problem/POJ-2251)

#### 题目描述

zjm被困在一个三维的空间中,现在要寻找最短路径逃生！
空间由立方体单位构成。
zjm每次向上下前后左右移动一个单位需要一分钟，且zjm不能对角线移动。
空间的四周封闭。zjm的目标是走到空间的出口。
是否存在逃出生天的可能性？如果存在，则需要多少时间？

#### 题目分析

- 注意是立体的六个方向，上下左右前后
- 宽度优先遍历即可，注意记录步数

#### 代码

```c++
#include<iostream>
#include<queue>
#include<string>
using namespace std;
int zx[6]={0,0,1,-1,0,0};
int zy[6]={1,-1,0,0,0,0};
int zz[6]={0,0,0,0,1,-1};
bool t[40][40][40];
bool v[40][40][40];
struct coord{
    int x,y,z;
    int step;
    coord(int x,int y,int z,int s):x(x),y(y),z(z),step(s){}
    coord(){step=0;}
}s,e;
int L,R,C;
void solve(){
    queue<coord> q;
    q.push(s);
    while(q.size()){
        coord h(q.front());
        q.pop();
        for(int i = 0;i<6;i++){
            if(0 < h.x + zx[i] && h.x + zx[i] <= L &&
            0 < h.y + zy[i] && h.y + zy[i] <= R &&
            0 < h.z + zz[i] && h.z + zz[i] <= C && 
            t[h.x + zx[i]][h.y + zy[i]][h.z + zz[i]] && !v[h.x + zx[i]][h.y + zy[i]][h.z + zz[i]]){
                if(h.x + zx[i] == e.x && h.y + zy[i] == e.y && h.z + zz[i] == e.z){
                    cout<<"Escaped in "<<h.step+1<<" minute(s).\n";
                    return;
                }
                v[h.x + zx[i]][h.y + zy[i]][h.z + zz[i]] = 1;
                q.push(coord(h.x + zx[i],h.y + zy[i],h.z + zz[i],h.step+1));
            }
        }
    }
    cout<<"Trapped!\n";
}
int main(){
    while(cin>>L>>R>>C){
        if(L==R && R == C&&C == 0)break;
        for(int i = 1;i<=L;i++){
            for(int j = 1;j<=R;j++){
                string str;
                cin>>str;
                for(int k = 1;k<=C;k++){
                    t[i][j][k] = (str[k-1] == '.' ? 1:0);
                    if(str[k-1] == 'S') {s.x = i, s.y = j, s.z = k;t[i][j][k] = 1;}
                    else if(str[k-1] == 'E') {e.x = i, e.y = j, e.z = k;t[i][j][k] = 1;}
                    // cout<<t[i][j][k];
                }
                // cout<<"\n";
            }
        }
        solve();
        for(int i = 1;i<=L;i++){
            for(int j = 1;j<=R;j++){
                for(int k = 1;k<=C;k++){
                    t[i][j][k] = v[i][j][k] = 0;
                }
            }
        }
    }
    return 0;
}
```

## [C - 必做题 - 3](https://vjudge.net/problem/HDU-1024)

#### 题目描述

东东每个学期都会去寝室接受扫楼的任务，并清点每个寝室的人数。
每个寝室里面有ai个人(1<=i<=n)。从第i到第j个宿舍一共有sum(i,j)=a[i]+...+a[j]个人
这让宿管阿姨非常开心，并且让东东扫楼m次，每一次数第i到第j个宿舍sum(i,j)
问题是要找到sum(i1, j1) + ... + sum(im,jm)的最大值。且ix <= iy <=jx和ix <= jy <=jx的情况是不被允许的。也就是说m段都不能相交。
注：1 ≤ i ≤ n ≤ 1e6 , -32768 ≤ ai ≤ 32767 人数可以为负数。。。。(1<=n<=1000000)

##### 输入

输入m，输入n。后面跟着输入n个ai 处理到 EOF

##### 输出

输出最大和

#### 题目分析

- 先考虑了贪心做法：划分负数和正数段，判断m与正数段的关系；然而m<正数段总个数时，无法判定是通过添加负数段使得相邻的正数段连起来or取最大的m个正数段。

- 然后考虑dp：定义状态`dp[i][j][0/1]`为：到第i个班级，分成j段，选/不选第i个的最大值。

  转移方程：

  > 当前不选 ->前一个选/不选
  >
  > `dp[i][j][0] = max( dp[i-1][j][0],dp[i-1][j][1]) ); `
  >
  >  当前选 ->开新段（前面选/不选）/ 合并到前一段 
  >
  > `dp[i][j][1] = max(dp[i-1][j-1][1], dp[i-1][j-1][0] ,dp[i-1][j][1]) +a[i]); `

  后两维用滚动数组就只有2*2大小。然而并不对。原因是：仅凭左边一个无法确定当前状态。

- 因为当前状态的确定依赖于`j-1`段的所有状态，所以重新定义`dp[i][j]`为：到第i个班级，分成j段，同时选第i个的最大值。

  新的转移方程：

  > 开新段（从i之前位置的j-1段挑最大的）/ 合并到前一段 
  >
  > `dp[i][j] = max( max(dp[k][j-1])(0<k<i) , dp[i-1][j-1]) + a[i]; `

  发现每次都是从`j-1`状态转来，那么可以直接抹去这个维度。每次都要计算位置`i`之前最大的，那可以直接开个变量每次更新最大值，增加一个pre数组记录每个位置之前的最大值，更新下一段的时候用。

#### 代码

```c++
#include<iostream>
#include<algorithm>
#define ll long long
#define inf 0x7fffff
using namespace std;
int m,n,a[1000100];
int dp[1000100];
int pre[1000100];
ll read(){
    bool flag = 0;
    ll ans = 0;
    char c = getchar();
    while(!isdigit(c)) {if(c == '-') flag = 1;c=getchar();}
    while(isdigit(c)) {
        ans = ans*10 + (c-'0');
        c = getchar();
    }
    return ans*(flag ? -1 :1);
}
int main(){
    while(scanf("%d %d",&m,&n)!=-1){
        for(int i = 1;i<=n;i++){
            // a[i] = read();
            scanf("%d",&a[i]);
            dp[i] = pre[i] = 0;
        }
        int maxx;
        for(int j = 1;j<=m;j++){
            maxx = -inf;
            for(int i = j;i<=n;i++){
                dp[i] = max(pre[i-1],dp[i-1])+a[i];
                pre[i-1] = maxx;
                maxx = max(maxx,dp[i]);
            }
        }
        printf("%d\n",maxx);
    }
    return 0;
}
```

### [D - 选做题 - 1](https://vjudge.net/problem/POJ-2955)

#### 题目描述

给出一组括号序列，算出最长匹配的子序列的长度

#### 题目分析

- 区间dp，注意要左端点要反向遍历，否则无法使用之前更新好的状态。
- 转移方程：`dp[i][j] = max(dp[i][j] , dp[i][k],dp[k+1][j]);`
- `if(s[i] matches s[j]) dp[i][j] = dp[i+1][j-1]+2`

#### 代码

```c++
#include<iostream>
#include<cstring>
#include<string>
using namespace std;
int dp[1000][1000];
int main(){
    string s ;
    while(cin>>s){
        if(s[0] == 'e') break;
        int n = s.length();
        memset(dp,0,sizeof(dp));
        // for(int k = 0 ;k < n;k++) dp[k][k] = 1;
        for(int i = n-1 ;i >= 0;i--){ // 注意反向
            for(int j = i+1; j < n;j++){
                if((s[i] == '[' && s[j] == ']') || (s[i] == '(' && s[j] == ')')) dp[i][j] = dp[i+1][j-1]+2;
                for(int k = i ; k <= j;k++){
                    dp[i][j] = max(dp[i][j] , dp[i][k]+dp[k+1][j]);
                }
            }
        }
        cout<<dp[0][n-1]<<"\n";
    }
    return 0;
}
```

### [E - 选做题 - 2](https://vjudge.net/problem/HDU-1074)

 #### 题目描述

马上假期就要结束了，zjm还有 n 个作业，完成某个作业需要一定的时间，而且每个作业有一个截止时间，若超过截止时间，一天就要扣一分。
zjm想知道如何安排做作业，使得扣的分数最少。
Tips: 如果开始做某个作业，就必须把这个作业做完了，才能做下一个作业。

##### 输入

有多组测试数据。第一行一个整数表示测试数据的组数
第一行一个整数 n(1<=n<=15)
接下来n行，每行一个字符串(长度不超过100) S 表示任务的名称和两个整数 D 和 C，分别表示任务的截止时间和完成任务需要的天数。
这 n 个任务是按照字符串的字典序**从小到大**给出。

##### 输出

每组测试数据，输出最少扣的分数，并输出完成作业的方案，如果有多个方案，输出字典序最小的一个。

#### 题目分析

- 观察到作业数量很少，因此可以考虑状压dp

- 由于题目要求按照字符串从小到大给出结果，因此我们选择将所有课程先按字典序排序。

  然后考虑将作业状压：完成置1，未完成置0，转移方程：

  > `dp[ i ] = dp[ i-bit[j] ] + max( sum - task[j].ddl , 0 ) ;`

  其中`i`表示某个状态，`j`表示作业号，`bit[j]`是该作业号对应的位，`sum`是状态`i`消耗的总时间，`task[j].ddl`是任务`j`的ddl，先然超前完成不可能扣分，因此和0取个max。

  整个方程的意思就是：当前状态是由所有和当前状态差一个作业的状态转移而来。
  
- 期间还要输出顺序，pre数组直接记录某个状态要完成的最后一个作业号就好。递归输出
#### 代码
```c++
#include<iostream>
#include <bitset>
#include<cstring>
#include<algorithm>
using namespace std;
struct rec{
    int ddl,c;
    string name;
    // void init(int d,int c,string name):ddl(d),c(c),name(name){}
}task[20];
int n;
int bit[20],pre[700000];
int dp[700000];
bool cmp(const rec &a,const rec &b){
    return a.name < b.name;
}
void getPre(int x){
    if(x != 0){
        // cout<<bitset<sizeof(int)*8>(x)<<" "<<pre[x]<<"\n";
        getPre(x-bit[pre[x]]);
        cout<<task[pre[x]].name<<"\n";
    }else return;
}
int main(){
    int T;
    cin>>T;
    bit[0] = 1;
    for(int i = 1;i<=15;i++){
        bit[i] = bit[i-1] << 1;
    }
    while(T--){
        cin>>n;
        memset(dp,63,sizeof(dp));
        dp[0] = 0;
        for(int i = 0;i<n;i++){
            cin>>task[i].name>>task[i].ddl>>task[i].c;
            dp[1<<i] = max(task[i].c - task[i].ddl,0) ;
        }
        sort(task,task+n,cmp);
        
        for(int i = 1;i <= (1<<n)-1;i++){
            int sum = 0;
            for(int j = 0;j<n;j++){
                if(bit[j] & i) sum += task[j].c;
            }
            // cout<<bitset<sizeof(int)*8>(i)<<" "<<sum<<"\n";
            for(int j = 0;j<n;j++){
                if(bit[j] & i){
                    // dp[i] = min(dp[i],dp[i-bit[j]] + max( sum + task[j].c-task[j].ddl, 0));
                    if(dp[i] >= dp[i-bit[j]] + max( sum - task[j].ddl  , 0)){
                        dp[i] = dp[i-bit[j]] + max( sum  - task[j].ddl , 0);
                        pre[i] = j;
                    }
                }
            }
        }
        cout<<dp[(1<<n)-1]<<"\n";
        getPre((1<<n)-1);
    }
    
}
  
```