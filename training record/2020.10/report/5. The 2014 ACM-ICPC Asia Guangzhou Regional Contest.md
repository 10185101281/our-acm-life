# The 2014 ACM-ICPC Asia Guangzhou Regional Contest

---

## J Yue Fei's Battle

 ### 题意

​	求给出$k$，求满足以下条件非同构树的个数。

​		（1）直径为$k$

​		（2）每个结点最多有3条连边。

​	$T\le 25, k\le 10^5$

### 思路

将直径从中间切开，得到的子树必定为二叉树。

考虑在二叉树上dp。

设

​	（1）$f[i]$：深度为$i$的不同构的二叉树个数；

​	（2）$sum[i]$：深度不超过$i$不同构二叉树个数。

分情况讨论：

​	（1）只有一个分支深度为$i-1$：$f[i-1]\times sum[i-2]$；

​	（2）两个分支深度都为$i-1$：

​		（2.1）两个分支不一样：$f[i-1]\times (f[i-1]-1)/2$；

​		（2.2）两个分支一样：$f[i-1]$；

然后分情况考虑直径切开后的情况：

​	（1）若$k$为偶数，得到两个深度为$k/2$的二叉树：$f[k/2]\times (f[k/2]+1)/2$；

​	（2）若$k$为奇数，切开后得到三个二叉树：

​		（2.1）三个分支有一个分支深度$\le k/2$：$f[k/2]\times (f[k/2]+1)/2\times sum[k/2-1]$；

​		（2.2）三个分支深度均为$k/2$：

​			（2.2.1）三个分支都相同：$f[k/2]$；

​			（2.2.2）三个分支中有2个相同：$f[k/2]\times (f[k/2]-1)$；

​			（2.2.3）三个分支互不相同：$C(f[k/2],3)$。

### 代码

```c++
#include <bits/stdc++.h>
#define pii pair<int,int>
#define fir first
#define sec second
#define pb push_back
#define ll long long
using namespace std;
const int mod = 1e9+7;
const int N = 1e5+10;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
int qpow(int a,int b){
    int ret = 1;
    for(;b; b>>=1){
        if(b & 1) ret = mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}
inline int rev(int x){return qpow(x, mod-2);}
int f[N], sum[N], t = -1, inv2, inv6;
void init(){
    f[0] = 1, f[1] = 1;
    sum[0] = 1, sum[1] =2;
    inv2=rev(2), inv6=rev(6);
    for(int i = 2; i<N; i++){
        f[i] = mul(f[i-1],sum[i-2]);
        f[i] = add(f[i], mul(mul(f[i-1],add(f[i-1],1)),inv2));
        sum[i] = add(sum[i-1], f[i]);
    }
}
inline int cal(int x){
    return mul(mul(x, mul(sub(x,1),sub(x,2))), inv6);
}
int main(){
    init();
    int k;
    while(scanf("%d",&k) == 1){
        if(k == 0) break;
        if(k & 1){
            int p = k/2;
            int ans = mul(mul(mul(f[p], add(f[p],1)), inv2), sum[p-1]);
            ans = add(ans, mul(f[p], f[p]));
            ans = add(ans, cal(f[p]));
            printf("%d\n", ans);
        } else {
            int p = k/2;
            printf("%d\n",mul(mul(f[p], add(f[p],1)), inv2));
        }
    }
}
```

