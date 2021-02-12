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

## 3 New Year and Original Order（B）

### 描述

​	定义$S(n)$为对$n$每位排序后的到的数，例如$S(50394) = 3459$。

​	给出一个数$X$，计算$\sum_{1\le k\le X}S(k)$

​	$1\le X\le 10^{700}$

https://www.cnblogs.com/heyujun/p/10225419.html

---

## 4 Game on Chessboard（B）

### 描述

​	给一个$n\times n$的棋盘。

​	棋盘上有棋子。每个棋子有一个颜色（白、黑）和一个值$w$。

​	需要将这些棋子移走。一个棋子能被移走当且仅当其左下区域没有棋子。移走棋子有两种操作：

​		（1）每次移走一个黑棋$u$一个白棋$v$，代价为$｜w_u-w_v｜$。

​		（2）直接移走一个棋子$u$，代价为$w_u$。

​	计算移走棋子的最小代价。

​	$1\le n\le 12$。

### 题解-bl

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

## 5 Fountains

https://www.cnblogs.com/reverymoon/p/14153891.html

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

## 7 Lucky Numbers

### 题解-bl

​	如果不限制数的和。那么可以将数看作物品，数的数量是物品的个数，数的大小是物品的重量，数在表中是物品的价值。那么就是一个多重背包问题。

​	现在限制了恰好画k个数，且数的和为n。显然，对于每个数位，贪心地考虑最多只有一个数在这个位置不为3的倍数。那么对于k-1个数，我们按照上面的方法进行多重背包，最后的一个数预处理其价值即可。	

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

## 8 String Transformation

https://codeforces.com/blog/entry/80562

---

## 9 Financiers Game

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

https://www.cnblogs.com/lhm-/p/13697304.html

---

## 11 Cell Blocking

https://www.cnblogs.com/Ike-shadow/p/14093202.html