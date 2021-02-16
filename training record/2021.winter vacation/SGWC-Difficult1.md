# SGWC-Difficult1

---

## A-AB Tree

### 思路

​	将每一层的点看作一组。

​	注意到答案一定$\ge d+1$，$d$是最深的深度。

​	当答案为$d+1$时，必然存在一些组的和为$x$，转化为分组背包问题。由于不同的大小最多为$O(\sqrt N)$，复杂度为$O(N\sqrt N)$。

​	否则，答案必然为$d+2$。构造方法：对于每一层，将其填充为同一种字母。如果某一时刻无法填充，必然有不等式：非叶子结点$\le m/2\le $较多的字母数，其中$m$是为填充节点。此时我们将非叶子结点全部用较多字母填充，并将剩余的字母填充至部分的叶子结点，然后另所有剩余结点为另一种字母即可。

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pii pair<int, int>
#define fir first
#define sec second
#define pb emplace_back

#define gc() getchar()
inline int read()
{
    int now=0,f=1; char c=gc();
    for(;!isdigit(c);c=='-'&&(f=-1),c=gc());
    for(;isdigit(c);now=now*10+c-48,c=gc());
    return now*f;
}

const int mod = 1e9+7;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int add(int a,int b,int mod){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int qpow(int a,int b){
    int ret=1;
    for(; b; b>>=1){
        if(b&1) ret=mul(ret,a);
        a=mul(a,a);
    }
    return ret;
}

const int N=1e5+10;
const int M=350;
vector<int> es[N];
vector<int> vec[N];
int isleaf[N];
int dfs(int dep,int x){
    vec[dep].pb(x);
    int d=dep;
    for(auto &y: es[x]){
        d=max(d,dfs(dep+1, y));
    }
    isleaf[x]=es[x].empty();
    return d;
}
const int inf=1e8;
int pre[M][N], pf[N], f[N], num[N], w[N];
int mx[N];
vector<int> q[N],tmp;
char ans[N];
int main(){
    int n=read(),x=read();
    for(int i=2; i<=n; i++) {
        int p=read(); es[p].pb(i);
    }
    int d=dfs(1,1);
    //for(int i=1; i<=d; i++){cout<<i<<':'; for(auto &y: vec[i]) cout<<y<<' '; cout<<endl;}
    for(int i=1; i<=d; i++){
        q[vec[i].size()].pb(i);
        tmp.pb(vec[i].size());
    }
    int sz=tmp.size();
    sort(tmp.begin(),tmp.end());
    int m=0;
    for(int l=1; l<=sz; l++){
        int r=l; while(r+1<=sz&&tmp[r]==tmp[l-1])r++;
        num[++m]=r-l+1; w[m]=tmp[l-1];
        l=r;
    }
    memset(f,0,sizeof(f));
    memset(pf,0,sizeof(pf));
    memset(pre,-1,sizeof(pre));
    f[0]=1;
    int ansm;
    for(int i=1; i<=m; i++){
        //cout<<num[i]<<' '<<w[i]<<endl;
        for(int j=0; j<=n; j++) pf[j]=f[j],f[j]=0,mx[j]=-inf;
        for(int j=0; j<=n; j++) {
            int k=j/w[i], b=j%w[i];
            if(pf[j]) mx[b]=k;
            if(!f[j] && k-mx[b]<=num[i]) {
                f[j]=1;
                pre[i][j]=mx[b]*w[i]+b;
            }
        }
        if(f[x]) {ansm=i;break;}
    }
    if(f[x]){
        printf("%d\n",d);
        for(int i=1; i<=n; i++) ans[i]='b'; ans[n+1]=0;
        int t=x;
        for(int i=ansm; i>=1; --i){
            int pt=pre[i][t];
            int c=(t-pt)/w[i];
            for(int j=0; j<c; j++){
                int dep=q[w[i]][j];
                for(auto &y: vec[dep]) ans[y]='a';
            }
            t=pt;
        }
        puts(ans+1);
    } else {
        printf("%d\n",d+1);
        for(int i=1; i<=n+1; i++) ans[i]=0;
        int resa=x, resb=n-x;
        for(int i=1; i<=d; i++){
            int cnt=vec[i].size();
            if(cnt<=resa){
                for(auto &y: vec[i]) ans[y]='a';
                resa-=cnt;
            } else if(cnt<=resb){
                for(auto &y: vec[i]) ans[y]='b';
                resb-=cnt;
            } else {
                if(resa>=resb){
                     for(auto &y: vec[i]) if(!isleaf[y]) ans[y]='a', resa--;
                     for(auto &y: vec[i]) {
                         if(!ans[y]){
                             if(resa) ans[y]='a', resa--;
                             else ans[y]='b', resb--;
                         }
                     }
                } else {
                    for(auto &y: vec[i]) if(!isleaf[y]) ans[y]='b', resb--;
                    for(auto &y: vec[i]) {
                        if(!ans[y]){
                            if(resb) ans[y]='b', resb--;
                            else ans[y]='a', resa--;
                        }
                    }
                }
            }
        }
        puts(ans+1);
    }
    return 0;
}
```

---

## C-[Odd Mineral Resource](https://codeforces.com/problemset/problem/1479/D)

### 思路

​	对每种资源赋值一个64位随机数权值。

​	设$s(u,v,c)$表示从u到v的c种资源数量，$f(u,v,l,c)$表示u到v的所有资源权值异或和。

​	那么有：

​		$Pr[\forall c,s(u,v,c)\equiv0(mod\ 2)\mid f(u,v,l,r)=0]=1-2^{-64}$

​		$Pr[\exists c,s(u,v,c)\not\equiv 0(mod\ 2)\mid f(u,v,l,r)\neq 0]=1$

​	而$f(u,v,l,c)=f(1,u,l,r)\oplus f(1,v,l,r)\oplus f(1,lca,l,r)\oplus f(1,fa[lca],l,r)$	

​	对于每个节点，维护其到跟节点1的所有资源权值异或和即可。

​	由于一个节点实际上只会在其父节点的基础上对一种资源做出修改，所以可以用主席树维护。

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
#define pii pair<int, int>
#define fir first
#define sec second
#define pb emplace_back

#define gc() getchar()
inline int read()
{
    int now=0,f=1; char c=gc();
    for(;!isdigit(c);c=='-'&&(f=-1),c=gc());
    for(;isdigit(c);now=now*10+c-48,c=gc());
    return now*f;
}
mt19937_64 RD(chrono::steady_clock::now().time_since_epoch().count());
const int mod = 1e9+7;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int add(int a,int b,int mod){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int qpow(int a,int b){
    int ret=1;
    for(; b; b>>=1){
        if(b&1) ret=mul(ret,a);
        a=mul(a,a);
    }
    return ret;
}

const int N=3e5+10;
struct node{
    int lc, rc;
    ll val;
} T[N<<5];
int tot=0, rt[N];
int a[N]; ll X[N];
void pushup(int p){
    T[p].val=T[T[p].lc].val^T[T[p].rc].val;
}
int build(int L,int R){
    int p=++tot;
    if(L==R){
        T[p].val=0;
        return p;
    }
    int mid=(L+R)>>1;
    T[p].lc=build(L,mid);
    T[p].rc=build(mid+1,R);
    pushup(p);
    return p;
}
int update(int pre,int L,int R,int pos){
    int p=++tot;
    if(L==R){
        T[p].val=T[pre].val^X[pos];
        return p;
    }
    int mid=(L+R)>>1;
    T[p].lc=T[pre].lc;
    T[p].rc=T[pre].rc;
    if(pos<=mid) T[p].lc=update(T[pre].lc,L,mid,pos);
    else T[p].rc=update(T[pre].rc,mid+1,R,pos);
    pushup(p);
    return p;
}
vector<int> es[N];
int n, q;
int dep[N]{0}, pa[N][30];
const int SP=20;
void dfs(int u,int fa){
    pa[u][0]=fa; dep[u]=dep[fa]+1;
    for(int i=1; i<=SP; i++) pa[u][i]=pa[pa[u][i-1]][i-1];
    rt[u]=update(rt[fa],1,n,a[u]);
    for(auto &v: es[u]){
        if(v==fa)continue;
        dfs(v,u);
    }
}
int LCA(int u,int v){
    if(dep[u]<dep[v]) swap(u,v);
    int t=dep[u]-dep[v];
    for(int i=0; i<=SP; i++) if(t&(1<<i)) u=pa[u][i];
    for(int i=SP; i>=0; --i){
        int uu=pa[u][i], vv=pa[v][i];
        if(uu!=vv) {u=uu; v=vv;}
    }
    return u==v? u:pa[u][0];
}
int getans(int a,int b,int c,int d,int L,int R){
    if(L==R) return L;
    int mid=(L+R)>>1;
    int la=T[a].lc, lb=T[b].lc, lc=T[c].lc, ld=T[d].lc;
    int ra=T[a].rc, rb=T[b].rc, rc=T[c].rc, rd=T[d].rc;
    if(T[la].val^T[lb].val^T[lc].val^T[ld].val) return getans(la,lb,lc,ld,L,mid);
    else return getans(ra,rb,rc,rd,mid+1,R);
}
int query(int a,int b,int c,int d,int L,int R,int l,int r){
    if(L==l&&R==r){
        if(T[a].val^T[b].val^T[c].val^T[d].val) return getans(a,b,c,d,L,R);
        else return -1;
    }
    int mid=(L+R)>>1;
    if(r<=mid) return query(T[a].lc,T[b].lc,T[c].lc,T[d].lc,L,mid,l,r);
    if(l >mid) return query(T[a].rc,T[b].rc,T[c].rc,T[d].rc,mid+1,R,l,r);
    int ret=query(T[a].lc,T[b].lc,T[c].lc,T[d].lc,L,mid,l,mid);
    if(ret==-1) ret=query(T[a].rc,T[b].rc,T[c].rc,T[d].rc,mid+1,R,mid+1,r);
    return ret;
}
int main(){
    n=read(), q=read();
    for(int i=1; i<=n; i++) a[i]=read();
    for(int i=1; i<=n; i++) X[i]=RD();
    for(int i=1; i<n; i++){
        int u=read(),v=read();
        es[u].pb(v); es[v].pb(u);
    }
    rt[0]=build(1,n);
    dfs(1,0);
    while(q--){
        int u=read(),v=read(),l=read(),r=read();
        int lca=LCA(u,v), flca=pa[lca][0];
        printf("%d\n",query(rt[u],rt[v],rt[lca],rt[flca],1,n,l,r));
    }
    return 0;
}
```