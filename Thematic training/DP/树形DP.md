# 树形DP

---

## 蓝魔法师

### 题意

​	给出一棵树，求有多少种删边方案，使得删后的图每个连通块大小小于等于k。

​	$2\le n\le 2000, 1\le k\le 2000$

### 思路

​	树上背包。

​	定义$f[u][i]$：$u$子树全部合法，且$u$的所在联通块大小为$i$的方案数。

​	枚举每一条边，考虑删除或保留：

​	（1）删除：

​		$f[u][i]=f[u][i]\times \sum_{j=1}^{j=min(k,sz[v])}f[v][j]$。

​	（2）保留：	

​		背包DP。尝试所有合法的合并情况。

​		$f[u][i+j]=f[u][i+j]+(f[u][i]*f[v][j]),\ i+j\le k$

​	关于复杂度计算。对每个节点，复杂度为$\sum sz_l\times sz_r+\sum sz_i$，将每个$sz_i$看作是$i$个点，那么问题就转为求点匹配情况个数。由于任意两点仅会在其LCA上计算一次，所以整体复杂度$O(n^2)$。

### 代码

```c++
#include <bits/stdc++.h>
#define ll long long
#define pb push_back
using namespace std;
const int N = 2e3+10;
const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
vector<int> es[N];
int n, k;
int f[N][N]{0}, sum[N][N], sz[N];
int tmp[N];
void dfs(int u,int fa){
    f[u][1]=1; sz[u]=1;
    for(auto v: es[u]){
        if(v == fa) continue;
        dfs(v, u);
        for(int i=min(sz[u],k); i>0; --i){
            for(int j=min(sz[v],k-i); j>0; --j){
                tmp[i+j]=add(tmp[i+j],mul(f[u][i],f[v][j]));
            }
        }
        for(int i=k; i>0; --i){
            f[u][i]=add(tmp[i],mul(f[u][i],sum[v][k]));
            tmp[i]=0;
        }
        sz[u]+=sz[v];
    }
    sum[u][0] = 0;
    for(int i=1; i<=k; i++) sum[u][i]=add(sum[u][i-1],f[u][i]);
}
int main(){
    cin>>n>>k;
    for(int i=1; i<n; i++){
        int u, v; scanf("%d%d",&u,&v);
        es[u].pb(v);
        es[v].pb(u);
    }
    dfs(1,0);
    cout<<sum[1][k]<<endl;
}
```



----

