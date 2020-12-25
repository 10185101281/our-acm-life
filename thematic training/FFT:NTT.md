# FFT/NTT

----

NTT素数表及其对应原根

| MOD        | G    |
| ---------- | ---- |
| 40961      | 3    |
| 65537      | 3    |
| 786433     | 10   |
| 5767169    | 3    |
| 7340033    | 3    |
| 23068673   | 3    |
| 104857601  | 3    |
| 167772161  | 3    |
| 469762049  | 3    |
| 998244353  | 3    |
| 1004535809 | 3    |

| MOD         | G    |
| ----------- | ---- |
| 2013265921  | 31   |
| 2281701377  | 3    |
| 3221225473  | 5    |
| 75161927681 | 3    |

----

## 【模版】分治FFT

### 描述

​	给定序列$g_{1...n-1}$，求序列$f_{0...n-1}$。

​	其中$f_i=\sum_{j=1}^if_{i-j}g_j,f_0=1$。

​	答案对$998244353$取模。

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;

const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int qpow(int a,int b){
    int ret = 1;
    for(; b; b>>=1){
        if(b & 1) ret=mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}

const int N = 2e5+10;
const int G = 3;
int wn[N<<4], rev[N<<4];
// mod 998244353,1004535809,469762049
int NTT_init(int pn){
    int step=0; int n = 1;
    for(; n<pn; n<<=1) ++step;
    for(int i=1; i<n; i++) rev[i] = (rev[i>>1]>>1) | ((i&1)<<(step-1));
    int g = qpow(G,(mod-1)/n);
    wn[0] = 1;
    for(int i=1; i<=n; i++) wn[i] = mul(wn[i-1], g);
    return n;
}
void NTT(int a[],int n,int f){
    for(int i=0; i<n; i++){
        if(i<rev[i]) swap(a[i], a[rev[i]]);
    }
    for(int k=1; k<n; k<<=1){
        for(int i=0; i<n; i+=(k<<1)){
            int t = n/(k<<1);
            for(int j=0; j<k; j++){
                int w = (f==1)? wn[t*j]: wn[n-t*j];
                int x = a[i+j];
                int y = mul(a[i+j+k], w);
                a[i+j] = add(x, y);
                a[i+j+k] = sub(x, y);
            }
        }
    }
    if(f == -1){
        int ninv = qpow(n, mod-2);
        for(int i=0; i<n; i++) a[i] = mul(a[i], ninv);
    }
}
int f[N], g[N];
int a[N<<4], b[N<<4];
int n;
void solve(int l,int r,int len){
    if(l >= n) return ;
    if(len == 1) return ;
    int tlen = len>>1;
    int mid = (l+r)>>1;
    solve(l,mid,tlen);
    NTT_init(len);
    for(int i=0; i<len; i++) a[i] = b[i] = 0;
    for(int i=0; i<tlen; i++) a[i] = f[i+l];
    for(int i=0; i<len; i++) b[i] = g[i];
    NTT(a,len,1); NTT(b,len,1);
    for(int i=0; i<len; i++) a[i] = mul(a[i],b[i]);
    NTT(a,len,-1);
    for(int i=tlen; i<len; i++) f[i+l] = add(f[i+l], a[i]);
    solve(mid,r,tlen);
}
int main(){
    cin>>n;
    int tn = 1; while(tn<n) tn<<=1;
    for(int i=0; i<tn; i++) g[i] = 0;
    for(int i=1; i<n; i++) scanf("%d",g+i);
    for(int i=0; i<tn; i++) f[i] = 0; f[0] = 1;
    solve(0, tn, tn);
    for(int i=0; i<n; i++) printf("%d%c",f[i]," \n"[i==n]);
    return 0;
}
```

## Residual Polynomial

### 题意

​	给定多项式$f_1(x)=\sum_{i=0} ^n a_ix^i$与序列$\{b_n\},\{c_n\}$。

​	已知递推式$f_n=b_nf'_{n-1}+c_nf_{n-1}(n>1)$，求$f_n$。

​	$3\le n\le 10^5, 0\le a_i,b_i,c_i<998244353, mod\ 998244353$

### 思路

$$
f_{1,0}\qquad f_{2,0}\qquad f_{3,0}\qquad \cdots \qquad f_{n,0}\\
f_{1,1}\qquad f_{2,1}\qquad f_{3,1}\qquad \cdots \qquad f_{n,1}\\
\vdots \ \ \ \ \ \qquad\vdots\ \ \ \ \  \qquad \vdots \qquad \ddots \qquad\ \  \vdots \\
f_{1,n}\qquad f_{2,n}\qquad f_{3,n}\qquad \cdots \qquad f_{n,n}
$$

​	对于$f_{i,j}$存在两种转移：
$$
	\begin{cases}
	f_{i,j}\overset{c_{i+1}}{\rightarrow} f_{i+1,j}&i<n\\
	
	f_{i,j}\overset{j·b_{i+1}}\rightarrow f_{i+1,j}&i<n,j>0
	\end{cases}
$$
​	那么$f_{1,i}$对$f_{n,j}$的贡献为$f_{1,i}·\sum(\Pi pathvalue)$，即$f_{1,i}$到$f_{n,j}$所有可行路径边权乘积和。

​	先不考虑第二类转移中$𝑗⋅𝑏𝑖+1$的$j$，对于任何转移$f_{1,i}\rightarrow f_{n,j}$，实际是对于每个$2\le k\le n$，选择$b_k$或$c_k$，其中$b_k$选择$i−j$个，$c_k$选择$n−1−(i−j)$个，乘起来然后再把所有方案加起来。

​	令选择$x$个$b_x$和$n-1-x$个$c_k$所有方案和为$F(x)$，$F(x)$可以由分治+FFT得到：

​		令$F(l,r,x)$表示在区间$[l,r)$中选择$x$个$b_k$的所有方案的和，那么有$F(l,r,x)=\sum_{i+j=x}F(l,mid,i)·F(mid,r,j)$，其中$mid =\lfloor \frac{l+r}{2}\rfloor$。

​	再算上之前没考虑的$j$的贡献乘积，可以得到$f_{n,j}=\sum_{i-j=k}F(k)·f_{1,i}·\frac{i!}{j!}$，

​	将数组反转，即$f_{i,j}$与$f_{i,n-j}$互换，那么有$f_{n,j}=\sum_{i+j=k}F(k)·f_{1,i}·\frac{(n-i)!}{(n-j)!}$

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;
const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int qpow(int a,int b){
    int ret = 1;
    for(; b; b>>=1){
        if(b & 1) ret=mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}
const int N = 2e5+10;
int fac[N], inv[N], ifac[N];
inline void init(int n){
    fac[0] = ifac[0] = inv[1] = 1;
    for(int i=1; i<=n; i++) fac[i] = mul(fac[i-1], i);
    for(int i=2; i<=n; i++) inv[i] = mul(sub(mod,mod/i),inv[mod%i]);
    for(int i=1; i<=n; i++) ifac[i] = mul(ifac[i-1], inv[i]);
}

const int G = 3;
int wn[N<<4], rev[N<<4];
// mod 998244353,1004535809,469762049
int NTT_init(int pn){
    int step=0; int n = 1;
    for(; n<pn; n<<=1) ++step;
    for(int i=1; i<n; i++) rev[i] = (rev[i>>1]>>1) | ((i&1)<<(step-1));
    int g = qpow(G,(mod-1)/n);
    wn[0] = 1;
    for(int i=1; i<=n; i++) wn[i] = mul(wn[i-1], g);
    return n;
}
void NTT(int a[],int n,int f){
    for(int i=0; i<n; i++){
        if(i<rev[i]) swap(a[i], a[rev[i]]);
    }
    for(int k=1; k<n; k<<=1){
        for(int i=0; i<n; i+=(k<<1)){
            int t = n/(k<<1);
            for(int j=0; j<k; j++){
                int w = (f==1)? wn[t*j]: wn[n-t*j];
                int x = a[i+j];
                int y = mul(a[i+j+k], w);
                a[i+j] = add(x, y);
                a[i+j+k] = sub(x, y);
            }
        }
    }
    if(f == -1){
        int ninv = qpow(n, mod-2);
        for(int i=0; i<n; i++) a[i] = mul(a[i], ninv);
    }
}

int F[N<<2], a[N<<2], b[N<<2], c[N<<2], ans[N<<2];
void cal_F(int F[],int l,int r){
    if(l+1 == r){
        F[0] = c[l];
        F[1] = b[l];
        return ;
    }
    int mid = (l+r)>>1;
    int len = r-l+1;
    int lf[len<<2], rf[len<<2];
    memset(lf, 0, sizeof(lf));
    memset(rf, 0, sizeof(rf));
    cal_F(lf,l,mid);
    cal_F(rf,mid,r);
    int tn = 1; while(tn < len) tn<<=1;
    NTT_init(tn);
    NTT(lf,tn,1); NTT(rf,tn,1);
    for(int i=0; i<tn; i++) F[i] = mul(lf[i],rf[i]);
    NTT(F,tn,-1);
    for(int i=len; i<tn; i++) F[i] = 0;
}

int main(){
    init(N-1);
    int T; cin>>T;
    while(T--){
        int n; scanf("%d",&n);
        for(int i=0; i<n+1; i++) scanf("%d",a+i);
        for(int i=0; i<n-1; i++) scanf("%d",b+i);
        for(int i=0; i<n-1; i++) scanf("%d",c+i);
        int len = 1; while(len<n) len<<=1;
        cal_F(F,0,n-1);

        while(len<=n+1) len<<=1; len<<=1;
        reverse(a,a+n+1);
        for(int i=0; i<n+1; i++) a[i] = mul(a[i],fac[n-i]);
        for(int i=n; i<len; i++) F[i] = 0;
        for(int i=n+1; i<len; i++) a[i] = 0;
        NTT_init(len);
        NTT(a,len,1); NTT(F,len,1);
        for(int i=0; i<len; i++) ans[i] = mul(a[i], F[i]);
        NTT(ans,len,-1);
        for(int i=0; i<n+1; i++) ans[i] = mul(ans[i],ifac[n-i]);
        reverse(ans,ans+n+1);
        for(int i=0; i<n+1; i++) printf("%d%c",ans[i]," \n"[i==n]);
    }
    return 0;
}
```

-----

## Expected area

### 描述

​	给出两个区间集合$S=\{(l_1,r_1),...,(l_n,r_n)\},T=\{(s_1,t_1),...,(s_n,t_n)\}$。

​	计算$\bigcup_{i=1}^n([l_i,r_i]\times [s_{p(i)},t_{p(i)}])$的期望面积。$p$是等概率随机排列。

​	$2\le n\le 2\times 10^5,0\le l_i<r_i< 998244353,0\le s_i<t_i<998244353$

​	结果模$998244353$

### 思路

​	对一块区域，考虑计算它不被覆盖的概率$p$，那么它对答案的贡献就是$(1-p)\times S$。

​	假设该区域x轴覆盖$i$次，$y$轴覆盖$j$次，那么该区域不被覆盖，当且仅当这个i个x区间与j个y区间不配对，那么$p=\frac{C_{n-i}^jA_j^jA_{n-j}^{n-j}}{A_n^n}=\frac{A_{n-i}^{n-i}A_j^jA_{n-j}^{n-j}}{A_n^nA_{j}^{j}A_{n-i-j}^{n-i-jj}}=\frac{A_{n-i}^{n-i}A_{n-j}^{n-j}}{A_n^nA_{n-i-j}^{n-i-j}}$。

​	最终答案为所有区域的贡献和，即$\sum S-pS=\sum len_i\times len_j-\sum\frac{A_{n-i}^{n-i}A_{n-j}^{n-j}}{A_n^nA_{n-i-j}^{n-i-j}}\times len_ilen_j$。

​	用$NTT$计算即可。

### 代码

```c++
#include <bits/stdc++.h>
using namespace std;
#define fir first
#define sec second
#define pb push_back
const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}
int qpow(int a,int b){
    int ret = 1;
    for(; b; b>>=1){
        if(b & 1) ret=mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}
const int N = 2e5+10;
int fac[N], inv[N], ifac[N];
inline void init(int n){
    fac[0] = ifac[0] = inv[1] = 1;
    for(int i=1; i<=n; i++) fac[i] = mul(fac[i-1], i);
    for(int i=2; i<=n; i++) inv[i] = mul(sub(mod,mod/i),inv[mod%i]);
    for(int i=1; i<=n; i++) ifac[i] = mul(ifac[i-1], inv[i]);
}

const int G = 3;
int wn[N<<4], rev[N<<4];
// mod 998244353,1004535809,469762049
int NTT_init(int pn){
    int step=0; int n = 1;
    for(; n<pn; n<<=1) ++step;
    for(int i=1; i<n; i++) rev[i] = (rev[i>>1]>>1) | ((i&1)<<(step-1));
    int g = qpow(G,(mod-1)/n);
    wn[0] = 1;
    for(int i=1; i<=n; i++) wn[i] = mul(wn[i-1], g);
    return n;
}
void NTT(int a[],int n,int f){
    for(int i=0; i<n; i++){
        if(i<rev[i]) swap(a[i], a[rev[i]]);
    }
    for(int k=1; k<n; k<<=1){
        for(int i=0; i<n; i+=(k<<1)){
            int t = n/(k<<1);
            for(int j=0; j<k; j++){
                int w = (f==1)? wn[t*j]: wn[n-t*j];
                int x = a[i+j];
                int y = mul(a[i+j+k], w);
                a[i+j] = add(x, y);
                a[i+j+k] = sub(x, y);
            }
        }
    }
    if(f == -1){
        int ninv = qpow(n, mod-2);
        for(int i=0; i<n; i++) a[i] = mul(a[i], ninv);
    }
}
int n;
map<int, int> mp;
void cal_cover(int a[],int l[],int r[]){
    mp.clear();
    for(int i=1; i<=n; i++) {
        mp[l[i]]++; mp[r[i]]--;
    }
    int pre = -1, num = 0;
    for(auto &pr: mp){
        if(num != 0){
            int len = (pr.fir-1) - pre + 1;
            a[num] += len;
        }
        num += pr.sec;
        pre = pr.fir;
    }
}
int a[N<<4], b[N<<4], c[N<<4], d[N<<4];
int l[N], r[N], s[N], t[N];
int main(){
    scanf("%d",&n);
    for(int i=1; i<=n; i++) scanf("%d%d",l+i,r+i);
    for(int i=1; i<=n; i++) scanf("%d%d",s+i,t+i);
    memset(a,0,sizeof(a));
    memset(b,0,sizeof(b));
    cal_cover(a,l,r);
    cal_cover(b,s,t);
    init(n);
    memset(c,0,sizeof(c));
    memset(d,0,sizeof(d));
    int ans = 0;
    int tn = 1; while(tn<n+1) tn<<=1; tn<<=1;
    NTT_init(tn);
    NTT(a,tn,1); NTT(b,tn,1);
    for(int i=0; i<tn; i++) c[i] = mul(a[i],b[i]);
    NTT(a,tn,-1);NTT(b,tn,-1);
    NTT(c,tn,-1);
    for(int i=0; i<tn; i++) ans = add(ans, c[i]);
    for(int i=0; i<=n; i++) {
        a[i] = mul(a[i], fac[n-i]);
        b[i] = mul(b[i], fac[n-i]);
    }
    NTT(a,tn,1); NTT(b,tn,1);
    for(int i=0; i<tn; i++) d[i] = mul(a[i],b[i]);
    NTT(d,tn,-1);
    for(int i=0; i<=n; i++) {
        ans =  sub(ans, mul(mul(ifac[n-i],ifac[n]),d[i]));
    }
    printf("%d\n",ans);
    return 0;
}
```