# 2019 ICPC XuZhou 

----

## A 

### 思路

​	对于1～n的前缀异或和：

​		$\begin{cases} n & (n\ mod\ 4 = 0)\\ 1 & (n\ mod\ 4 = 1)\\ n+1&(n\ mod\ 4 = 2)\\ 0&(n\ mod\ 4 = 3)\end{cases}$

​	那么区间$L,R$左右收缩几步后，必然可以得到异或和为0，比这个更小的区间就没有必要讨论了。因此，枚举$l\in[L,L+10],r\in[R-10,R]$的情况，取max即可。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define pii pair<int,int>
#define fir first
#define sec second
#define ll long long

ll get(ll n){
    if(n % 4 == 0) return n;
    else if(n % 4 == 1) return 1;
    else if(n % 4 == 2) return n+1;
    else return 0;
}
int main(){
    int T; cin>>T;
    while(T--){
        ll L, R, S; scanf("%lld%lld%lld",&L,&R,&S);
        ll ans = -1;
        for(ll l=L; l<=L+10; l++){
            if(l > R) break;
            for(ll r=max(l,R-10); r<=R; r++){
                if((get(l-1)^get(r)) <= S) {
                    ans = max(ans,  r-l+1);
                }
            }
        }
        printf("%lld\n",ans);
    }
    return 0;
}
```

------

## M

### 思路

​	考虑对于每棵子树，其重心一定是在重儿子到根的这条路径上。

​	预处理出每棵子树的大小，暴力扫描这条路径即可，这样整体复杂度是$O(N)$的。

​	注意处理一颗树可能存在两个重心的情况。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define pii pair<int,int>
#define pb push_back
#define fir first
#define sec second
#define ll long long

const int N = 2e5+10;
vector<int> es[N];
int n;
int sz[N]{0}, f[N]{0}, ans[N]{0}, ans1[N]{0};
void dfs(int x,int fa){
    sz[x] = 1; ans[x] = x;
    int son = 0;
    for(auto &y: es[x]){
        if(y == fa) continue;
        f[y] = x;
        dfs(y, x);
        sz[x] += sz[y];
        if(sz[y] > sz[son]) son = y;
    }
    if(2*sz[son] >= sz[x]){
        ans[x] = ans[son];
        while(2*sz[ans[x]] < sz[x]) ans[x] = f[ans[x]];
        if(2*sz[ans[x]]==sz[x]) ans1[x] = f[ans[x]];
    }
}
int main(){
    cin>>n;
    for(int i=1; i<n; i++){
        int x, y; scanf("%d%d",&x,&y);
        es[x].pb(y); es[y].pb(x);
    }
    dfs(1, -1);
    for(int i=1; i<=n; i++){
        if(ans1[i]) printf("%d ",min(ans[i],ans1[i]));
        printf("%d\n",max(ans[i],ans1[i]));
    }
    return 0;
}
```

----

## E

### 思路

​	用pollard_rho算法对X进行质因数分解。

​	枚举每个质因子在$\frac{Y!}{Z}$中剩余多少。

​	最后贪心拼$X$即可。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define ll long long
#define pll pair<ll, ll>
#define pb push_back
#define fir first
#define sec second

inline ll mul(ll a, ll b, ll mod){return (__int128) a * b % mod;}
ll pow_mod(ll a, ll b, ll mod){
    ll ret = 1;
    for(; b; b>>=1){
        if(b & 1) ret = mul(ret, a, mod);
        a = mul(a, a, mod);
    }
    return ret;
}
ll gcd(ll a, ll b){
    return b? gcd(b, a%b): a;
}

bool checkQ(ll a,ll n){
    if(a>=n) return true;
    ll d = n-1;
    while(!(d&1)) d>>=1;
    ll t = pow_mod(a, d, n);
    while(d!=n-1 && t!=1 && t!=n-1){
        t = mul(t, t, n);
        d <<= 1;
    }
    return t==n-1 || d&1;
}
bool primeQ(ll n){
    if(n==2) return true;
    if(n==1 || !(n&1)) return false;
    static vector<ll> t = {2, 325, 9375, 28178, 450775, 9780504, 1795265022};
    if(n <= 1) return false;
    for(auto k: t) if(!checkQ(k, n)) return false;
    return true;
}

mt19937 mt(time(0));
ll pollard_rho(ll n,ll c){
    ll x = uniform_int_distribution<ll>(1, n-1)(mt), y = x;
    auto f = [&](ll v){ll t = mul(v, v, n) + c; return t < n? t: t-n;};
    while(true){
        x = f(x); y = f(f(y));
        if(x == y) return n;
        ll d = gcd(abs(x-y), n);
        if(d != 1) return d;
    }
}
ll fac[100], fcnt;
void get_fac(ll n, ll cc = 19260817) {
    if (n==4) {fac[++fcnt] = 2; fac[++fcnt] = 2; return;}
    if (primeQ(n)) {fac[++fcnt] = n; return;}
    ll p = n;
    while (p==n) p = pollard_rho(n, --cc);
    get_fac(p);
    get_fac(n / p);
}
void go_fac(ll n){ fcnt = 0; if(n>1) get_fac(n);}

const int N = 1e5+10;
ll a[N]; vector<pll> p;
int main(){
    int T; cin>>T;
    while(T--){
        int n; ll X, Y;
        scanf("%d%lld%lld",&n,&X,&Y);
        for(int i=1; i<=n; i++) scanf("%lld", a+i);
        go_fac(X);
        p.clear();
        sort(fac+1,fac+fcnt+1);
        for(int i=1; i<=fcnt; i++){
            int j=i;
            while(j+1<=fcnt && fac[j+1]==fac[i]) j++;
            p.pb(pll{fac[i],j-i+1});
            i = j;
        }
        ll ans = 1e18;
        for(auto &pr: p){
            ll x=pr.fir, num = pr.sec;
            ll cnt = 0;
            for(int j=1; j<=n+1; j++){
                int mx = (j<=n)? (log(a[j])/log(x)): (log(Y)/log(x));
                ll t = 1;
                for(int k=1; k<=mx; k++){
                    t = t * x;
                    if(j<=n) cnt -= a[j]/t;
                    else cnt += Y/t;
                }
            }
            ans = min(ans, cnt/num);
        }
        printf("%lld\n",ans);
    }
    return 0;
}
```