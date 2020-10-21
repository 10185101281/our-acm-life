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

----

## 礼物箱（EOJ Monthly 2020.9）

### 题意

​	一个礼物箱种有$n$种不同的礼物，第$i$种礼物有$a_i$件。

​	每次随机抽取一件礼物，不放回。

​	问期望抽取多少次可以收集到每一种礼物。

​	$1\le n\le 290, 1\le a_i\le 2000, \sum a_i\le 2000$

### 思路

​	令$X_i$表示搜集到第$i$种礼物时搜集到的礼物数量，设$S=\{X_1,X_2,...,X_n\}$。

​	那么所求即为：$Y=max(S)$。

​	由$min-max$容斥，有：$Y=max(S)=\sum_{T\subseteq S}(-1)^{|T|-1}min(T)$。

​	考虑如何计算$min(T)$：

​		设$\sum_{X_i\in T}a_i=a,\sum a_i =b$，

​		问题转化为：一个盒子里有$a$白球、$b-a$黑球，每次拿出一个球，问第一次拿到白球时，已经拿出球的个数期望。

​		那么有：$min(T)=\sum_{i=0}^{b-a}\frac{C_{b-a}^i}{C_b^i}\times \frac{a}{b-i}\times (1+i)$。

​	$min(T)$最多有2000种情况，暴力计算即可。

### 代码

`````c++
#include <bits/stdc++.h>
#define pii pair<int,int>
#define fir first
#define sec second
#define pb push_back
#define ll long long
using namespace std;
const int mod = 998244353;
const int N = 25;
const int M = 2e3+10;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int qpow(int a,int b){
    int ret = 1;
    for(; b; b>>=1){
        if(b & 1) ret = mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}
int fac[M], ifac[M];
inline int C(int n,int m){
    return mul(fac[n], mul(ifac[m],ifac[n-m]));
}
inline int inv(int x){
    return qpow(x, mod-2);
}
void init(){
    fac[0] = 1;
    for(int i=1; i<M; i++) fac[i] = mul(fac[i-1], i);
    ifac[M-1] = qpow(fac[M-1], mod-2);
    for(int i=M-2; i>=0; --i) ifac[i] = mul(ifac[i+1], i+1);
}
int a[N], f[M], b=0;
int cal(int a){
    if(f[a] != -1) return f[a];
    int ret = 0;
    for(int i=0; i<=b-a; i++){
        int cnt = mul(mul(mul(C(b-a,i), inv(C(b,i))), mul(a, inv(b-i))), i+1);
        ret = add(ret, cnt);
    }
    return f[a] = ret;
}
int main(){
    init();
    int n; cin>>n;
    for(int i=0; i<n; i++) scanf("%d",a+i), b += a[i];
    int D = (1<<n), ans = 0;
    memset(f, -1, sizeof(f));
    for(int i=1; i<D; i++){
        int cnt=0, sum = 0;
        for(int j=0; j<n; j++){
            if((i>>j) & 1) cnt++, sum += a[j];
        }
        if(cnt & 1) ans = add(ans, cal(sum));
        else ans = sub(ans, cal(sum));
    }
    cout<<ans<<endl;
    return 0;
}
`````

