# 2020 ICPC KunMing

##A AC

### 题解

### 代码

## C Cities

### 题解

​	将连续$a_i$压缩，设压缩后数组为$b$有$m$段。

​	记$f[l][r]$表示将区间$[l,r]$合并为$b_l$能省掉的最大步数，转移方程：

​	$f[l][r]=max\{f[l+1][r],max_{l+1\le i\le r, a_l=a_i}\{f[l+1,i-1]+f[i,r]+1\}\}$

### 代码

```c++
#include <bits/stdc++.h>

#define pb push_back
#define pii pair<int, int>
#define fir first
#define sec second
#define ll long long
#define rep(i,a,b) for(int i=(a); i<=(b); ++i)
#define per(i,a,b) for(int i=(a); i>=(b); --i)

using namespace std;

#define gc() getchar()
inline int read()
{
    int now=0,f=1; char c=gc();
    for(;!isdigit(c);c=='-'&&(f=-1),c=gc());
    for(;isdigit(c);now=now*10+c-48,c=gc());
    return now*f;
}
const int N = 5e3+10;
int a[N], b[N];
int p[N], nxt[N];
int f[N][N];
void solve(){
    int n=read(), m=0;
    rep(i,1,n) a[i]=read();
    memset(p,0,sizeof(p));
    memset(f,0,sizeof(f));
    rep(i,1,n){
        int j=i;
        while(j+1<=n && a[j+1]==a[i]) ++j;
        b[++m] = a[i];
        nxt[p[b[m]]] = m;
        nxt[m] = n+1;
        p[b[m]] = m;
        i=j;
    }
    rep(len,2,m){
        rep(l,1,m){
            int r=l+len-1;
            if(r>m) break;
            f[l][r] = f[l+1][r];
            for(int k=nxt[l]; k<=r; k=nxt[k]){
                f[l][r] = max(f[l][r], f[l][k-1]+f[k][r]+1);
            }
        }
    }
    printf("%d\n",m-1-f[1][m]);
}
int main(){
    int T=read();
    while(T--) solve();
    return 0;
}
```

## D Competiton

### 题解

​	胜利条件为$k^n$种序列恰好可以划分成$n$份 ，即$n\mid k^n$。

​	对于判断$n\mid k^n$，充要条件是$n$的质因子被$k$覆盖。重复操作$n\leftarrow \frac{n}{gcd(k,n)}$，观察$n$是否会降为1即可。

### 代码

```c++
#include <bits/stdc++.h>

#define pb push_back
#define pii pair<int, int>
#define fir first
#define sec second
#define ll long long
using namespace std;

#define gc() getchar()
inline int read()
{
    int now=0,f=1; char c=gc();
    for(;!isdigit(c);c=='-'&&(f=-1),c=gc());
    for(;isdigit(c);now=now*10+c-48,c=gc());
    return now*f;
}
inline ll readll()
{
    ll now=0,f=1; char c=gc();
    for(;!isdigit(c);c=='-'&&(f=-1),c=gc());
    for(;isdigit(c);now=now*10+c-48,c=gc());
    return now*f;
}
ll gcd(ll a,ll b){
    return b? gcd(b, a%b): a;
}
int main(){
     int q=read();
     while(q--){
         ll n=readll(), k=readll();
         for(; gcd(n,k)!=1; n/=gcd(n,k));
         if(n==1) puts("HUMAN");
         else puts("ROBOT");
     }
     return 0;
}
```