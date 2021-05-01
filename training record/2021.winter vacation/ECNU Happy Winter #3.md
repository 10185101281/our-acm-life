# ECNU Happy Winter #3

## 

### 思路

​	$E[Ans] = \sum xP(Ans=x)=\sum P(Ans\ge x)$

​	考虑现在要求$P(Ans>x)$，对于每个叶子，假如权值$\ge x$，将其权值设为$1$，否则设为0。现在相当于叶子中有$m-x+1$个1，$x-1$个0，问$F[1]=1$的概率。

​	设$f[u][x][0..1]$表示$u$的子树中有$x$个1，$F[u]=0/1$的方案数。有$P(Ans\ge x)=\frac{f[1][x][1]}{C_{m}^{x}}$。

​	转移时，考虑当前是谁，用1表示老板娘，0表示蟹老板。博弈DP即可。

​	最终答案为$\sum \frac{f[1][x][1]}{C_{m}^{x}}\times m!=\sum f[1][x][1]\times x!(m-x)!$

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;

const int mod = 1e9+7;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}

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

const int N = 5e3+10;
int fac[N];
void init(int n){
    fac[0]=1;for(int i=1; i<=n; i++) fac[i]=mul(fac[i-1],i);
}
vector<int> es[N];
int f[N][N][2], g[N][2];
int dfs(int x,int fa,int k){
    int cx=0, cy;
    for(auto &y: es[x]){
        if(y==fa) continue;
        cy=dfs(y,x,k^1);
        if(cx==0){
            memcpy(f[x],f[y],sizeof(f[x]));
            cx=cy;
            continue;
        }
        memset(g,0,sizeof(g));
        for(int i=0; i<=cx; i++){
            for(int j=0; j<=cy; j++){
                int d1 = mul(f[x][i][k^1],f[y][j][k^1]);
                int sum1 = add(f[x][i][k],f[x][i][k^1]);
                int sum2 = add(f[y][j][k],f[y][j][k^1]);
                int sum = mul(sum1, sum2);
                int d2 = sub(sum, d1);
                g[i+j][k^1] = add(g[i+j][k^1],d1);
                g[i+j][k] = add(g[i+j][k],d2);
            }
        }
        memcpy(f[x],g,sizeof(f[x]));
        cx+=cy;
    }
    if(cx==0){
        cx=1;
        f[x][0][0] = f[x][1][1] = 1;
    }
    return cx;
}
int main(){
    int n=read(); init(n);
    for(int i=1; i<n; i++){
        int x=read(), y=read();
        es[x].pb(y); es[y].pb(x);
    }
    int m=dfs(1,0,1);
    int ans = 0;
    for(int i=1; i<=m; i++){
        //cout<<i<<":"<<f[1][i][1]<<endl;
        ans = add(ans,mul(f[1][i][1],mul(fac[i],fac[m-i])));
    }
    printf("%d\n",ans);
    return 0;
}
```

---



