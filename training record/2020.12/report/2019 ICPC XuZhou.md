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

