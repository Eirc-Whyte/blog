---
title: 程设Week8-模拟
date: 2020-04-12 22:25:31
tags:
- 思维题
- 尺取
- 二分+前缀和
categories:
- 程序设计思维与实践
thumbnail: /gallery/chengshe.png
---

T3最后一分钟没交上啊啊啊啊啊啊啊啊

以后看都不看先打暴力！！！

下次一定！！！

<!--more-->

### T1 序列

有一个序列a，问是否存在一个K，让a中的一些数+K或者-K或者不变，最后序列中所有的数相等。

##### 输入

输入第一行是一个正整数 表示数据组数。 接下来对于每组数据，输入的第一个正整数 表示序列 的长 度，随后一行有 个整数，表示序列 。 

##### 输出

输出共包含 行，每组数据输出一行。对于每组数据，如果存在这样的K，输出"YES"，否则输出“NO”。 （输出不包含引号） 

#### 题目分析

- 一开始以为是平均数，后来想T3的时候突然惊觉好像不对劲，赶紧回来改了。
- 序列a中最多存在3种不同的数：一种是数A，一种是+K=A，一种是-K=A
  - 序列a中存在1/2种数：必然存在K；
  - 序列a中存在3种数：sort一下判断差值是否相等，如果相等则可以
  - 序列a中存在多于三种数：必然不存在K

#### 代码

```c++
int n;
long long k,nums[10100];
int main(){
    ios::sync_with_stdio(false);
    int t;
    cin>>t;
    while(t--){
        cin>>n;
        int cnt = 0;
        for(int i = 1;i<=n;i++){
            cin>>k;
            bool flag = 0;
            for(int j = 0;j<cnt;j++) if(nums[j] == k) flag = 1;
            if(!flag) nums[cnt++] = k;
        }
        if(cnt>3) {cout<<"NO\n";}
        else if(cnt == 3){
            sort(nums,nums+cnt);
            if(nums[2]-nums[1] == nums[1] - nums[0]) cout<<"YES\n";
            else cout<<"NO\n";
        }else{
            cout<<"YES\n";
        }
    }
    return 0;
}
```

### T2 学英语

#### 题目描述

给定一个字符串，字符串中包括26个大写字母和特殊字 符'?'，特殊字符'？'可以代表**任何一个大写字母**。现在TT问你是否存在一个**位置连续的且由26个大写字 母组成的子串**，在这个子串中每个字母出现且仅出现一次，如果存在，请输出从左侧算起的第一个出现 的符合要求的子串，并且要求，如果有多组解同时符合位置最靠左，则输出字典序最小的那个解

##### 输入

输入只有一行，一个符合题目描述的字符串。 

##### 输出

输出只有一行，如果存在这样的子串，请输出，否则输出-1 

#### 题目分析

- 因为看到**连续子串**，所以考虑尺取类似的方法。（其实更像滑动窗口？

  - 右端点右移的条件：前面的串不够26长

    右移时把新的一位加入到当前子串的字符记录里

  - 左端点右移的条件：当前串不符合要求（没有26个不一样的英文字母

    右移时把旧的一位从当前子串的字符记录里删除

- 字典序最小只需要最后算出剩余字母并按字典序从前向后填充`?`的位置

#### 代码

```c++
int n;
int sp[30];
int main(){
    ios::sync_with_stdio(false);
    string s;
    cin>>s;
    int len = s.length(),l = -1, r = 0,cnt = 0;
    if(s[0]!='?') {sp[s[0] - 'A']++; cnt++;}
    else sp[26]++;
    while(r<len){
        if(r-l < 26) {
            r++;
            if(r == len) break;
            if(s[r]!='?'){
                if(!sp[s[r]-'A']){
                    cnt++;
                }
                sp[s[r] - 'A']++; 
            }
            else sp[26]++;
        }else {
            if(r-l == 26){
                if(cnt + sp[26] == 26){
                    char block[26];
                    int ct = 0;
                    for(int i = 25;i>=0;i--){
                        if(!sp[i]) block[ct++] = i+'A';
                    }
                    for(int i = l+1;i<=r;i++){
                        if(s[i] != '?')cout<<s[i];
                        else cout<<block[--ct];
                    }
                    return 0;
                }else {
                    l++;
                    if(s[l]!='?'){
                        sp[s[l] - 'A']--; 
                        if(!sp[s[l]-'A']){
                            cnt--;
                        }
                    }
                    else sp[26]--;
                }
            }
        }
    }
    cout<<"-1";
    return 0;
}
```

### T3 奇妙序列

有一个奇怪的无限序列：112123123412345 ......这个序列由连续正整数组成的若干部分构成，其 中第一部分包含1至1之间的所有数字，第二部分包含1至2之间的所有数字，第三部分包含1至3之间的所 有数字，第i部分总是包含1至i之间的所有数字。所以，这个序列的前56项会是 11212312341234512345612345671234567812345678912345678910，其中第1项是1，第3项是2，第20项是 5，第38项是2，第56项是0。咕咕东 现在想知道第 k 项数字是多少

##### 输入

输入由多行组成。
第一行一个整数q表示有q组询问
接下来第i+1行表示第i个输入 ，表示询问第 项数字

##### 输出

输出包含q行
第i行输出对询问 的输出结果。 

#### 题目分析

- 记1为第一轮，12为第二轮，1....n第n轮；
- 记1121231234...9为数列a1，1..1011/1...101112......99为数列2.....
- 那么可以轻易算出来数列1-8所包含的**数字数量**：
  - 数列1为首项为1公差为1大小为9的等差数列
  - 数列2为首项为10公差为2大小为90的等差数列
  - ...
  - 等差数列求和不用我多说了8，再顺便求个前缀和也很easy
- 1-8的数列就已经包含到$10^{20}$位数字了，所以我们只需要把输入和前缀和数组相比较，就可以知道要找的数在**哪个数列**里；
- 知道了在哪个数列，然后就要算在**哪个轮次**里。
- 由于数列8的大小为$9\times10^8$，因此需要在这里用一次二分，算一下要求的数在哪个轮次；
- 然后就和上面差不多，我们可以算出1到9是9\*1=9位，10到99是90*2=180位...最多$9\times 10 ^ 8 \times 8$位，那么最多只需要比较8次我们就可以知道**要求的数的所在数字**有几位，假设有k位；
- 那么只需要`所在数字-(前置数列总数字个数+前置轮次总数字个数+前置不同位数字总数字个数)/k = n ...m `
- 那么所求数字就是当前位数数字的开头数字+（n - 1） * 数字位数（+1）是否加一取决于余数m
- 细节：
  - 如果余数m==0，那么要取前一个数的末尾数字；否则直接从当前的数截取；
  - 因为很多地方用到了10的幂（算位数、最后截取），因此可以直接预处理出10的幂数组方便使用;
  - 注意开longlong

### 代码

```c++
using namespace std;
unsigned long long ten[20];
struct req{
    unsigned long long a0,d,n,an;
    unsigned long long sum;
    req(){}
    req(unsigned long long a0,unsigned long long d,unsigned long long n):a0(a0),d(d),n(n){
        sum = (a0+a0+(n-1)*d)*n/2;
        an = a0+(n-1)*d;
    }
}a[100],b[100];
unsigned long long c[30],d[30];
int main(){
    ten[0] = 1;
    for(int i = 1;i<=18;i++) ten[i] = ten[i-1] * 10;
    a[1] = req(1,1,9); 
    c[1] = a[1].sum;
    for(int i = 2;i <= 9;i++){
        a[i] = req(a[i-1].an + i,i,9*ten[i-1]);
        c[i] = c[i-1] + a[i].sum;
    }
    for(int i = 1;i <= 18;i++){
        b[i] = req(ten[i-1],0,9*ten[i-1]);
        d[i] = d[i-1] + i*9*ten[i-1];
    }
    int q;
    cin>>q;
    while(q--){
        unsigned long long k,p = 0,p1 = 0;
        cin>>k;
        for(int i = 1;i<=9;i++){
            if(c[i] > k) {p = i-1;break;}
        }
        unsigned long long las = k-c[p];
        p = p+1;
        long long l = 0,r = a[p].n;
        while(l<=r){
            long long mid = (l+r)>>1;
            if((a[p].a0 + a[p].a0 + (mid-1) * a[p].d)*mid/2 >= las){
                r = mid - 1;
            }else {
                l = mid + 1;
            }
        }
        unsigned long long k1 = las - (a[p].a0 + a[p].a0 + (r-1) * a[p].d)*r/2;
        for(int i = 1;i<=18;i++){
            if(d[i] > k1) {p1 = i-1;break;}
        }
        unsigned long long la = k1 - d[p1];
        int bits = p1+1;
        unsigned long long s = la/bits,yu = la-s*bits;
        unsigned long long num = b[bits].a0 + s ;
        // cout<<num<<"\n";
        if(yu == 0) cout<<(num-1)%10<<"\n";
        else cout<<num/ten[bits-yu]%10<<"\n";
    }
}
```

