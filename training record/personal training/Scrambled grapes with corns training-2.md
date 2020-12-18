# Scrambled grapes with corns training-2

----

## link

https://vjudge.net/contest/413512#overview

----

## D Emotional Fisherman

### 思路

​	DP后效性处理、数学。

​	将$a$排序，考虑填满一个长度为n的空位序列。

​	设$f_i$表示当前最大值是$a_i$的填充方案数，$lim_i$表示最大的$j$满足$2a_j\le a_i$。

​	考虑如何转移。假设当前枚举到$i$，考虑枚举前一个最大值的位置$j$。对于$j$，已经填空位数是$lim_j+1$，它的方案数是$f_j$。此时$a_i$一定是放在剩余空位的最前面的位置，这样在它前面的数就确定且一定合法了。然后考虑还有$lim_i-lim_j-1$个数需要放置，此时剩余$n-(lim_j+1)-1$个空位，那么方案是$A(n-lim_j-2,lim_i-lim_j-1)$的。剩余的$i-(lim_i+1)$个数，我们暂时先不放置它们，如果最后存在合法方案的话，那么最后剩余的数一定是0。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define pb push_back
#define fir first
#define sec second
#define ll long long
#define pii pair<int, int>
#define mp make_pair

const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}

const int N = 5e3+10;
int inv[N], fac[N], ifac[N];
void init(int n){
    inv[1] = fac[0] = fac[1] = ifac[0] = ifac[1] = 1;
    for(int i=2; i<=n; i++){
        inv[i] = mul(mod-mod/i,inv[mod%i]);
        fac[i] = mul(fac[i-1],i);
        ifac[i] = mul(ifac[i-1], inv[i]);
    }
}
int A(int n,int m){
    if(n<0 || m<0 || n<m) return 0;
    return mul(fac[n],ifac[n-m]);
}

ll a[N];
int lim[N], f[N];
int main(){
    int n; cin>>n;
    for(int i=1; i<=n; i++) {
        scanf("%lld",a+i);
        a[i] *= 2;
    }
    sort(a+1,a+1+n);
    for(int i=1; i<=n; i++){
        lim[i] = upper_bound(a+1,a+1+n,a[i]/2)-a-1;
    }
    if(lim[n] != n-1){
        cout<<0<<endl;
        return 0;
    }
    init(n);
    memset(f, 0, sizeof(f));
    f[0] = 1; lim[0] = -1;
    for(int i=1; i<=n; i++){
        for(int j=0; j<=lim[i]; j++){
            f[i] = add(f[i], mul(f[j], A(n-2-lim[j],lim[i]-lim[j]-1)));
        }
    }
    cout<<f[n]<<endl;
    return 0;
}
```

------

## E Sum Over Subsets

### 思路

​	设$g(n)$为集合的$gcd$恰好为$n$时的答案。

​	设$f(n)$为集合的$gcd$为$n$倍数时的答案。

​	那么有，$f(n)=\sum_{n\mid d}g(d)$

​	由莫比乌斯反演，有$g(n)=\sum_{n\mid d} \mu(\frac{d}{n})f(d)$

​	则答案为$g(1)=\sum_d\mu(d)f(d)$

​	即预处理所有$f(i)$。

​	枚举$i$，对于$\sum_{x\in A}·\sum_{y\in B}xy$，枚举每对$xy$的贡献：

​		设$|S|$为当前集合$A$的大小，

​		（1）当$x$和$y$是同个数时：

​			$x^2\times freq_x\times(|S|-1)\times 2^{|S|-2}$

​		（2）当$x,y$相同，但不是同个数时：

​			$x^2\times freq_x(freq_x-1)\times ((|S|-2)\times 2^{|S|-3}+2^{|S|-2})$

​		（3）当$x,y$不相同时：

​			$xy\times freq_x\times freq_y\times ((|S|-2)\times 2^{|S|-3}+2^{|S|-2})$

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define ll long long
inline ll add(ll a,ll b,ll mod){return (a+b)%mod;}
inline ll sub(ll a,ll b,ll mod){return ((a-b)%mod+mod)%mod;}
inline ll mul(ll a,ll b,ll mod){return a*b%mod;}
ll qpow(ll a,ll b,ll mod){
    ll ret = 1;
    for(; b; b>>=1){
        if(b & 1) ret = mul(ret,a,mod);
        a = mul(a,a,mod);
    }
    return ret;
}
ll C2(ll x,ll mod){
    return mul(x,sub(x,1,mod),mod);
}

const int N = 1e5+10;
const int maxn = 1e5;

ll mu[N]{0}; int v[N];
void init(int n,ll mod){
    for(int i=1; i<=n; i++) mu[i] = 1;
    memset(v, 0, sizeof(v));
    for(int i=2; i<=n; i++){
        if(v[i] == 0){
            for(int j=i; j<=n; j+=i){
                if((j/i)%i == 0) mu[j] = 0;
                else mu[j] = mu[j]*(-1);
                v[j] = 1;
            }
        }
    }
    for(int i=1; i<=n; i++) mu[i] = add(mu[i], mod, mod);
}

const ll mod = 998244353;
const ll mod1 = mod-1;
ll a[N], freq[N];
int main(){
    int m; scanf("%d",&m);
    for(int i=1; i<=m; i++){
        scanf("%lld",a+i);
        scanf("%lld",freq+a[i]);
    }
    init(maxn,mod);
    ll ans = 0;
    for(int i=1; i<=maxn; i++){
        ll s = 0, ts = 0, sum = 0;
        for(int j=i; j<=maxn; j+=i) s = s + freq[j];
        if(s < 2) continue;
        ts = s % mod1;
        s = s % mod;

        ll d1 = mul(sub(s,1,mod),qpow(2,sub(ts,2,mod1),mod),mod);
        ll d2 = add(
                mul(sub(s,2,mod),qpow(2,sub(ts,3,mod1),mod),mod),
                qpow(2,sub(ts,2,mod1),mod),
                mod
                );

        for(int j=i; j<=maxn; j+=i){
            sum = add(sum,
                    mul(j,freq[j],mod),
                    mod);
        }
        ll fi = 0;
        for(int j=i; j<=maxn; j+=i){
            ll t1 = mul(mul(mul(j,j,mod),freq[j],mod),d1,mod);
            ll t2 = mul(mul(mul(j,j,mod),C2(freq[j],mod),mod),d2,mod);
            ll t3 = mul(
                    mul(mul(j,freq[j],mod),sub(sum,mul(j,freq[j],mod),mod),mod),
                    d2,
                    mod);
            fi = add(fi,add(t1,add(t2,t3,mod),mod),mod);
        }
        ans = add(ans, mul(mu[i],fi,mod),mod);
    }
    cout<<ans<<endl;
    return 0;
}
```

