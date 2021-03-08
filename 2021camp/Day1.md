# Day 1

---

## 1 波浪（G）

### 描述

​	对一个排列$P[1..N]$，定义其波动强度$L=|P_2-P_1|+|P_3-P_2|+...+|P_{N}-P_{N-1}|$。

​	给出$N,M$，问随机$1...N$的排列，波动强度不小于$M$的概率

​	$N\le 100, 0\le M\le 2147483647$

### 题解

​	填坑DP

​	$f[i][j][k]$填1-i个数，分为j段，代价为k的方案数。

​	计算代价时考虑分别计算每个数的贡献。

​	转移：

​	（1）另起一段

​	（2）合并两段

​	（3）贴在其中一段

---

## 2 隐蔽的居所

### 描述

​	一个圆上有$N$户人家。

​	相邻两户人家编号$i,j$满足$|i-j|\le K$

​	有M个“不喜欢”关系，如果$i$不喜欢$j$，则$j$不可以住在$i$顺时针方向下一个位置。

​	问方案数。

​	$1\le N\le 10^6,0\le M\le 10^5,0\le K\le 3$

### 题解

​	填坑DP

---

## 3 New Year and Original Order（GXB）

### 描述

​	定义$S(n)$为对$n$每位排序后的到的数，例如$S(50394) = 3459$。

​	给出一个数$X$，计算$\sum_{1\le k\le X}S(k)$

​	$1\le X\le 10^{700}$

https://www.cnblogs.com/heyujun/p/10225419.html

---

## 4 Game on Chessboard（BL）

### 描述

​	给一个$n\times n$的棋盘。

​	棋盘上有棋子。每个棋子有一个颜色（白、黑）和一个值$w$。

​	需要将这些棋子移走。一个棋子能被移走当且仅当其左下区域没有棋子。移走棋子有两种操作：

​		（1）每次移走一个黑棋$u$一个白棋$v$，代价为$｜w_u-w_v｜$。

​		（2）直接移走一个棋子$u$，代价为$w_u$。

​	计算移走棋子的最小代价。

​	$1\le n\le 12$。

### 题解

​	轮廓线DP。

```c++
#include <bits/stdc++.h>
using namespace std;
#define pb push_back
#define fir first
#define sec second
#define pii pair<int, int>
#define ll long long
const int N = 15;
const int M = (1<<24)+10;

char g[N][N]; int w[N][N];
int f[M];
int bitcnt(int x){
    int ret = 0;
    for(; x; x-=x&-x) ret++;
    return ret;
}
vector< pair<int,pii> > vec;
int main(){
    int n;
    while(scanf("%d",&n) == 1) {
        for (int i = 0; i < n; i++) scanf("%s", g[i]);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                scanf("%d", &w[i][j]);
            }
        }
        int t = (1<<n) - 1, s = t << n;
        //memset(f, 0x3f, sizeof(f));
        for(int st=s; st>=t; --st) f[st] = 0x3f3f3f3f;
        f[s] = 0;
        for (int st = s; st >= t; --st) {
            if (bitcnt(st) != n) continue;
            vec.clear();
            int x = n - 1, y = n;
            for (int i = 0; i < 2 * n - 1; i++) {
                if ((st >> i) & 1) x--;
                else y--;
                if (((st >> (i + 1)) & 1) == 0 || ((st >> i) & 1) == 1) continue;
                f[st - (1 << i)] = min(f[st - (1 << i)], f[st] + (g[x][y] != '.') * w[x][y]);
                if(g[x][y] == '.') continue;
                for(auto &pr: vec){
                    int j=pr.fir, tx = pr.sec.fir, ty = pr.sec.sec;
                    if (g[x][y] + g[tx][ty] != 'W' + 'B') continue;
                    f[st - (1 << i) - (1 << j)] = min(f[st - (1 << i) - (1 << j)], f[st] + abs(w[x][y] - w[tx][ty]));
                }
                vec.pb(pair<int,pii>{i,pii{x, y}});
            }
        }
        printf("%d\n", f[t]);
    }
    return 0;
}
```



----

## 5 Fountains（BL）

### 题目

https://codeforces.ml/gym/102900/problem/F

### 题解

​	每个区间一定贪心的取最大的可行区间。

​	对所有可选择区间按值排序，状压DP。

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
const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
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

const int N=15;
const int M=105;
struct node{
    int l, r; ll x;
};
bool cmp(node a, node b){
    return a.x > b.x;
}
ll s[N]{0}, ans[M];
ll code(int n,int a[]){
    ll ret=a[n+1];
    for(int i=n; i>=1; --i) ret=ret*10+a[i];
    return ret;
}
void decode(ll x,int n,int a[]){
    for(int i=1; i<=n; i++) a[i]=x%10, x/=10;
    a[n+1]=x;
}
map<ll,ll> f[2];
vector<node> vec;
int main(){
    int n=read();
    for(int i=1; i<=n; i++) s[i]=read()+s[i-1];
    ll sum=0;
    for(int l=1; l<=n; l++){
        for(int r=l; r<=n; r++){
            sum+=s[r]-s[l-1];
            vec.pb(node{l,r,s[r]-s[l-1]});
        }
    }
    sort(vec.begin(),vec.end(),cmp);
    int a[15];
    for(int i=1; i<=n; i++) a[i]=n; a[n+1]=0;
    f[1][code(n,a)]=0;
    for(auto seg: vec) {
        int l=seg.l, r=seg.r; ll val=seg.x;
        f[0] = f[1];
        for (auto &pr: f[0]) {
            ll x = pr.fir, y = pr.sec;
            decode(x, n, a);
            for (int i=1; i<=l; i++) {
                if (a[i] >= r) {
                    y += (a[i]-r+1) * val;
                    a[i] = r-1;
                }
            }
            a[n+1]++;
            ll nx = code(n, a);
            f[1][nx] = max(f[1][nx], y);
        }
    }
    memset(ans,0,sizeof(ans));
    for(auto &pr: f[1]){
        ll x=pr.fir, y=pr.sec;
        decode(x,n,a);
        ans[a[n+1]]=max(ans[a[n+1]],y);
    }
    int m=n*(n+1)/2;
    for(int i=1; i<=m; i++) printf("%lld\n",sum-ans[i]);
    return 0;
}
```

---

## 6 Sum

### 做法1 分治DP-bl

​	分治，背包DP。

```c++
#include <bits/stdc++.h>

using namespace std;
#define ll long long
#define pb push_back
#define fir first
#define sec second

const int N = 3e3+10;
int n, k, t[N]; ll a[N][N]{0};
ll f[N];
ll ans = 0;
void dp(int l,int r){
    if(l == r){
        //cout<<l<<":"<<endl;
        //for(int j=0; j<=k; j++) cout<<f[j]<<' '; cout<<endl;
        for(int i=0; i<=t[l]; i++){
            ans = max(ans, f[k-i]+a[l][i]);
        }
        return ;
    }

    int mid = l+r>>1;

    ll tf[N];
    for(int i=0; i<=k; i++) tf[i] = f[i];
    for(int i=l; i<=mid; i++){
        for(int j=max(0, k-t[i]); j>=0; --j){
            f[j+t[i]] = max(f[j+t[i]],f[j]+a[i][t[i]]);
        }
    }
    dp(mid+1, r);

    for(int i=0; i<=k; i++) f[i]= tf[i];
    for(int i=mid+1; i<=r; i++){
        for(int j=max(0, k-t[i]); j>=0; --j){
            f[j+t[i]] = max(f[j+t[i]],f[j]+a[i][t[i]]);
        }
    }
    dp(l,mid);
}
int main(){
    cin>>n>>k;
    for(int i=1; i<=n; i++){
        scanf("%d",t+i);
        for(int j=1; j<=t[i]; j++){
            ll x; scanf("%lld",&x);
            if(j<=k){
                a[i][j] = a[i][j-1] + x;
            }
        }
        t[i] = min(t[i], k);
    }
    memset(f, 0xcf, sizeof(f));
    f[0] = 0;
    dp(1,n);
    cout<<ans<<endl;
    return 0;
}
```

### 做法2 退背包-gxb

---

## 7 Lucky Numbers（BL）

https://codeforces.com/problemset/problem/1428/G2

### 题解

​	将数看作物品，数的数量是物品的个数，数的大小是物品的重量，数在表中映射是物品的价值。那么就是一个分组多重背包问题。

​	现在限制了恰好画k个数，且数的和为n。显然，对于每个数位，贪心地考虑最多只有一个数在这个位置不为3的倍数。那么对于k-1个数，我们按照上面的方法进行分组多重背包，最后的一个数预处理其价值即可。

​	直接多重分组背包的复杂度是$O(6MlogK^3)$，注意到体积为6，9的物品都可以拆成为 3 的物品，就变成最多选 3\*(k-1) 个物品了，复杂度进一步优化到$O(6Mlog(3K))$

```c++
#include <bits/stdc++.h>
using namespace std;

#define ll long long
const int M = 1e6;
ll F[10];
ll f[M+100]; 
int main(){
    int k; scanf("%d",&k);
    for(int i=0; i<6; i++) scanf("%lld",F+i);
    memset(f, 0, sizeof(f));
    for(int i=0; i<M; i++){
        int d = 0, x = i;
        while(x){
            int now = x%10;
            if(now%3==0) f[i] += F[d]*(now/3);
            d++;
            x/=10;
        }
    }
    int base = 3;
    for(int i=0; i<6; i++){
        for(int j=1; j<=3; j++){
            int res = min(k-1, (M-1)/base);
            for(int t=1; t<res; t<<=1){
                int d = base*t; res -= t;
                for(int p=M-1; p>=0; --p){
                    if(p < d) break;
                    f[p] = max(f[p],f[p-d]+ F[i]*t);
                }
            }
            int d = base*res;
            for(int p=M-1; p>=0; --p){
                if(p < d) break;
                f[p] = max(f[p],f[p-d]+ F[i]*res);
            }
        }
        base = base*10;
    }
    int q; scanf("%d",&q);
    while(q--){
        int n; scanf("%d",&n);
        printf("%lld\n",f[n]);
    }
    return 0;
}
```

---

## 8 String Transformation（BL）

### 题目

​	https://codeforces.com/problemset/problem/1383/C

### 题解

​	首先对需要转化的字母关系建图。

​	对每个联通图分别求解：

​	假设当前联通图点为$n$，最大DAG大小为$|LDAG|$，则最少操作为$2*n-|LDAG|-1$。

​	证明：

​		（1）如果对于该联通图，存在一个$k$的可行操作（实际上即选择k个边），那么必然可以得到一个大小为$2*n-k+1$的DAG。

​		（2）考虑增量构造：当加入$(u,v)$时，如果u,v已经联通，则将v从图中删除，否则将两个点的图联通。

​		（3）由于这是可行操作，最终的图必然是联通的。使图联通的边数为$n-1$，那么删减的点数为$k-n+1$，所以这个构造出的DAG大小为$n-(k-n+1)=2*n-k-1$。

​		（4）即$|LDAG|\ge 2*n-k-1$，那么有$k\le 2*n-|LDAG|-1$。（必要）

​		（5）当我们找到最大DAG时，令不在这个DAG上的点连成一个环路，即可构造出操作数为$2*n-|LDAG|-1$的方案。（充分）

​	问题转化为对联通图求解最大DAG，复杂度$O(n\times 2^n)$。

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
const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
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
const int M=22;
int g[M][M];
vector<int> es[M];
char s1[N], s2[N];
int mp[M], rch[M], f[1<<M];
int bitcnt(int x){int ret=0;for(int i=x; i; i-=i&-i) ret++; return ret;}
int solve(int G){
    int n=0, cnt=0;
    for(int x=0; x<20; x++) if((G>>x)&1) n++, mp[x]=cnt++;
    for(int x=0; x<20; x++){
        if((G>>x)&1){
            rch[mp[x]]=0;
            for(auto &y: es[x]) rch[mp[x]]|=(1<<mp[y]);
        }
    }
    int LDAG=0, MK=(1<<n);
    for(int i=0; i<MK; i++) f[i]=0;
    f[0]=1;
    for(int i=1; i<MK; i++){
        for(int j=0; j<n; j++){
            if((i>>j)&1){
                f[i]|=f[i^(1<<j)]&&((i&rch[j])==0);
            }
        }
        if(f[i]) LDAG=max(LDAG,bitcnt(i));
    }
    return 2*n-LDAG-1;
}
int fa[M];
int get(int x){return fa[x]==x? x: fa[x]=get(fa[x]);}
int main(){
    int T=read();
    while(T--){
        int n=read();
        scanf("%s%s",s1+1,s2+1);
        for(int i=0; i<20; i++) es[i].clear();
        for(int i=0; i<20; i++) fa[i]=i;
        memset(g,0,sizeof(g));
        for(int i=1; i<=n; i++){
            int x=s1[i]-'a', y=s2[i]-'a';
            if(x==y||g[x][y]) continue;
            es[x].pb(y); g[x][y]=1;
            int fx=get(x), fy=get(y);
            if(fx!=fy) fa[fx]=fy;
        }
        int ans=0;
        for(int i=0; i<20; i++){
            int G=0;
            for(int j=0; j<20; j++){
                if(get(j)==i) G|=(1<<j);
            }
            if(G) ans+=solve(G);
        }
        printf("%d\n",ans);
    }
    return 0;
}
```

---

## 9 Financiers Game（BL）

### 描述

​	两人从长度为n数列两端取数。一个人取了k个数，下一个人须取k或k+1个。最开始可以取1个或2个，不能操作时结束。

​	两人都希望自己取到的数字之和尽量大，并保持绝对理智。

​	求最后取到的数字之和之差。

### 题解-bl

​	设$f[l][r][k][0/1]$表示左侧取到$l$，右侧取到$r$，当前$k$值，操作方。

​	由于$k$最多为$\sqrt N$级别，$l,n-r$相差最多为$k$，实际复杂度为$O(N^2)$。

​	状态Hash。

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

const int mod = 998244353;
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
const int N = 4e3+5;
const int M = 5e7;
const int inf = 1e9;
int Hash(int a,int b,int c,int d){
    static ll t1=4000, t2=100, t3=2;
    return ((((a*t1)+b)*t2+c)*t3+d)%M;
}
int a[N], sum[N]{0}, f[M+10];
inline int cal(int l,int r){
    return sum[r]-sum[l-1];
}
int dfs(int l,int r,int k,int t){
    if(r-l+1<k) return 0;
    int s=Hash(l,r,k,t);
    if(f[s]!=inf) return f[s];
    int ret;
    if(t==0){
        ret = dfs(l+k,r,k,1)+cal(l,l+k-1);
        if(r-l+1>=k+1) ret=max(ret,dfs(l+k+1,r,k+1,1)+cal(l,l+k));
    } else {
        ret = dfs(l,r-k,k,0)-cal(r-k+1,r);
        if(r-l+1>=k+1) ret=min(ret,dfs(l,r-k-1,k+1,0)-cal(r-k,r));
    }
    return f[s]=ret;
}
int main(){
    int n=read();
    for(int i=1; i<=n; i++) a[i]=read(), sum[i]=sum[i-1]+a[i];
    for(int i=0; i<M; i++) f[i]=inf;
    printf("%d\n",dfs(1,n,1,0));
    return 0;
}
```

---

## 10 The Hanged Man（HDU 6566）

### 题目

http://acm.hdu.edu.cn/showproblem.php?pid=6566

### 题解

#### DFS序 重链剖分-GXB

https://www.cnblogs.com/SovietPower/p/14418118.html

#### 优先递归重儿子-BL



---

## 11 Cell Blocking

### 题目

https://codeforces.com/gym/102538/problem/C

### 题解

​	分类讨论。

- 如果一开始就不联通，那么答案为$\frac{cnt\times (cnt-1)}{2}$，$cnt$为空地个数。

- 否则，预处理出每个斜对角线上的可行点（可达起点和终点）的位置。枚举对角线：
    - 如果可行点个数为1，则将该点填充，任取另外一个空地即可。
    - 否则，必然要填充最左边或最右边的可行点（填充中间点无意义）。对于只填充一边可行点的情况，贪心向后搜索每个斜对角线，对于可以只填充一个位置的对角线记录到答案中。

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

const int N=3e3+10;
char g[N][N];
int f[2][N][N];
vector<int> vec[N<<1];
int main(){
    int n=read(), m=read();
    for(int i=1; i<=n; i++) scanf("%s",g[i]+1);
    for(int i=1; i<=n; i++){
        for(int j=1; j<=m; j++){
            f[0][i][j] = ((i==1&&j==1)||f[0][i-1][j]||f[0][i][j-1]) && g[i][j]=='.';
        }
    }
    for(int i=n; i>=1; --i){
        for(int j=m; j>=1; --j){
            f[1][i][j] = ((i==n&&j==m)||f[1][i+1][j]||f[1][i][j+1]) && g[i][j]=='.';
        }
    }

    int cnt = 0;
    for(int i=1; i<=n; i++){
        for(int j=1; j<=m; j++){
            if(g[i][j] == '.') cnt++;
            if(f[0][i][j] && f[1][i][j]){
                vec[i+j].pb(i);
            } else {
                g[i][j] = '*';
            }
        }
    }

    if(f[0][n][m] == 0){
        printf("%lld\n",1LL*cnt*(cnt-1)/2);
        return 0;
    }

    ll ans = 0;
    //1, 2
    for(int i=2; i<=n+m; i++){
        if(vec[i].size() == 1) ans += (--cnt);
        if(vec[i].size() == 2) ans ++;
    }
    //3
    for(int i=2; i<=n+m; i++){
        if(vec[i].size()==1) continue;

        int r=vec[i][0], l=vec[i][vec[i].size()-2];
        for(int j=i+1; j<=n+m; j++){
            if(g[l+1][j-(l+1)]=='.') l++;
            if(g[r][j-r]!='.') r++;
            if(l==r && vec[j].size()!=1) ans++;
        }

        r=vec[i][1], l=vec[i][vec[i].size()-1];
        for(int j=i+1; j<=n+m; j++){
            if(g[l+1][j-(l+1)]=='.') l++;
            if(g[r][j-r]!='.') r++;
            if(l==r && vec[j].size()!=1) ans++;
        }
    }
    printf("%lld\n",ans);
    return 0;
}
```