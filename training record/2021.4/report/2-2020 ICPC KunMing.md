# 2020 ICPC KunMing

##A AC

### 题解

​	计算$c_i$表示将原串第$i$位改为$a$，第$i+1$位改为$c$的代价。问题转化为：在$\sum c_i\le k$，且相邻两个$c_i$不同时被选的前提下，最多能选多少$c_i$。用堆维护反悔贪心解决。

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

const int N = 5e5+10;
const int inf = 1e9;

struct node{
    int id, val;
    bool operator < (const node &othr) const{
        return val > othr.val;
    }
};
priority_queue<node> pq;

struct seg{
    int l, r, val;
    int mk, L, R;
} a[N];
void del(int x){
    a[x].l = a[a[x].l].l;
    a[x].r = a[a[x].r].r;
    a[a[x].l].r = x;
    a[a[x].r].l = x;
}
char s[N];
int vis[N];
int main(){
    int n=read(), k=read();
    scanf("%s",s+1);
    rep(i,1,n-1){
        int val = (s[i]!='a')+(s[i+1]!='c');
        pq.push(node{i,val});
        a[i]=seg{i-1,i+1,val,0,i,i};
    }
    memset(vis,0,sizeof(vis));
    a[0].val = a[n].val = inf;
    a[0].L=a[0].R=0; a[n].L=a[n].R=n;
    int ans=0, cnt=0;
    rep(i,1,n){
        while(!pq.empty() && vis[pq.top().id]) pq.pop();
        if(pq.empty() || cnt+pq.top().val > k) break;
        node x = pq.top(); pq.pop();
        ans ++; cnt += x.val;
        x.val = a[x.id].val = a[a[x.id].l].val + a[a[x.id].r].val - x.val;
        vis[a[x.id].l] = vis[a[x.id].r] = 1;
        a[x.id].L = a[a[x.id].l].L;
        a[x.id].R = a[a[x.id].r].R;
        a[x.id].mk = 1;
        a[a[x.id].l].mk = a[a[x.id].r].mk = 0;
        pq.push(x);
        del(x.id);
    }
    printf("%d\n",ans);
    rep(i, 1, n-1){
        if(a[i].mk){
            for(int j=a[i].L+1; j<=a[i].R; j+=2){
                s[j]='a'; s[j+1]='c';
            }
        }
    }
    puts(s+1);
    return 0;
}
```

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