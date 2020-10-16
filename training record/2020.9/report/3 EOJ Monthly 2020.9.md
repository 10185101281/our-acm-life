# EOJ Monthly 2020.9



---

## 击鼓传花（EOJ Monthly 2020.9）

### 题意

​	有$n$个人围成一个环，从第$s$人开始传花，每个人将花传给向后数第$x$个人。对传花的人进行编号（从0开始，0,1,2,...），问在区间$[l,r]$的人第一次被编号的号码（如果始终不会被编号，则输出-1）。

​	由于围成一个环，如果$l\le r$，则表示区间$[l,r]$，否则表示$[r,n-1]$和$[0,l]$。

​	$T\le 10^5, 2\le n\le 10^9, 1\le x\le 10^9, 0\le s,l,r\le n-1$。

### 思路

​	首先，问题可以转化为从0开始，环的长度为$b$，每次跳$a$步，第一次跳到区间$[l,r],l\le r$所用次数。

​	即求解最小的自然数$x$，满足$l\le ax\ mod\ b\le r$，记为$f(a,b,l,r)$。

​	分情况讨论：

​	（1）若$\lceil \frac{l}{a}\rceil \le \lfloor \frac{r}{a}\rfloor $，

​		则$f(a,b,l,r)=\lceil \frac{l}{a}\rceil $

​	（2）否则，

​		存在$M\in N^*$，使得$aM<l\le r< a(M+1)$。

​		$aM<l\le ax-bk\le r<a(M+1)$

​	$\Rightarrow -a(M+1)<-r\le bk-ax\le -l< -aM$

​	$\Rightarrow a(x-M-1)<-r+ax\le bk\le -l+ax<a(x-M)$

​		而$k$即为$f(b\ mod\ a ,a,(-r)\ mod\ a, (-l)\ mod\ a)$。

​		递归求解即可。

​		注意判断无解的情况：

​	（1）$a=0$：上一层没有得到答案，所以必有$0\not\in [l,r]$，所以无解。

​	（2）$l>r$：上一层的不等式必然无解。

### 代码

```c++
#include <bits/stdc++.h>
#define pii pair<int,int>
#define fir first
#define sec second
#define pb push_back
#define ll long long
using namespace std;
const ll inf = 1e18;
inline ll tsf(ll x,ll mod){return (x%mod+mod)%mod;}
ll f(ll a, ll b, ll l, ll r){
    if(!a || l>r) return inf;
    ll t1 = (l+a-1)/a, t2 = r/a;
    if(t1 <= t2) return t1;
    ll k = f(tsf(b,a), a, tsf(-r,a), tsf(-l,a));
    if(k == inf) return inf;
    return (l+k*b + a-1)/a;
}
int main(){
     int T; cin>>T;
     while(T--){
         ll x, n, s, l, r;
         scanf("%lld%lld%lld%lld%lld",&x,&n,&s,&l,&r);
         l =tsf(l-s, n), r=tsf(r-s, n);
         ll ans = (l>r)? min(f(x,n,l,n-1),f(x,n,0,r)): f(x,n,l,r);
         if(ans == inf) puts("-1");
         else printf("%lld\n",ans);
     }
     return 0;
}
```

