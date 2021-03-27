# DP

---

## 兔子的排列

### 题目

https://ac.nowcoder.com/acm/problem/21348

### 题解

​	枚举交换的位置，对两部分分治DP。注意到只需要记录左右两个端点的情况，状态数量为$O(N^4)$。

​	复杂度$O(N^5)$

### 代码

```c++
#include <bits/stdc++.h>

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
int qpow(int a,int b){
    int ret=1;
    for(; b; b>>=1){
        if(b&1) ret=mul(ret, a);
        a = mul(a, a);
    }
    return ret;
}

const int N = 55;
int fac[N], ifac[N];
void init(int n){
    fac[0]=1; for(int i=1; i<=n; i++) fac[i]=mul(fac[i-1], i);
    ifac[n]=qpow(fac[n],mod-2); for(int i=n-1; i>=0; --i) ifac[i]=mul(ifac[i+1], i+1);
}
inline int C(int n,int m){
    return (n<m||m<0)? 0: mul(fac[n], mul(ifac[m], ifac[n-m]));
}

int f[N][N][N][N];
int q[N], a[N];
int dfs(int l,int r,int x,int y){
    if(f[l][r][x][y] != -1) return f[l][r][x][y];
    if(l==r){
        return f[l][r][x][y] = (x==y && q[l]==x);
    }
    if(l+1 == r){
        return f[l][r][x][y] = mul(dfs(l,l,y,y),dfs(r,r,x,x));
    }
    int ret=0;
    for(int i=l; i<r; i++){
        int x1 = (i==l)? i+1: x;
        int y1 = (i==r-1)? y: i+1;
        int x2 = (i==l)? x: i;
        int y2 = (i==r-1)? i: y;
        ret = add(ret, mul(mul(dfs(l,i,x1,y1),dfs(i+1,r,x2,y2)), C(r-l-1,i-l)));
    }
    return f[l][r][x][y] = ret;
}
int main(){
    int n=read();
    init(N-1);
    for(int i=1; i<=n; i++) q[i]=read()+1;
    memset(f,-1,sizeof(f));
    printf("%d\n",dfs(1,n,1,n));
    return 0;
}
```