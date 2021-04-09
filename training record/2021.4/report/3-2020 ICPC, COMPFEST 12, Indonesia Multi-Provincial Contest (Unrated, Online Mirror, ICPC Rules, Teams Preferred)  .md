# 2020 ICPC, COMPFEST 12, Indonesia Multi-Provincial Contest (Unrated, Online Mirror, ICPC Rules, Teams Preferred)

## B Blue and Red of Our Faculty!

### 题解

​	预处理出所有的环。

​	设$f[i][j][k]$，表示到了第i个环，红蓝两色所占边之差为j的情况下：

- k=0，未相遇
- k=1，在环的点上相遇
- k=2，在环的边上相遇
- k=3，在1点相遇
- k=5，在1点边上相遇

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

const int mod = 1e9+7;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}

const int N = 2e3+10;
const int M = 4e3+10;
vector<int> es[N];
int v[N];
int dfs(int x){
    v[x]=1;
    for(auto &y: es[x]){
        if(v[y]) continue;
        return dfs(y)+1;
    }
    return 1;
}
int n, m, a[N];
int f[N][2*N+5][5];
const int Base = N;
inline int valid(int x){return abs(x-Base)<=m/2;}
int main(){
    n=read(), m=read();
    for(int i=1; i<=m; i++){
        int u=read(), v=read();
        es[u].pb(v); es[v].pb(u);
    }
    memset(v,0,sizeof(v));
    v[1]=1; int num=0;
    for(auto &x: es[1]){
        if(v[x]) continue;
        a[++num] = dfs(x)+1;
    }
    memset(f,0,sizeof(f));
    f[0][Base][0]=1;
    f[0][Base][3]=1;
    for(int i=1; i<=num; i++){
        for(int j=-m+Base; j<=m+Base; ++j){
            for(int t=0; t<5; t++){
                if(valid(j-a[i])) f[i][j-a[i]][t]=add(f[i][j-a[i]][t], f[i-1][j][t]);
                if(valid(j+a[i])) f[i][j+a[i]][t]=add(f[i][j+a[i]][t], f[i-1][j][t]);
                if(t<3) if(valid(j)) f[i][j][t]=add(f[i][j][t], f[i-1][j][t]);
            }

            //环上点
            for(int k=1; k<a[i]; k++){
                if(valid(j+k-(a[i]-k)))f[i][j+k-(a[i]-k)][1]=add(f[i][j+k-(a[i]-k)][1], mul(2,f[i-1][j][0]));
            }
            //环上边
            for(int k=1; k<a[i]-1; k++){
                if(valid(j+k-(a[i]-k-1)))f[i][j+k-(a[i]-k-1)][2]=add(f[i][j+k-(a[i]-k-1)][2], mul(2,f[i-1][j][0]));
            }
            //1上边
            if(valid(j+(a[i]-1))) f[i][j+(a[i]-1)][4]=add(f[i][j+(a[i]-1)][4], mul(2,f[i-1][j][3]));
            if(valid(j-(a[i]-1))) f[i][j-(a[i]-1)][4]=add(f[i][j-(a[i]-1)][4], mul(2,f[i-1][j][3]));
        }
    }

    int ans=0;
    for(int t=1; t<5; t++) ans=add(ans, f[num][Base][t]);
    printf("%d\n",ans);

    return 0;
}
```

