#树形DP

---

## Independent Set（cf-630-div2 F）

## 题意

​	给一棵有$n$节点的树，问它的所有边子集的所有点独立集的情况数是多少。

### 思路

​	问题实际上是两层染色。第一层染色是找边子集，第二层染色是在边子集的基础上找独立集。

​	先考虑找边子集的问题。以“每个点作为根的子树”为阶段，维护两种状态：

​	（1）在所有子节点为根的子树合法情况下，当前点不与任何子节点相连，设为$dp[x][0]$。

​	（2）在所有子节点为根的子树合法情况下，当前点可以与与任何子节点相连，设为$dp[x][1]$。（注意是可以，不是必须）

​	显然，当前结点为根的子树合法的情况为$dp[x][1]-dp[x][0]$。

​	然后考虑如何递推：

​	（1）$dp[x][0]=\Pi (dp[y][1]-dp[y][0])$，即所有子节点为根子树合法情况的累积。

​	（2）$dp[x][1]=\Pi (dp[y][1]-dp[y][0]+dp[y][0])=\Pi dp[y][1]$。即如果向子节点连边的话，那么子节点会贡献$dp[y][0]$，否则子节点贡献$dp[y][1]-dp[y][0]$。

​	注意，为了方便DP，将空集作为合法方案。最后减去即可。

​	然后回到原题。原题实际上是找边子集问题的强化，我们仍可以考虑用找边子集的思路来处理。

​	仍以“每个点作为根的子树”为阶段，维护三种状态：

​	（1）所有子节点代表子树合法情况下，当前节点可以与子节点连边，但不取到独立集中，设为$dp[x][0]$。

​	（2）所有子节点代表子树合法情况下，当前节点可以与子节点连边，且取到独立集中，设为$dp[x][1]$。

​	（3）所有子节点代表子树合法情况下，当前节点不与任何子节点连边，且取到独立集中，设为$dp[x][2]$。

​	与前问题相似，$dp[x][1]$包含着“x不与任何子节点相连但被取到独立集中”的不合法状态，减掉$dp[x][2]$即可。因此该子节点该表子树所有合法取法有$dp[y][0]+dp[y][1]-dp[y][2]$。

​	然后考虑如何递推，不妨设$sum[y]=dp[y][0]+dp[y][1]-dp[y][2]$。

​	（1）$dp[x][0]=\Pi (dp[y][1]+dp[y][0]+sum[y])$：

​	如果$x$和$y$相连，$y$可以被染色也可以不被染色，那么$y$贡献的方案为：$dp[y][0]+dp[y][1]$（由于和$x$相连，原本$dp[y][0]$不合法的情况将会合法）。

​	如果$x$不与$y$相连，那么$y$贡献的方案为：$sum[y]$。

​	（2）$dp[x][1]=\Pi(dp[y][0]+sum[y])$：

​	如果$x$和$y$相连，那么$y$不可以被染色，因此$y$贡献的方案为：$dp[y][0]$。

​	如果$x$不与$y$相连，那么$y$贡献的方案为：$sum[y]$。

​	（3）$dp[x][2]=\Pi sum[y]$：

​	因为$x$不与$y$相连，所以$y$贡献的方案为：$sum[y]$。

​	同样的，为了方便DP，我们考虑空集作为合法方案，所以对于所有的状态，初始化都为$1$。最后注意需要剪去空集的情况即可。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int mod = 998244353;
const int N = 3e5+10;
int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
int mul(int a,int b){return 1LL*a*b%mod;}
int sub(int a,int b){return a<b? a-b+mod: a-b;}
int dp[N][3];
vector<int> es[N];
void dfs(int x,int fa){
    for(int i = 0; i<3; i++) dp[x][i] = 1;
    for(auto &y: es[x]){
        if(y == fa) continue;
        dfs(y,x);
        int sum = sub(add(dp[y][0],dp[y][1]),dp[y][2]);
        dp[x][0] = mul(dp[x][0], add(add(dp[y][0],dp[y][1]),sum));
        dp[x][1] = mul(dp[x][1], add(dp[y][0],sum));
        dp[x][2] = mul(dp[x][2], sum);
    }
}
int main(){
    int n; cin>>n;
    for(int i = 1; i<n; i++){
        int x, y; scanf("%d%d",&x,&y);
        es[x].pb(y);
        es[y].pb(x);
    }
    dfs(1,0);
    int ans = sub(add(dp[1][0],dp[1][1]), dp[1][2]);
    cout<<sub(ans,1)<<endl;
}
```

-----

 ## Tree（2020hdu多校-5）

### 题意

​	给一个有n结点的树，每个边都有权重（为非负整数）。给出k，问满足以下条件的最大总权重的子图G：

​	（1）G连通

​	（2）度数大于k的顶点数不大于1。

​	$1≤n≤2×10^5,0≤k<n, ∑n≤10^6$

### 思路

​	考虑树形DP。以“每个点作为根的子树”为阶段，维护两种状态：

​	（1）在当前结点和父亲结点相连合法的情况下，当前结点的联通块中不含有度数大于k的顶点。

​	（2）在当前结点和父亲结点相连合法的情况下，当前结点的联通块中含有度数大于k的顶点。

​	限制条件“在当前结点和父亲结点相连合法的情况下”，是为了保证在讨论每个结点和子节点相连时，只需要考虑父亲结点的连接方式是否合法。具体来说，如果当前结点的联通块中不含有度数大于k的结点，那么当前结点与其子节点连边的数量需$\le k-1$，这样才能保证当前结点和父亲结点相连后当前结点度数$\le k$；同样的，如果如果当前结点的联通块中含有度数大于k的结点，如果这个“度数大于k的结点”就是当前结点，那么我们就不需要对其连边进行限制，否则那么当前结点与其子节点连边的数量需$\le k-1$。

​	然后我们考虑如何递推。不妨设两个状态分别为$f[x][0],f[x][1]$。

​	（1）对于第一种状态：

​	一定是贪心与$\le k-1$个第一种状态子结点连边，假设当前节点与子节点边数为cnt，那么就是$\sum^{min(cnt,k-1)}_{i=1}f[y_i][0]+w_{xy_i}$。对$f[y][0]+w_{xy}$排序一下即可贪心。

​	（2）对于第二种状态：

​	我们需要分类讨论这个“度数大于k的结点”是否是当前节点。

​	如果是：

​	一定是贪心地与所有的第一种状态子节点连边，即$\sum^{cnt}_{i=1}f[y_i][0]+w_{xy_i}$。

​	如果不是：

​	从贪心地角度来讲，一定是在与$\le k-1$个第一种状态子结点连边的基础上（即$\sum^{min(cnt,k-1)}_{i=1}f[y_i][0]+w_{xy_i}$），选择某个点删除，然后连一个第二种状态子结点。暴力枚举复杂度很高，可以考虑维护前缀（后缀）和来优化。假如这个被选择的“第二种状态子结点”是我们前面选择的“一种状态子结点”中的一个，那么我们只需要取$f[y_i][1]-f[y_i][0]$差值最大的那个即可，这个差值可以用前缀和维护最大值。假如这个被选择的“第二种状态子结点”不是我们前面选择的“一种状态子结点”中的一个，那么我们删除的节点必然是$f[y_i][0]+w_{xy_i}$最小的那一个，增加的节点必然是$f[y_i][1]+w_{xy_i}$最大的那一个，对$f[y_i][1]+w_{xy_i}$我们再维护一个后缀最大即可。

​	最后，我们还要处理一种情况。为了方便递推，我们给每个结点的状态都增加了“在当前结点和父亲结点相连合法的情况下”的限制。那么有一种情况我们没有考虑：某个结点可能和该结点的父亲相连。对于这种情况，无需再增添一维状态（因为它无法更新父亲的信息），可以直接在dfs过程中更新答案。具体的求法同$f[x][1]$，只是对于“度数大于k的结点不是当前节点”的情况，向子结点连边的限制改为$\le k$。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
using namespace std;
const int N = 2e5+10;
const ll inf = 1e18;
int n, k;
vector<pii> es[N];
ll ans = 0, f[N][2];
bool cmp(pll a, pll b){
    if(a.fir == b.fir) return a.sec > b.sec;
    else return a.fir > b.fir;
}
pll a[N]; int cnt;
ll prefix[N], suffix[N];
void init(){
    sort(a+1, a+1+cnt, cmp);
    prefix[0] = 0;
    for(int i = 1; i<=cnt; i++) prefix[i] = max(prefix[i-1], a[i].sec-a[i].fir);
    suffix[cnt+1] = 0;
    for(int i = cnt; i>=1; --i) suffix[i] = max(suffix[i+1], a[i].sec);
}
ll cal(int num){
    if(num <= 0) return 0;
    ll ret = 0;
    int t = min(num, cnt);
    for(int i = 1; i<t; i++){
        ret += a[i].fir;
    }
    return max(ret+a[t].fir+prefix[t], ret+suffix[t]);
}
void dfs(int x,int fa){
    for(auto &e: es[x]){
        if(e.fir == fa) continue;
        dfs(e.fir, x);
    }
    cnt = 0;
    for(auto &e: es[x]){
        if(e.fir == fa) continue;
        a[++cnt] = pll{f[e.fir][0]+e.sec, f[e.fir][1]+e.sec};
    }
    init();
    f[x][0] = f[x][1] = 0;
    for(int i = 1; i<=min(cnt, k-1); i++) f[x][0] += a[i].fir;
    for(int i = 1; i<=cnt; i++) f[x][1] += a[i].fir;
    f[x][1] = max(f[x][1], cal(k-1));
    ans = max(ans, f[x][0]);
    ans = max(ans, f[x][1]);
    ans = max(ans, cal(k));
}
int main(){
    int T; cin>>T;
    while(T--){
        scanf("%d%d",&n,&k);
        for(int i = 1; i<=n; i++) es[i].clear();
        for(int i = 1; i<n; i++){
            int x, y, z; scanf("%d%d%d",&x,&y,&z);
            es[x].pb(pii{y,z});
            es[y].pb(pii{x,z});
        }
        if(k == 0){
            puts("0"); continue;
        }
        ans = 0;
        dfs(1, 0);
        printf("%lld\n",ans);
    }
}
```

------

## Groundhog and Apple Tree（2020牛客多校-9）

### 题意

​	给一棵n结点的树。初始$hp=0$，经过一条边会掉$w_i\ hp$，每条边最多经过两次。第一次到达一个点可以回血$a_i$。 在任何时刻都可以休息，每$1s$可以回复$1hp$。任何时刻血不能小于$0$

​	求从起点（1号点）经过所有点再回到起点到最小时间。

​	$1⩽T⩽1000,1⩽n⩽10^5,∑n⩽10^6,0⩽a_i,w_i<2^{31}$

### 思路

​	因为每个边只能经过两次，即进入子结点一次和从子结点回到父结点一次。所以对于每个点，一旦进入子结点必须将子结点为根的子树完全遍历才能回到父结点，也即到达每个点必须将其子树完全遍历。因此可以考虑以“每个点作为根的子树”为阶段进行树形DP。

​	考虑如何进行转移。注意到，一旦通过休息恢复$hp$，在后面无法消除这个花费，因此转移具有最优子结构的性质，所以可以考虑贪心。对每个子树记录两个属性：最少要通过休息恢复的$hp$和在此基础上的收益，也即花费和收益。这样转移的模型可以简化为：有若干个物品，每取一个物品有一个花费和一个收益，如果当前的现金（$hp$）不足可以先贷款（贷款不能通过后面的收益补上），求最小的贷款。将物品分为两类：收益>花费；花费>=收益。显然前者一定要优先取。

​	（1）收益>花费：花费小的要先选，因为其带来的收益可能会在后面取花费更大的物品时用到。

​	（2）花费>=收益：收益大的要先选，因为最终的收益越小，总贷款的数额越小。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define pb push_back
#define pii pair<int, int>
#define pll pair<ll, ll>
#define pss pair<string, string>
#define pdd pair<double, double>
#define fir first
#define sec second
const int N = 1e5+10;
vector<pii> es[N];
ll a[N];
bool cmp1(pll x, pll y){
    return x.fir < y.fir;
}
bool cmp2(pll x, pll y){
    return x.sec > y.sec;
}
pll dfs(int x,int fa,ll d){
    vector<pll> vec1, vec2;
    for(auto e: es[x]){
        if(e.fir == fa) continue;
        pll tmp = dfs(e.fir, x, e.sec);
        if(tmp.sec > tmp.fir) vec1.pb(tmp);
        else vec2.pb(tmp);
    }
    ll sum1 = d, sum2 = a[x];
    sort(vec1.begin(), vec1.end(), cmp1);
    sort(vec2.begin(), vec2.end(), cmp2);
    for(auto &pr: vec1){
        if(sum2 < pr.fir){
            sum1 += pr.fir - sum2;
            sum2 = pr.fir;
        }
        sum2 += pr.sec-pr.fir;
    }
    for(auto &pr: vec2){
        if(sum2 < pr.fir){
            sum1 += pr.fir - sum2;
            sum2 = pr.fir;
        }
        sum2 += pr.sec-pr.fir;
    }
    if(sum2 >= d) return pll{sum1, sum2-d};
    else return  pll{sum1+d-sum2, 0};
}
int main(){
    int T; cin>>T;
    while(T--){
        int n; scanf("%d",&n);
        for(int i = 1; i<=n; i++) scanf("%d",a+i);
        for(int i = 1; i<=n; i++) es[i].clear();
        for(int i = 1; i<n; i++){
            int x, y, z;  scanf("%d%d%d",&x,&y,&z);
            es[x].pb(pii{y, z});
            es[y].pb(pii{x, z});
        }
        printf("%lld\n",dfs(1, 0, 0).fir);
    }
}
```

-----

## Identical Trees（2020牛客多校-10）

### 题意

​	给两个结点个数为$n$的有根树。求一个两树同构的匹配方式（保证存在），使得相应位置点标号不同的位置最少。

​	$1\le n\le 500$

### 思路

​	首先思考如何判断树的同构。对于有根树，因为根已经确定，可以直接递归向下搜索，只要保证向下搜索过程中任意一对结点向子结点连边个数相同即可确定为为同构。（如果为无根树，可以考虑分别尝试两个重心作为根，转化为有根树）

​	然后考虑如何使得代价最小。考虑树形DP。对于每对结点计算出其匹配的最小代价。

​	具体来讲。对于每对结点，我们考虑先处理出其子结点的对应关系。在向下递归时，$O(E^2)$的枚举哪个子树和哪个子树相匹配，并计算出匹配的代价。得到代价后，可以建立二分图模型，问题转化为二分图带权匹配。因为如果同构必然存在完美匹配，所以可以直接使用KM算法。（实现时，可以将不匹配的点设为无穷大，因为必然有解所以不会影响最终答案）

​	因为每对点最多匹配一次（所以不需要记忆化），所以复杂度为$O(N^2+N^3)$。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define pb push_back
#define pii pair<int, int>
#define pll pair<ll, ll>
#define pss pair<string, string>
#define pdd pair<double, double>
#define fir first
#define sec second
#define FOR(i, x, y) for (decay<decltype(y)>::type i = (x), _##i = (y); i < _##i; ++i)
const int N = 5e2+10;
const int inf = 1e3;
namespace R {
    const int M = N;
    const int INF = 2e9;
    int n;
    int w[M][M], kx[M], ky[M], py[M], vy[M], slk[M], pre[M];

    ll KM() {
        fill(ky, ky + n + 1, 0);
        fill(kx, kx + n + 1, 0);
        fill(py, py + n + 1, 0);

        FOR (i, 1, n + 1)
        FOR (j, 1, n + 1)
        kx[i] = max(kx[i], w[i][j]);
        FOR (i, 1, n + 1) {
            fill(vy, vy + n + 1, 0);
            fill(slk, slk + n + 1, INF);
            fill(pre, pre + n + 1, 0);
            int k = 0, p = -1;
            for (py[k = 0] = i; py[k]; k = p) {
                int d = INF;
                vy[k] = 1;
                int x = py[k];
                FOR (j, 1, n + 1)
                if (!vy[j]) {
                    int t = kx[x] + ky[j] - w[x][j];
                    if (t < slk[j]) { slk[j] = t; pre[j] = k; }
                    if (slk[j] < d) { d = slk[j]; p = j; }
                }
                FOR (j, 0, n + 1)
                if (vy[j]) { kx[py[j]] -= d; ky[j] += d; }
                else slk[j] -= d;
            }
            for (; k; k = pre[k]) py[k] = py[pre[k]];
        }
        ll ans = 0;
        FOR (i, 1, n + 1) ans += kx[i] + ky[i];
        return ans;
    }
}

vector<int> es1[N], es2[N];
int f[N][N];
int dfs(int u1,int u2){
    f[u1][u2] = (u1!=u2);
    int u1sz = es1[u1].size(), u2sz = es2[u2].size();
    if(u1sz != u2sz) return f[u1][u2] = inf;
    if(!u1sz) return f[u1][u2];
    for(int i = 0; i<u1sz; i++){
        for(int j = 0; j<u2sz; j++){
            int v1 = es1[u1][i], v2 = es2[u2][j];
            dfs(v1, v2);
        }
    }
    R::n = u1sz;
    for(int i = 0; i<u1sz; i++){
        for(int j = 0; j<u2sz; j++){
            int v1 = es1[u1][i], v2 = es2[u2][j];
            R::w[i+1][j+1] = -f[v1][v2];
        }
    }
    f[u1][u2] -= R::KM();
    return f[u1][u2];
}
int main(){
    int n; cin>>n;
    int r1 = 0, r2 = 0;
    for(int i = 1; i<=n; i++){
        int p; scanf("%d",&p);
        if(p) es1[p].pb(i);
        else r1 = i;
    }
    for(int i = 1; i<=n; i++){
        int p; scanf("%d",&p);
        if(p) es2[p].pb(i);
        else r2 = i;
    }
    printf("%d\n",dfs(r1,r2));
    return 0;
}
```

----

## Broadcast Stations

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

---

## Yue Fei's Battle

 ### 题意

​	求给出$k$，求满足以下条件非同构树的个数。

​		（1）直径为$k$

​		（2）每个结点最多有3条连边。

​	$T\le 25, k\le 10^5$

### 思路

将直径从中间切开，得到的子树必定为二叉树。

考虑在二叉树上dp。

设

​	（1）$f[i]$：深度为$i$的不同构的二叉树个数；

​	（2）$sum[i]$：深度不超过$i$不同构二叉树个数。

分情况讨论：

​	（1）只有一个分支深度为$i-1$：$f[i-1]\times sum[i-2]$；

​	（2）两个分支深度都为$i-1$：

​		（2.1）两个分支不一样：$f[i-1]\times (f[i-1]-1)/2$；

​		（2.2）两个分支一样：$f[i-1]$；

然后分情况考虑直径切开后的情况：

​	（1）若$k$为偶数，得到两个深度为$k/2$的二叉树：$f[k/2]\times (f[k/2]+1)/2$；

​	（2）若$k$为奇数，切开后得到三个二叉树：

​		（2.1）三个分支有一个分支深度$\le k/2$：$f[k/2]\times (f[k/2]+1)/2\times sum[k/2-1]$；

​		（2.2）三个分支深度均为$k/2$：

​			（2.2.1）三个分支都相同：$f[k/2]$；

​			（2.2.2）三个分支中有2个相同：$f[k/2]\times (f[k/2]-1)$；

​			（2.2.3）三个分支互不相同：$C(f[k/2],3)$。

### 代码

```c++
#include <bits/stdc++.h>
#define pii pair<int,int>
#define fir first
#define sec second
#define pb push_back
#define ll long long
using namespace std;
const int mod = 1e9+7;
const int N = 1e5+10;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
int qpow(int a,int b){
    int ret = 1;
    for(;b; b>>=1){
        if(b & 1) ret = mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}
inline int rev(int x){return qpow(x, mod-2);}
int f[N], sum[N], t = -1, inv2, inv6;
void init(){
    f[0] = 1, f[1] = 1;
    sum[0] = 1, sum[1] =2;
    inv2=rev(2), inv6=rev(6);
    for(int i = 2; i<N; i++){
        f[i] = mul(f[i-1],sum[i-2]);
        f[i] = add(f[i], mul(mul(f[i-1],add(f[i-1],1)),inv2));
        sum[i] = add(sum[i-1], f[i]);
    }
}
inline int cal(int x){
    return mul(mul(x, mul(sub(x,1),sub(x,2))), inv6);
}
int main(){
    init();
    int k;
    while(scanf("%d",&k) == 1){
        if(k == 0) break;
        if(k & 1){
            int p = k/2;
            int ans = mul(mul(mul(f[p], add(f[p],1)), inv2), sum[p-1]);
            ans = add(ans, mul(f[p], f[p]));
            ans = add(ans, cal(f[p]));
            printf("%d\n", ans);
        } else {
            int p = k/2;
            printf("%d\n",mul(mul(f[p], add(f[p],1)), inv2));
        }
    }
}
```

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

