---
title: 程设Week10-作业+模拟
date: 2020-05-06 17:08:16
tags:
- 动态规划
- 模拟
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

模拟——人生之敌

<!--- more -->

### [A- CodeForces - 1141A ](https://vjudge.net/problem/CodeForces-1141A/origin)

#### 题目描述

东东在玩游戏“Game23”。

在一开始他有一个数字n，他的目标是把它转换成m，在每一步操作中，他可以将n乘以2或乘以3，他可以进行任意次操作。输出将n转换成m的操作次数，如果转换不了输出-1。

##### 输入

输入的唯一一行包括两个整数n和m（1<=n<=m<=5*10^8).

##### 输出

输出从n转换到m的操作次数，否则输出-1.

#### 题目分析

- 如果可以转换，那么n必然是m的因子，有n*k=m
- 那么k的因子必然有且只有2和3，可以循环除两波
- 否则转换不了（最终k=1）

#### 代码

```c++
#include<iostream>
using namespace std;
int main(){
    int n,m;
    cin>>n>>m;
    if(m%n != 0){
        cout<<-1;
    }else {
        int cnt = 0,k=m/n;
        while(k%2 == 0 && k!=0){
            k/=2;cnt++;
        }
        while(k%3 == 0 && k!=0){
            k/=3;cnt++;
        }
        if(k!=1) cout<<-1;
        else cout<<cnt;
    }
}
```

### [B - LIS & LCS](https://vjudge.net/problem/Gym-277140A)

#### 题目描述

东东有两个序列A和B。

他想要知道序列A的LIS和序列AB的LCS的长度。

注意，LIS为严格递增的，即a1<a2<…<ak(ai<=1,000,000,000)。

##### 输入

第一行两个数n，m（1<=n<=5,000,1<=m<=5,000）
第二行n个数，表示序列A
第三行m个数，表示序列B

##### 输出

输出一行数据ans1和ans2，分别代表序列A的LIS和序列AB的LCS的长度

#### 题目分析

- LIS的转移方程：$ dp[i] = max(dp[i],dp[j] + 1);$ 其中`dp[i]`表示到以第i个位置的数为结尾的LIS，因此过程中应当记录最大值为最终答案。且dp数组应初始化为1

- LCS的转移方程：

  - `a[i] == b[j]`

    $dp[i][j] = max(dp[i-1][j-1]+1,dp[i][j])$

  - `a[i] != b[j]`

     $dp[i][j] = max(dp[i-1][j],dp[i][j-1])$

  `dp[i][j]`表示第一个序列的0-i和第二个序列的0-j构成的LCS，答案直接取`dp[n][m]`即可

#### 代码

```c++
#include<iostream>
#include<cmath>
using namespace std;
const int maxx = 5e3+10;
int a[maxx],b[maxx];
int dp1[maxx],dp2[maxx][maxx];
int main(){
    int n,m;
    cin>>n>>m;
    for(int i = 1;i<=n;i++){
        cin>>a[i];
        dp1[i] = 1;
    }
    for(int i = 1;i<=m;i++){
        cin>>b[i];
    }
    int ans1 = 0;
    for(int i = 1;i<=n;i++){
        for(int j = 1;j<i;j++){
            if(a[i] > a[j]) dp1[i] = max(dp1[i],dp1[j] + 1);
        }
        ans1 = max(ans1,dp1[i]);
    }
    for(int i = 1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(a[i] == b[j]){
                dp2[i][j] = max(dp2[i-1][j-1]+1,dp2[i][j]);
            }else {
                dp2[i][j] = max(dp2[i-1][j],dp2[i][j-1]);
            }
        }
    }
    cout<<ans1<<" "<<dp2[n][m];
    return 0;
}
```

### [C - 拿数问题 II](https://vjudge.net/problem/CodeForces-455A)

#### 题目描述

给一个序列，里边有 n 个数，每一步能拿走一个数，比如拿第 i 个数， Ai = x，得到相应的分数 x，但拿掉这个 Ai 后，x+1 和 x-1 (如果有 Aj = x+1 或 Aj = x-1 存在) 就会变得不可拿（但是有 Aj = x 的话可以继续拿这个 x）。求最大分数。

##### 输入

第一行包含一个整数 $n (1 ≤ n ≤ 10^5)$，表示数字里的元素的个数

第二行包含n个整数$a_1, a_2, ..., a_n (1 ≤ a_i ≤ 10^5)$

##### 输出

输出一个整数n：你能得到最大分值。

#### 题目分析

- 相当于把之前的位置变成了数字大小，本质是一样的。显然拿某个数就可以把相等的其他数全都拿走；
- 读入一个x时，将其加入a[x]桶里，也就是说，最终拿走每个数的分值就是a[x]；
- 转移方程：$dp[i] = max(dp[i-1],dp[i-2]+a[i])$ `dp[i]`表示取到第i个位置的最大得分是多少；

#### 代码

```c++
#include<iostream>
#include<cmath>
#include<vector>
#include<algorithm>
using namespace std;
const int maxx = 1e7+10;
long long a[maxx],dp[maxx];
int main(){
    int n;
    cin>>n;
    int l = 21474836,r = 0;
    for(int i = 1;i<=n;i++){
        int k;
        cin>>k;
        a[k] += k;
        l = min(l,k);
        r = max(k,r);
    }
    dp[l] = a[l];
    dp[l+1] = max(a[l],a[l+1]);
    for(int i = l+2;i <= r ;i++){
        dp[i] = max(dp[i-1],dp[i-2]+a[i]);
    }
    cout<<dp[r];
    return 0;
}
```

## 限时模拟

### [A - 签到题](https://vjudge.net/problem/AtCoder-2041)

#### 题目描述

TT有一个A×B×C的长方体。这个长方体是由A×B×C个1×1×1的小正方体组成的。

现在TT想给每个小正方体涂上颜色。

需要满以下三点条件：

1. 每个小正方体要么涂成红色，要么涂成蓝色。
2. 所有红色的小正方体组成一个长方体。
3. 所有蓝色的小正方体组成一个长方体。

现在TT想知道红色小正方体的数量和蓝色小正方体的数量的差异。

你需要找到红色正方体的数量与蓝色正方体的数量差值的绝对值的最小值。
即min{|红色正方体数量 - 蓝色正方体数量|}。

##### 输入

输入仅一行，三个数A B C (2≤A,B,C≤10^9)。

##### 输出

输出一个数字。

即差值绝对值的最小值。

#### 题目分析

- 注意 原来的ABC长方体不能拆开
- 如果ABC某个是2的倍数，直接对半开就可
- 否则要满足要求，则两种颜色的正方体数肯定要相差一个面的正方体数，那么求最小的面大小就可；

#### 代码

```c++
#include<iostream>
#include<cmath>
using namespace std;
int main(){
    long long a,b,c;
    cin>>a>>b>>c;
    if(a%2 == 0 || b%2 == 0 || c%2 == 0){
        cout<<0;
    }else {
        cout<<min(a*b,min(b*c,c*a));
    }
}
```

### [B - 团 队 聚 会 （不支持C++11）](https://vjudge.net/problem/POJ-1960)

#### 题目描述

TA团队每周都会有很多任务，有的可以单独完成，有的则需要所有人聚到一起，开过会之后才能去做。但TA团队的每个成员都有各自的事情，找到所有人都有空的时间段并不是一件容易的事情。

给出每位助教的各项事情的时间表，你的任务是找出所有可以用来开会的时间段。

##### 输入

第一行一个数T（T≤100），表示数据组数。

对于每组数据，第一行一个数m（2 ≤ m ≤ 20），表示TA的数量。

对于每位TA，首先是一个数n（0≤ n≤100），表示该TA的任务数。接下来n行，表示各个任务的信息，格式如下

```
YYYY MM DD hh mm ss YYYY MM DD hh mm ss "some string here"
```

每一行描述的信息为：开始时间的年、月、日、时、分、秒；结束时间的年、月、日、时、分、秒，以及一些字符串，描述任务的信息。

数据约定：

所有的数据信息均为固定位数，位数不足的在在前面补前导0，数据之间由空格隔开。

描述信息的字符串中间可能包含空格，且总长度不超过100。

所有的日期时间均在1800年1月1日00:00:00到2200年1月1日00:00:00之间。

为了简化问题，我们假定所有的月份（甚至2月）均是30天的，数据保证不含有不合法的日期。

注意每件事务的结束时间点也即是该成员可以开始参与开会的时间点。

##### 输出

对于每一组数据，首先输出一行"Scenario #i:"，i即表明是第i组数据。

接下来对于所有可以用来开会的时间段，每一个时间段输出一行。

需要满足如下规则：

1. 在该时间段的任何时间点，都应该有至少两人在场。
2. 在该时间段的任何时间点，至多有一位成员缺席。
3. 该时间段的时间长度至少应该1h。

所有的成员都乐意一天24h进行工作。

举个例子，假如现在TA团队有3位成员，TT、zjm、hrz。

那么这样的时间段是合法的：会议开始之初只有TT和zjm，后来hrz加入了，hrz加入之后TT离开了，此后直到会议结束，hrz和zjm一直在场。

要求：

1. 输出满足条件的所有的时间段，尽管某一段可能有400年那么长。
2. 时间点的格式为MM/DD/YYYY hh:mm:ss。
3. 时间段的输出格式为"appointment possible from T0 to T1"，其中T0和T1均应满足时间点的格式。
4. 严格按照格式进行匹配，如果长度不够则在前面补前导0。
5. 按时间的先后顺序输出各个时间段。
6. 如果没有合适的时间段，输出一行"no appointment possible"。
7. 每组数据末尾须打印额外的一行空行。

#### 题目分析

- 将所有出现的时间点（时刻）投射到一个时间轴上，每两个时间点之间构 成一个足够小的区间（左开右开），可以用set实现，也可以不去重直接排序。 
- 逐次判断每个区间是否合法，标记所有合法的区间。 
- **合并**所有相邻的区间，判断合并后的区间**长度**是否合法。 
- 输出所有合法的区间。

#### 代码

```c++
#include <iostream>
#include <iomanip>
#include <cstring>
#include <vector>
#include <set>

using namespace std;

struct TimeNode {
    int year, month, day;
    int hour, minute, second;

    TimeNode() {}

    TimeNode(int a, int b, int c, int d, int e, int f) {
        year = a;
        month = b;
        day = c;
        hour = d;
        minute = e;
        second = f;
    }

    TimeNode(int *a) {
        year = a[0];
        month = a[1];
        day = a[2];
        hour = a[3];
        minute = a[4];
        second = a[5];
    }

    TimeNode operator=(const TimeNode &b) {
        year = b.year;
        month = b.month;
        day = b.day;
        hour = b.hour;
        minute = b.minute;
        second = b.second;
    }

    bool operator<(const TimeNode &b) const {
        if (year != b.year)return year < b.year;
        if (month != b.month)return month < b.month;
        if (day != b.day)return day < b.day;
        if (hour != b.hour)return hour < b.hour;
        if (minute != b.minute)return minute < b.minute;
        return second < b.second;
    }

    bool operator>(const TimeNode &b) const { return b < *this; }

    bool operator<=(const TimeNode &b) const { return !(b < *this); }

    bool operator>=(const TimeNode &b) const { return !(*this < b); }

    bool operator==(const TimeNode &b) const { return !(b < *this || *this < b); }

    bool operator!=(const TimeNode &b) const { return b < *this || *this < b; }

    void Output() {
        cout << setw(2) << setfill('0') << month << '/';
        cout << setw(2) << setfill('0') << day << '/';
        cout << setw(4) << setfill('0') << year << ' ';
        cout << setw(2) << setfill('0') << hour << ':';
        cout << setw(2) << setfill('0') << minute << ':';
        cout << setw(2) << setfill('0') << second;

    }
};

int n;
set<TimeNode> s;
vector<TimeNode> Node;
vector<pair<TimeNode, TimeNode> > A[22], Ans;

//清空函数
void clr(int n) {
    s.clear();
    Ans.clear();
    Node.clear();
    for (int i = 0; i <= n; ++i)A[i].clear();
}

bool checkPer(int id, TimeNode l, TimeNode r) {
    for (int i = 0; i < A[id].size(); ++i) {
        if (l >= A[id][i].first && r <= A[id][i].second)return 0;
    }
    return 1;
}

bool check(TimeNode l, TimeNode r) {
    int tot = 0;
    for (int i = 1; i <= n; ++i) {
        if (checkPer(i, l, r))++tot;
    }
    return (tot >= n - 1 && tot >= 2);
}

bool checkLen(TimeNode l, TimeNode r) {
    if (r.year - l.year >= 2)return 1;
    r.month += (r.year - l.year) * 12;
    if (r.month - l.month >= 2)return 1;
    r.day += (r.month - l.month) * 30;
    if (r.day - l.day >= 2)return 1;
    r.hour += (r.day - l.day) * 24;
    if (r.hour - l.hour >= 2)return 1;
    r.minute += (r.hour - l.hour) * 60;
    r.second += (r.minute - l.minute) * 60;
    if (r.second - l.second >= 3600)return 1;
    return 0;
}

int main() {
    ios::sync_with_stdio(0);
    TimeNode Begin(1800, 1, 1, 0, 0, 0), End(2200, 1, 1, 0, 0, 0);
    int _;
    cin >> _;
    for (int sce = 1; sce <= _; ++sce) {
        cin >> n;
        clr(n);
        s.insert(Begin);
        s.insert(End);
        for (int i = 1; i <= n; ++i) {
            int m;
            cin >> m;
            for (int j = 1; j <= m; ++j) {
                int a[10], b[10];
                for (int k = 0; k < 6; ++k)cin >> a[k];
                for (int k = 0; k < 6; ++k)cin >> b[k];
                string str;  //没用
                getline(cin, str);
                TimeNode l(a), r(b);
                A[i].push_back(make_pair(l, r));
                s.insert(l);
                s.insert(r);
            }
        }
        for (set<TimeNode>::iterator it = s.begin(); it != s.end(); ++it) {
            Node.push_back(*it);
        }

        int l = 0, r = 0, cnt = Node.size();
        cout << "Scenario #" << sce << ":\n";
        while (l < cnt && r < cnt) {
            if (r >= cnt) break;
            while (r < cnt - 1 && check(Node[r], Node[r + 1]))++r;
            if (checkLen(Node[l], Node[r]))Ans.push_back(make_pair(Node[l], Node[r]));
            l = r + 1;
            while (l < cnt - 1 && !check(Node[l], Node[l + 1]))++l;
            r = l;
        }
        if (Ans.size() == 0)cout << "no appointment possible\n";
        else {
            for (int i = 0; i < Ans.size(); ++i) {
                cout << "appointment possible from ";
                Ans[i].first.Output();
                cout << " to ";
                Ans[i].second.Output();
                cout << '\n';
            }
        }
        cout << endl;
    }
    return 0;
}
```

