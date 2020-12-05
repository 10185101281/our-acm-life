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

-----

## H. Yuuki and a problem(树状数组套主席树)

$Description$

给定长为$n$的序列$A_i$，两种操作：
1. 将某个数$A_i$修改为$v$。
2. 查询用区间$[l,r]$内的数不能组成的最小的数（能组成$v$是指存在一个$[l,r]$的子集$s$使$s$的和等于$v$）。
$n,A_i\leq 2\times10^5$。

$Solution$
[BZOJ(CodeChef)原题树套树版](https://www.cnblogs.com/SovietPower/p/9636966.html)

先考虑询问。设区间$[l,r]$内已经可以组成的数为$[1,v]$，$[l,r]$中最小的大于$v$的数为$mx$，若$mx>v+1$则区间的答案即为$v+1$；否则$mx=v+1$，$v$可以更新为$Sum(1,v+1)$（$[l,r]$中值在$[1,v+1]$的所有数的和），继续重复上述过程。
求$mx$的时候可以求$Sum(1,v+1)$，若$Sum=mx$则显然$mx>v+1$；否则$mx=v+1$。
容易发现$v$每次至少增加$v+1$，且这个过程可以用主席树实现。所以查询复杂度为$O(\log V\log n)$。
对于修改，改成[带修改主席树（树状数组套主席树）](https://www.cnblogs.com/SovietPower/p/8653956.html)就可以了。复杂度$O(n\log V\log^2n)$。

这个带修改主席树，每个位置维护一个前缀和即可，查询是单点查询。（不太懂他们麻烦的写法）

-----
```cpp
//1144ms	486.4MB
#include <bits/stdc++.h>
#define pc putchar
#define gc() getchar()
typedef long long LL;
const int N=2e5+5,V=2e5;

int A[N];
struct President_Tree
{
	#define S N*155//N*18*18*2 //注意MLE
	#define ls son[x][0]
	#define rs son[x][1]
	#define lson ls,l,m
	#define rson rs,m+1,r
	int tot,son[S][2];
	LL pre[S];
	#define R(x) (rs?rs:(rs=++tot))
	void Insert(int &x,int l,int r,int p)
	{
		!x&&(x=++tot);
		if(l==r) {pre[x]+=l; return;}
		int m=l+r>>1; p<=m?(pre[R(x)]+=p,Insert(lson,p)):Insert(rson,p);
	}
	void Delete(int &x,int l,int r,int p)
	{
//		!x&&(x=++tot);
		if(l==r) {pre[x]-=l; return;}
		int m=l+r>>1; p<=m?(pre[R(x)]-=p,Delete(lson,p)):Delete(rson,p);
	}
	LL Query(int x,int l,int r,int p)
	{
		if(!x) return 0;
		if(l==r) return pre[x];
		int m=l+r>>1; return pre[x]+(p<=m?Query(lson,p):Query(rson,p));
	}
};
struct BIT
{
	President_Tree T;
	int n,root[N];
	std::vector<int> vl,vr;
	#define lb(x) (x&-(x))
	void Modify(int p,int a,int v)
	{
		for(; p<=n; p+=lb(p))
		{
			if(a) T.Delete(root[p],1,V,a);
			T.Insert(root[p],1,V,v);
		}
	}
	void Query0(int l,int r)
	{
		vl.clear(), vr.clear();
		for(int p=r; p; p^=lb(p)) vr.emplace_back(p);
		for(int p=l-1; p; p^=lb(p)) vl.emplace_back(p);		
	}
	LL Query(int v)
	{
		LL res=0;
		for(auto p:vr) res+=T.Query(root[p],1,V,v);
		for(auto p:vl) res-=T.Query(root[p],1,V,v);
		return res;
	}
}T;

inline int read()
{
	int now=0,f=1; char c=gc();
	for(;!isdigit(c);c=='-'&&(f=-1),c=gc());
	for(;isdigit(c);now=now*10+c-48,c=gc());
	return now*f;
}

int main()
{
	int n=read(),Q=read(); T.n=n;
	for(int i=1; i<=n; ++i) A[i]=read();
	for(int i=1; i<=n; ++i) T.Modify(i,0,A[i]);
	for(int p,v; Q--; )
	{
		if(read()==1) p=read(),v=read(),T.Modify(p,A[p],v),A[p]=v;
		else
		{
			LL v=0; int l=read(),r=read(); T.Query0(l,r);//树状数组节点可以先求出来 
			while(1)
			{
				LL s=T.Query((int)std::min(LL(V),v+1));//不需要离散化 
				if(s==v) break;
				v=s;
			}
			printf("%lld\n",v+1);
		}
	}

	return 0;
}
```

