# Day 1

---

## 1 波浪（G）

### 描述

​	对一个排列$P[1..N]$，定义其波动强度$L=|P_2-P_1|+|P_3-P_2|+...+|P_{N}-P_{N-1}|$。

​	给出$N,M$，问随机$1...N$的排列，波动强度不小于$M$的概率

​	$N\le 100, 0\le M\le 2147483647$

### 题解

​	填坑DP

​	$f[i][j][k]$填1-i个数，分为j段，代价为k的方案数。

​	计算代价时考虑分别计算每个数的贡献。

​	转移：

​	（1）另起一段

​	（2）合并两段

​	（3）贴在其中一段

---

## 2 隐蔽的居所

### 描述

​	一个圆上有$N$户人家。

​	相邻两户人家编号$i,j$满足$|i-j|\le K$

​	有M个“不喜欢”关系，如果$i$不喜欢$j$，则$j$不可以住在$i$顺时针方向下一个位置。

​	问方案数。

​	$1\le N\le 10^6,0\le M\le 10^5,0\le K\le 3$

### 题解

​	填坑DP

---

## 3 New Year and Original Order（B）

### 描述

​	定义$S(n)$为对$n$每位排序后的到的数，例如$S(50394) = 3459$。

​	给出一个数$X$，计算$\sum_{1\le k\le X}S(k)$

​	$1\le X\le 10^{700}$

https://www.cnblogs.com/heyujun/p/10225419.html

---

## 4 Game on Chessboard（B）

### 描述

​	给一个$n\times n$的棋盘。

​	棋盘上有棋子。每个棋子有一个颜色（白、黑）和一个值$w$。

​	需要将这些棋子移走。一个棋子能被移走当且仅当其左下区域没有棋子。移走棋子有两种操作：

​		（1）每次移走一个黑棋$u$一个白棋$v$，代价为$｜w_u-w_v｜$。

​		（2）直接移走一个棋子$u$，代价为$w_u$。

​	计算移走棋子的最小代价。

​	$1\le n\le 12$。

### 题解

​	轮廓线DP。

https://blog.csdn.net/qq_40859782/article/details/102489374

----

## 5 Fountains

https://www.cnblogs.com/reverymoon/p/14153891.html

---

## 6 Sum

### 做法1

​	分治，背包DP。

```c++
#include <bits/stdc++.h>

using namespace std;
#define ll long long
#define pb push_back
#define fir first
#define sec second

const int N = 3e3+10;
int n, k, t[N]; ll a[N][N]{0};
ll f[N];
ll ans = 0;
void dp(int l,int r){
    if(l == r){
        //cout<<l<<":"<<endl;
        //for(int j=0; j<=k; j++) cout<<f[j]<<' '; cout<<endl;
        for(int i=0; i<=t[l]; i++){
            ans = max(ans, f[k-i]+a[l][i]);
        }
        return ;
    }

    int mid = l+r>>1;

    ll tf[N];
    for(int i=0; i<=k; i++) tf[i] = f[i];
    for(int i=l; i<=mid; i++){
        for(int j=max(0, k-t[i]); j>=0; --j){
            f[j+t[i]] = max(f[j+t[i]],f[j]+a[i][t[i]]);
        }
    }
    dp(mid+1, r);

    for(int i=0; i<=k; i++) f[i]= tf[i];
    for(int i=mid+1; i<=r; i++){
        for(int j=max(0, k-t[i]); j>=0; --j){
            f[j+t[i]] = max(f[j+t[i]],f[j]+a[i][t[i]]);
        }
    }
    dp(l,mid);
}
int main(){
    cin>>n>>k;
    for(int i=1; i<=n; i++){
        scanf("%d",t+i);
        for(int j=1; j<=t[i]; j++){
            ll x; scanf("%lld",&x);
            if(j<=k){
                a[i][j] = a[i][j-1] + x;
            }
        }
        t[i] = min(t[i], k);
    }
    memset(f, 0xcf, sizeof(f));
    f[0] = 0;
    dp(1,n);
    cout<<ans<<endl;
    return 0;
}
```



---

## 7 Lucky Numbers

https://www.cnblogs.com/zkyJuruo/p/13834434.html

---

## 8 String Transformation

---

## 9 Financiers Game

---

## 10 The Hanged Man（HDU 6566）

---

## 11 Cell Blocking

