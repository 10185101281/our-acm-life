# 2017-2018 ACM-ICPC, Asia Daejeon Regional Contest

---

## A Broadcast Stations

### 题意

​	给一颗结点为$n$的树。可以选择一些点放上基站，如果u上的基站价值为d，那么距离u小于等于d的点都会被覆盖，问使得整棵树被覆盖的最小价值。

​	$1\le n\le 5\times 10^3$

### 思路

​	树上覆盖问题。讨论向下覆盖和向上覆盖两种情况。

​	（1）向下覆盖：

​		$g[u][j]$表示$u$点为根子树与$u$点距离$\ge j$的点都被覆盖最小代价。

​		可以直接转移：$g[u][j]=\sum g[v][j-1](j>0)$。

​	（2）向上覆盖：

​		$f[u][j]$表示将$u$点为根子树全覆盖，并且子树外与$u$点距离$\le j$的点都被覆盖的最小代价。

​		转移时考虑在$u$点设立辐射点，或不设立辐射点两种情况。

​		有$$

​		且最终有$g[u][0] = f[u][0]$。

### 代码

```c++
#include <bits/stdc++.h>
#define ll long long
#define pb push_back
#define fir first
#define sec second
using namespace std;
const int N = 5e3+10;
vector<int> es[N];

int n, f[N][N], g[N][N];
void dfs(int u,int fa){
    for(auto v: es[u]){
        if(v == fa) continue;
        dfs(v, u);
        for(int i=1; i<=n; i++) g[u][i] += g[v][i-1];
    }
    g[u][0] = f[u][n] = n;
    for(int i=n-1; i>=0; --i){
        f[u][i] = min(max(1,i)+g[u][max(1,i)+1], f[u][i+1]);
        for(auto v: es[u]){
            if(v == fa) continue;
            f[u][i] = min(f[u][i], f[v][i+1]+g[u][i+1]-g[v][i]);
        }
    }
    g[u][0] = f[u][0];
    for(int i=1; i<=n; i++){
        g[u][i] = min(g[u][i], g[u][i-1]);
    }
}

int main() {
    cin>>n;
    for(int i = 1; i<n; i++){
        int u, v; scanf("%d%d",&u,&v);
        es[u].pb(v); es[v].pb(u);
    }
    dfs(1,0);
    cout<<g[1][0]<<endl;
    return 0;
}
```

