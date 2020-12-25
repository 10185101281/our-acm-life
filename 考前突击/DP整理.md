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

# 区间DP

---

##张老师的旅行（第十七届同济大学校赛）

### 题意

​	给出水平数轴上n个点，每个点的位置$p_i$，最晚到达时间$t_i$（时间为0表示起点，只有一个）。

​	输出所需的最少时间，若无解输出-1。

​	$n\leq 10^3, 1\leq p_i\leq 10^5, 0\leq t_i\leq 10^7$

### 思路

​	在最小时间前提下走过的点必然是连续的区间，设为[l, r]。需要讨论终点落在l还是r。

​	考虑DP，设l\[i][j]表示点落在 l 的前提下走完区间[i,j]最小时间，r\[i][j]同理。

​	以区间长度len为阶段进行状态转移。

​	答案即min(l\[1]\[n], r\[1]\[n])。

### 代码

```c++
#include<bits/stdc++.h>
#define ll long long
using namespace std;
const int N = 1e3+10;
const ll inf = 0x3f3f3f3f3f3f3f3f;
int p[N], t[N];
ll l[N][N], r[N][N];
int main(){
    int n; cin>>n;
    for(int i = 1; i<=n; i++) scanf("%d",p+i);
    for(int i = 1; i<=n; i++) scanf("%d",t+i);
    memset(l,0x3f,sizeof(l));
    memset(r,0x3f,sizeof(r));
    for(int i = 1; i<=n; i++) l[i][i] = r[i][i] = 0;
    for(int len = 2; len<=n; len++){
        for(int L = 1; L+len-1<=n; L++){
            int R = L+len-1;
            l[L][R] = min(l[L+1][R]+p[L+1]-p[L], r[L+1][R]+p[R]-p[L]);
            r[L][R] = min(l[L][R-1]+p[R]-p[L], r[L][R-1]+p[R]-p[R-1]);
            if(l[L][R] > t[L]) l[L][R] = inf;
            if(r[L][R] > t[R]) r[L][R] = inf;
        }
    }
    ll ans = min(l[1][n],r[1][n]);
    if(ans == inf) cout<<-1<<endl;
    else cout<<ans<<endl;
}
```

---

## Kaavi and Magic Spell（cf-635-div2 E）

### 题意

​	给一个长度为$n$的字符串$S$和一个长度为$m$的字符串$T$ 。

​	然后开始有一个空串$A$，接下来可对$S$串进行$n$次操作:

​		-把$S$的首个字符添加到$A$的开头然后删掉

​		-把$S$的首个字符添加到$A$的尾端然后删掉

​	问在操作过程中使得$A$的前缀为$T$的情况共有多少？
​	长度不同或者是操作序列中有某个地方不同视为不同情况。

​	$1\leq m\leq n\leq 3000$

### 思路

​	首先，假设$T[m+1,n]$可以是任意字符，这样就成了匹配问题。

​	设$f(l,r)$表示用$S[1,r-l+1]$，按照这种操作方式构造匹配$T[l,r]$的串，有多少种不同情况。

​	对于每个i，考虑它加在每个区间的前边或后边两种情况，DP即可。

​	由于只要前缀满足就视为一种情况，所以答案为$\sum_{i=m}^nf(1,i)$。

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int N = 3e3+10;
const int mod = 998244353;
int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
char s[N], t[N];
int f[N][N]{0};
int main(){
    scanf("%s%s",s+1,t+1);
    int n = strlen(s+1), m = strlen(t+1);
    for(int i = 1; i<=m; i++) f[i][i] = (s[1]==t[i])*2;
    for(int i = m+1; i<=n; i++) f[i][i] = 2;
    for(int i = 2; i<=n; i++){
        for(int l = 1; l+i-1<=n; l++){
            int r = l+i-1;
            if(l>m || s[i]==t[l]) f[l][r] = add(f[l][r], f[l+1][r]);
            if(r>m || s[i]==t[r]) f[l][r] = add(f[l][r], f[l][r-1]);
        }
    }
    int ans = 0;
    for(int i = m; i<=n; i++) ans = add(ans, f[1][i]);
    cout<<ans<<endl;
}
```

-----

## Fragrant numbers（2020hdu多校-6）

### 题意

​	S是一个“1145141919”循环产生的无穷串。

​	给出一个n，要求选出最短的S的前缀，使得该前缀中间添加任意括号、加号、乘号，计算结果等于n。

​	$1\le T\le 30, 1\le N\le 5000$

### 思路

​	设$f[l][r][val]$表示区间$[l,r]$是否能构造出$val$，可以考虑区间DP处理。

​	DP的复杂度跑满有$O(len^3N)$，$len$是进行DP的字符串长度。在本地对len打表测试，可以发现在$len==12$时就可以将所有的情况处理出来，这样复杂度可以优化到$O(12^3N)$。实际上，许多情况是不合法的，$O(12^3N)$不会跑满，可以尝试提交。即使跑满，也只有$4e9$左右，可以在本体打表后提交。

### 代码

``` c++
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
const int N = 20;
const int M = 5e3+10;
string s = " 114514191911451419191145141919";
int f[N][N][M], ans[M];
void init(int n,int m){
    memset(f, 0, sizeof(f));
    for(int i = 1; i<=n; i++){
        int t = 0;
        for(int j = i; j<=n; j++){
            t = t*10+s[j]-'0';
            if(t > m) break;
            f[i][j][t] = 1;
        }
    }
    for(int len = 2; len<=n; len++){
        for(int l = 1; l<=n; l++){
            int r = l+len-1;
            for(int k = l; k<r; k++){
                for(int val1 = 1; val1<=m; val1++){
                    if(!f[l][k][val1]) continue;
                    for(int val2 = 1; val2<=m; val2++){
                        if(!f[k+1][r][val2]) continue;
                        if(val1+val2<=m) f[l][r][val1+val2] = 1;
                        if(val1*val2<=m) f[l][r][val1*val2] = 1;
                    }
                }
            }
        }
    }
    for(int j = 1; j<=m; j++){
        ans[j] = -1;
        for(int i = 1; i<=n; i++){
            if(f[1][i][j]) {
                ans[j] = i;
                break;
            }
        }
    }
}
int main(){
    init(12, 5000);
    int T; cin>>T;
    while(T--){
        int n; scanf("%d",&n);
        printf("%d\n",ans[n]);
    }
}
```

----

# 状压DP

---

##Lineup the Dominoes（UCF 2016）

### 题意

​	给n个多米诺骨牌，正反面皆有数字。一个骨牌排列合法，当且仅当每个位置的骨牌的背面数字与下一位置骨牌正面数字相同。在可以调整骨牌翻面和位置的情况下，求合法骨牌排列的个数。两个合法骨牌排列只有存在两个位置骨牌本质不同才认为它们不同。

​	n<=16

### 思路

​	状压DP。$dp[i][j][k]$ 表示 n 个骨牌已用的状态为 i，最后一个骨牌是 j，且最后一个骨牌 不翻转(0) / 翻转(1)。

​	注意需要特判所有骨牌都相同的情况。只有这种情况对于DP是无法进行正确统计的。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int mod = 1e9+7;
const int maxn = 17;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int l[maxn], r[maxn];
int dp[1<<maxn][maxn][2];
int solve(int n){
    int N = (1<<n);
    for(int i = 0; i<N; i++){
        for(int j = 0; j<n; j++){
            dp[i][j][0] = dp[i][j][1] = 0;
        }
    }
    for(int j = 0; j<n; j++)  {
        if(l[j] != r[j]) dp[1<<j][j][0] = dp[1<<j][j][1] = 1;
        else dp[1<<j][j][0] = 1;
    }
    for(int i = 1; i<N; i++){
        for(int j = 0; j<n; j++) if(i>>j & 1){
            for(int k = 0; k<n; k++) if(((i^1<<j)>>k & 1)){
                    if(r[k] == l[j]) dp[i][j][0] = add(dp[i][j][0], dp[i^1<<j][k][0]);
                    if(l[k] == l[j]) dp[i][j][0] = add(dp[i][j][0], dp[i^1<<j][k][1]);
                    if(l[j] != r[j]) {
                        if (r[k] == r[j]) dp[i][j][1] = add(dp[i][j][1], dp[i ^ 1 << j][k][0]);
                        if (l[k] == r[j]) dp[i][j][1] = add(dp[i][j][1], dp[i ^ 1 << j][k][1]);
                    }
            }
        }
    }
    int ans = 0;
    for(int j = 0; j<n; j++){
        ans = add(ans, add(dp[N-1][j][0],dp[N-1][j][1]));
    }
    return ans;
}
bool check(int n){
    for(int i = 1; i<n; i++){
        if(l[i] == l[i-1] && r[i] == r[i-1]) continue;
        return false;
    }
    return true;
}
int jc[maxn];
int main(){
    jc[0] = 1;
    for(int i = 1; i<maxn; i++){
        jc[i] = mul(jc[i-1], i);
    }
    int T; cin>>T;
    while(T--){
        int n; scanf("%d",&n);
        for(int i = 0; i<n; i++) {
            scanf("%d%d",l+i,r+i);
            if(l[i] > r[i]) swap(l[i], r[i]);
        }
        if(check(n)) printf("%d\n",jc[n]);
        else printf("%d\n", solve(n));
    }
}
```

----

# 数据结构优化DP

----

##Breaking Down News（2020hdu多校-8）

### 题意

​	给一个长度为n的序列$a_1,a_2,...,a_n(a_i\in\{-1,0,-1\})$。

​	要求将序列划分成若干个长度在$[L,R]$范围的段。定义每一段的价值为$[(\sum_{i=l_k}^{r_k}a_i>0)]-[(\sum_{i=l_k}^{r_k}a_i<0)]$（即如果这一段和>0，值为1；=0，值为0；<0，值为-1）。

​	求划分后最大的段值和。

​	$1\le T\le 1000,1\le L\le R\le n\le10^6,\sum_n\le 9\times 10^6$

### 思路

​	考虑DP。对于每个位置i，可以向其转移的区间为$[i-R+1,i-L]$。对这个区间建立前缀和的权值线段树，对三类情况分别讨论，设sum为前缀和：

​	（1）<sum[i]：dp[i] = max(dp[i], val+1)

​	（2）=sum[i]：dp[i] = max(dp[i], val)

​	（3）>sum[i]：dp[i] = max(dp[i], val-1)

​	对于每个位置i，在权值线段树上删除i-R，增加i-L，即可实现对该区间权值线段树的维护。

​	注意，0也是一个可以向前转移的位置，在维护区间时要将0考虑在内（初始化dp[0]=0)。

​	实现时，由于数据较大，需要对常数进行优化，否则会卡常。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define pb push_back
#define pii pair<int, int>
#define pll pair<ll, ll>
#define pli pair<ll, int>
#define pss pair<string, string>
#define pdd pair<double, double>
#define fir first
#define sec second
const int N = 2e6+10;
const int inf = 1e9;
inline int read(){
    int x=0,f=1;
    char ch=getchar();
    while(ch<'0'||ch>'9'){
        if(ch=='-')
            f=-1;
        ch=getchar();
    }
    while(ch>='0'&&ch<='9'){
        x=(x<<1)+(x<<3)+(ch^48);
        ch=getchar();
    }
    return x*f;
}
inline int max(int a,int b){return a>b?a:b;}
struct node{
    int l, r, mx;
}T[N*4];
inline void build(int p,int l,int r){
    T[p].l = l; T[p].r = r;
    T[p].mx = -inf;
    if(l==r) return;
    int mid = (l+r)/2;
    build(p*2,l,mid);
    build(p*2+1,mid+1,r);
}
inline void update(int p,int pos,int x){
    if(T[p].l==T[p].r){
        T[p].mx = x;
        return ;
    }
    int mid = (T[p].l+T[p].r)/2;
    if(pos<=mid) update(p*2,pos,x);
    else update(p*2+1,pos,x);
    T[p].mx = max(T[p*2].mx,T[p*2+1].mx);
}
inline int query(int p,int L,int R){
    if(L<=T[p].l && R>=T[p].r) {
        return T[p].mx;
    }
    int mid = (T[p].l+T[p].r)/2;
    int ans = -inf;
    if(L<=mid) ans = max(ans, query(p*2,L,R));
    if(R >mid) ans = max(ans, query(p*2+1,L,R));
    return ans;
}
int a[N], sum[N], id[N], c[N]; pii b[N];
int dp[N];
int main(){
    int T; T=read();
    while(T--) {
        int n=read(), L=read(), R=read();
        int m = 0;
        b[++m] = pii{0, 0};
        for(int i=1; i<=n; i++){
            a[i]=read();
            sum[i] = sum[i-1]+a[i];
            b[++m] = pii{sum[i], i};
        }
        sort(b+1,b+1+m);
        for(int i=1; i<=m; i++){
            id[b[i].sec] = i;
            c[i] = b[i].fir;
        }
        build(1, 1, m);
        dp[0] = 0;
        for(int i=1; i<=n; i++){
            dp[i] = -inf;
            if(i-L>=0) update(1,id[i-L],dp[i-L]);
            if(i-R>0) update(1,id[i-R-1],-inf);
            int mid = lower_bound(c+1,c+m+1,sum[i])-c;
            int r = upper_bound(c+1,c+m+1,sum[i])-c;
            int l = mid-1;
            if(l>=1) dp[i] = max(dp[i], query(1,1,l)+1);
            if(mid<r) dp[i] = max(dp[i], query(1,mid,r-1));
            if(r<=m) dp[i] = max(dp[i], query(1,r,m)-1);
        }
        printf("%d\n",dp[n]);
    }
}
```

-----

# LCS

----

## String Distance（2020hdu多校-2）

### 题意

​	给一个长度为$n$的串$S$和一个长度为$m$的串$T$，S,T只包含小写字母。定义两个字符串的距离为最少的令两字符串相同的操作次数（一步操作可以在任意位置增加或减少某个字符）。

​	$q$次询问，每次给出$l,r$，问S[l,r]和T的距离。

​	$1\le T\le 10, 1\le n\le 10^5, 1\le m\le 20, 1\le q\le 10^5$

### 思路

​	首先，对于两个字符串A,B，$dis(A, B) = |A|+|B| - 2\times |LCS(A, B)|$。

​	由于m很小，每次询问可以用$O(m^2)$的$LCS$算法。

​	具体做法为：

​	（1）对于A的每一个位置，预处理下一个字母最近的位置。

​	（2）记$dp[i][j]$为，对于B的位置i，LCS长度为j时，A最小花费（最少需要多长来匹配）。对于每个状态，仅可能由$dp[i-1][j],dp[i][j-1]$两个状态转移过来，因此可以做到$O(1)$转移，整体复杂度为$O(m^2)$。

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
#define pdd pair<double, double>
const int N = 1e5+10, M = 25;
char a[N], b[M];
int n, m, Next[N][26];
int f[M][M];
int cal(int l,int r){
    memset(f, -1, sizeof(f));
    f[0][0] = l-1;
    for(int i = 1; i<=m; i++){
        for(int j = 0; j<i; j++){
            if(f[i-1][j] == -1) break;
            f[i][j] = (f[i][j] == -1)? f[i-1][j]: min(f[i][j], f[i-1][j]);
            int t = Next[f[i-1][j]][b[i]-'a'];
            if(t !=-1 && t <= r){
                f[i][j+1] = (f[i][j+1] == -1)? t: min(f[i][j+1], t);
            }
        }
    }
    for(int j = 1; j<=m; j++) {
        if(f[m][j] == -1) return j-1;
    }
    return m;
}
int main(){
    int T; cin>>T;
    while(T--){
        scanf("%s%s",a+1,b+1);
        n = strlen(a+1), m = strlen(b+1);
        for(int j = 0; j<26;j ++) Next[n][j] = -1;
        for(int i = n-1; i>=0; --i){
            for(int j = 0; j<26; j++) Next[i][j] = Next[i+1][j];
            Next[i][a[i+1]-'a'] = i+1;
        }
        int q; scanf("%d",&q);
        while(q--){
            int l, r; scanf("%d%d",&l,&r);
            int l1 = r-l+1, l2 = m, lcs = cal(l,r);
            printf("%d\n",l1+l2-2*lcs);
        }
    }
}
```

----

# 背包DP

----

## Contest of Rope Pulling（2020hdu多校-4）

### 题意	

​	有两个班级，分别有n和m个学生，每个学生有两个值wi，vi，表示力量和美丽度，问从两个班级各选出一些人使两个班的总力量相等，且所有学生的总美丽度最大。

​	$1≤n,m≤1000$，$1≤wi≤1000$，$−10^9≤vi≤10^9$

### 思路

​	将第二个班级的学生的wi改为−wi，对两班力量差进行背包DP。因为最优解的力量差是在0附近震荡的，可以对力量差设一个上下阈值，将这n+m个学生随机重排一下之后这个阈值的期望值就会很小。经过证明，可以取$O(\sqrt {n+m}*w_i)$。

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
const int E = 5e4;
pii a[N];
ll f[2][N];
int main(){
    std::mt19937 generator(time(0));
    int T; cin>>T;
    while(T--){
        int n, m; scanf("%d%d",&n,&m);
        for(int i = 1; i<=n; i++){
            scanf("%d%d",&a[i].fir,&a[i].sec);
        }
        for(int i = 1; i<=m; i++){
            scanf("%d%d",&a[i+n].fir,&a[i+n].sec);
            a[i+n].fir = -a[i+n].fir;
        }
        n = n + m;
        shuffle(a+1,a+1+n,generator);
        memset(f, 0xcf, sizeof(f));
        f[0][E] = 0;
        int o = 1;
        for(int i = 1; i<=n; i++){
            for(int j = 0; j<=2*E; j++){
                f[o][j] = max(f[o][j], f[o^1][j]);
                if(j+a[i].fir>=0 && j+a[i].fir<=2*E){
                    f[o][j+a[i].fir] = max(f[o][j+a[i].fir], f[o^1][j]+a[i].sec);
                }
            }
            o ^= 1;
        }
        printf("%lld\n",f[o^1][E]);
    }
}
```

----

## Altruistic Amphibians（NCPC 2018 A）

### 题意

​	$n$个青蛙，每个青蛙各自有属性：跳跃能力$l_i$，体重$w_i$，身高$h_i$。青蛙可以叠罗汉，限制对于每个青蛙，叠在其上面的青蛙重量总和小于其自身。有一个长为$d$的沟壑，一个青蛙可以跳过沟壑，当且仅当其跳跃能力与其下所叠青蛙高度和大于沟壑长度。

​	问最多有多少青蛙可以跳过沟壑。

​	$1\le n\le 10^5,1\le d\le 10^8$

​	$1\le l_i,w_i,h_i\le 10^8$

​	$\sum w_i\le 10^8$

### 思路

​	体重小的青蛙跳走对体重大的青蛙不会产生影响，因此每个青蛙可以贪心计算是否可以跳过。将青蛙体重从大到小排序，$f[w]$表示能够撑起重量$w$的最大高度。考虑如何维护。

​	容易得到转移方程：

​	$$\begin{cases}f[w] = max(f[w],f[w+w_i]+h_i)&w<w_i \\ f[w_i-1] = max(f[w]+h_i) & w>w_i\end{cases}$$

​	由于数组是单调递减的，因此第二部分转移不用考虑。

### 代码

````c++
#include <bits/stdc++.h>
#define pii pair<int, int>
#define ll long long
#define pb push_back
#define fir first
#define sec second
using namespace std;
const int N = 1e5+10;
const int M = 1e8+10;
struct node{
    int l, w, h;
} a[N];
bool cmp(node x, node y){
    return x.w > y.w;
}
int f[M]{0};
int main(){
    int n, d; cin>>n>>d;
    for(int i = 1; i<=n; i++){
        scanf("%d%d%d",&a[i].l, &a[i].w, &a[i].h);
    }
    sort(a+1, a+1+n, cmp);
    int ans = 0;
    for(int i = 1; i<=n; i++){
        if(f[a[i].w] + a[i].l > d) ans++;
        for(int j = 1; j<min(a[i].w, M-a[i].w); j++){
            f[j] = max(f[j], f[j+a[i].w] + a[i].h);
        }
    }
    cout<<ans<<endl;
    return 0;
}
````

----

# 数位DP

---

## Harmony Pairs（2020牛客多校）

### 题意

​	求$1\le A\le B\le N$，满足$S(A) > S(B)$的$(A, B)$个数，$S(x)$是$x$数码和。

​	$1\le N\le 10^{100}$

### 思路

​	数位DP，枚举每一位，记忆化记录两数数码和之差和两个上界标记符号。

​	因为数码和之差可能为负数，所以要加上一个常数。

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int N = 1e2+10;
const int M = 2e3+10;
const int E = 1000;
const int mod = 1e9+7;
int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
int f[N][M][2][2], a[N];
int dfs(int k,int d,int p,int q){
    if(k == 0) return d > E;
    if(f[k][d][p][q] != -1) return f[k][d][p][q];
    int sum = 0;
    for(int i = 0; i<=(p?a[k]: 9); i++){
        for(int j = 0; j<=(q?i: 9); j++){
            sum = add(sum, dfs(k-1,d+j-i,p&&(i==a[k]),q&&(j==i)));
        }
    }
    return f[k][d][p][q] = sum;
}
char s[N];
int main(){
    scanf("%s",s+1);
    int n = strlen(s+1);
    for(int i = 1; i<=n; i++) a[i] = s[n-i+1]-'0';
    memset(f,-1,sizeof(f));
    printf("%d\n",dfs(n,E,1,1));
}
```

----

## X Number（2020hdu多校-3）

### 题意

​	给出L,R,d，求[L,R]中有多少个数，满足十进制位上d最多（严格最多）。

​	$1≤T≤1000,1≤l≤r≤10^{18}.0≤d≤9$

### 思路

​	考虑数位DP。一般数位 DP 的复杂度为 O(进制数 * 位数 + 进制数 * 新增状态数)，如果直接使用这样的数位DP，因为要记录每个数出现的次数，所以会跑满所有的情况（相当于暴力枚举）。

​	实际上，当没有限制时，可以用组合数学计算出结果。具体可以考虑进行一个三段循环的DP。用cnt[i]记录枚举到当前位每个数字𝑖i出现了多少次，pos表示还有多少个剩余位置是空的，枚举数码d出现的次数num，dp\[i][j]表示考虑0∼9中前i位且除了指定的那一位以外已经在剩余的位置安放了j个数。有转移方程：$dp[i][j] = \sum_{k=0}^{min(j,num-cnt[i]-1)}dp[i-1][j-k]\times C^k_{pos-j+k}$。

​	这样，第二部分的复杂度就可以优化掉了，复杂度变为O(进制数 * 位数 * 计算时DP的复杂度)。

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
const int N = 22;
int d, a[N], cnt[N];
ll C[N][N]{0};
ll dp[N][N];
ll dfs(int pos,bool limit,bool lead){
    if(pos == 0){
        for(int i = 0; i<=9; i++){
            if(i == d) continue;
            if(cnt[d] <= cnt[i]) return 0;
        }
        return 1;
    } else if(!limit && !lead){
        ll ans = 0;
        int mx = cnt[d];
        for(int i = 0; i<10; i++) if(i != d) mx = max(mx, cnt[i]+1);
        for(int num = mx; num<=cnt[d]+pos; num++){
            int res = cnt[d]+pos-num;
            memset(dp, 0, sizeof(dp));
            dp[0][0] = 1;
            for(int i = 1; i<=10; i++){
                if(i-1 == d){
                    for(int j = 0; j<=res; j++) dp[i][j] = dp[i-1][j];
                } else {
                    for(int j = 0; j<=res; j++){
                        for(int k = 0; k+cnt[i-1]+1<=num && k<=j; k++){
                            dp[i][j] += dp[i-1][j-k] * C[pos-(j-k)][k];
                        }
                    }
                }
            }
            ans += dp[10][res];
        }
        return ans;
    } else {
        ll ans = 0;
        for(int i = 0; i <= (limit?a[pos]: 9); i++){
            if(!lead || i) cnt[i]++;
            ans += dfs(pos-1, limit && i == a[pos], lead && i == 0);
            if(!lead || i) cnt[i]--;
        }
        return ans;
    }
}
ll cal(ll x){
    int len = 0;
    while(x){
        a[++len] = x%10;
        x/=10;
    }
    for(int i = 0; i<10; i++) cnt[i] = 0;
    return dfs(len, true, true);
}
void init(){
    for(int i = 0; i<N; i++) C[i][0] = 1;
    for(int i = 1; i<N; i++){
        for(int j = 1; j<=i; j++){
            C[i][j] = C[i-1][j-1] + C[i-1][j];
        }
    }
}
int main(){
    init();
    int T; cin>>T;
    while(T--){
        ll l, r; scanf("%lld%lld%d",&l,&r,&d);
        printf("%lld\n",cal(r)-cal(l-1));
    }
}
```