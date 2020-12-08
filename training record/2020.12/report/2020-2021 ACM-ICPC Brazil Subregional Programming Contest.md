# [2020-2021 ACM-ICPC Brazil Subregional Programming Contest](http://codeforces.com/gym/102861)

-----

# K

### 思路

​	最多划分为两个部分。不妨设一个部分编号为0，另一部分编号为1。问题转化为是否存在标号方案，使得每个点满足与其相连的标号相同的点的数量是奇数。

​	讨论每个点的度数奇偶性情况，设这个点标号为$x_i$，这个点相连点标号为$x_a,x_b,x_c,...$：

​	（1）度数为偶数。划分一定满足将其相邻点切割为两个奇数部分。有$x_a\oplus x_b\oplus x_c...=1$。

​	（2）度数为奇数。划分一定满足将其相邻点切割为一个奇数一个偶数两个部分，且奇数部分一定与$x_i$标号相同。偶数部分异或为0，奇数部分异或等于$x_i$，即$x_i\oplus x_a\oplus x_b\oplus x_c...=0$。

​	根据每个点可以获得一个异或方程式，从而得到一个包含n个方程的异或方程组。问题转化为该异或方程组是否有解。用高斯消元计算即可。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define pii pair<int,int>
#define pb push_back
#define fir first
#define sec second
#define ll long long

const int maxn = 1e2+10;
int a[maxn][maxn], x[maxn];
int equ, var; // equ个方程，var个变元
bool free_x[maxn];
void init(int n,int m){
    memset(a, 0, sizeof(a));
    memset(x, 0, sizeof(x));
    memset(free_x,false, sizeof(free_x));
    equ = n, var = m;
}
int xor_gauss(){
    int r=1, c=1;
    while(r<=equ && c<=var){
        int m_r = r;
        for(int i=r+1; i<=equ; i++){
            if(a[i][c]>a[m_r][c]) m_r=i;
        }
        if(a[m_r][c] == 0){
            ++c;
            free_x[c] = true;
            continue;
        }

        if(m_r!=r){
            for(int j=c; j<=var+1; j++) swap(a[r][j],a[m_r][j]);
        }
        for(int i=r+1; i<=equ; i++){
            if(a[i][c]){
                for(int j=c; j<=var+1; j++){
                    a[i][j] ^= a[r][j];
                }
            }
        }
        ++r; ++c;
    }

    for(int i=r; i<=equ; i++) if(a[i][var+1]) return -1;
    if(r <= var) return var-r+1;
    for(int i=var; i>=1; --i){
        x[i] = a[i][var+1];
        for(int j=i+1; j<=var; j++){
            x[i] ^= (a[i][j]&&x[j]);
        }
    }
    return 0;
}

const int N = 1e2+10;
vector<int> es[N];
int deg[N];
int main(){
    int n, m; scanf("%d%d",&n,&m);
    memset(deg, 0, sizeof(deg));
    for(int i=1; i<=m; i++){
        int x,y; scanf("%d%d",&x,&y);
        es[x].pb(y); es[y].pb(x);
        deg[x]++; deg[y]++;
    }
    init(n,n);
    for(int i=1; i<=n; i++){
        for(auto &j: es[i]) a[i][j] = 1;
        if(deg[i]&1){
            a[i][i] = 1;
            a[i][n+1] = 0;
        } else {
            a[i][n+1] = 1;
        }
    }
    if(xor_gauss() == -1) puts("N");
    else puts("Y");
    return 0;
}
```

-----

## D Divisibility Dance

### 题意

​	有N个男人与N个女人，每个人都有一个年龄。

​	男人、女人各自围成一个圈，组成两个同心圆。一个男人与一个女人一一对应，成为搭档。

​	从男人围成的圈开始，男人围成的圈与女人围成的圈轮流进行操作：每次操作使得这个圈顺时针旋转任意步数。要求旋转后原来每一对男女的搭档转变，并且在这个旋转的过程中一对男女不能面对面超过一次（即一次旋转步数要$< N$）。

​	一个操作序列是合法的，当且仅当这个N组搭档，每一对搭档的年龄和mod M相等。

​	问操作次数为K的合法操作序列个数。

​	$N\le 10^6, M\le 10^9,K\le 10^9$

### 思路

​	首先，操作实际上可以看作一种，将一个圈看作静止，那么相当于只有另一个圈在运动。

​	问题可以转化为：有多少种合法的最终匹配，K次操作达到这个状态的序列有多少种。

​	拆开来看，先解决第一个问题：有多少种合法的最终匹配。

​	对于女人age序列A，男人age序列B，一个匹配合法当且仅当它们相应位置和mod M相同。如果他们匹配的第一个位置确定了，则要求他们差分序列满足和为0，即A的差分序列等于B的差分序列相反数。那么问题转化为字符串匹配问题。

​	然后解决第二个问题：K次操作达到这个状态的序列有多少种。

​	由于与原始状态不相同的操作序列实际上是等价的，由此优化DP。定义$f(x,0)$为操作x后，与原始状态相同状态的操作序列，$f(x,1)$为操作x后，与原始状态不相同状态的操作序列。那么有：

​		$f(x,0) = (N-1)f(x-1,1)$

​		$f(x,1)=f(x-1,0)+(N-2)f(x-1,1)$

​	矩阵运算加速递推即可。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define ll long long

inline int add(int a,int b,int mod){return a+b>=mod? a+b-mod: a+b;}
inline int sub(int a,int b,int mod){return a<b? a-b+mod: a-b;}
inline int mul(int a,int b,int mod){return 1LL*a*b%mod;}

const int mod = 1e9+7;
const int N = 3e6+10;

int cnt1, cnt2;
int s[N], p[N], slen, plen;
int Next[N];
void getFail(){
    Next[0] = Next[1] = 0;
    for(int i=1; i<plen; i++){
        int j = Next[i];
        while(j && p[i]!=p[j]) j=Next[j];
        Next[i+1] = (p[i]==p[j])? j+1: 0;
    }
}
void kmp(){
    getFail();
    cnt1 = cnt2 = 0;
    for(int i=0, j=0; i<slen; i++){
        while(j && s[i]!=p[j]) j=Next[j];
        if(s[i]==p[j]) j++;
        if(j == plen) {
            if(i == plen-1) cnt1++;
            else cnt2++;
            j=Next[j];
        }
    }
}

void mul(int f[2],int a[2][2],int mod){
    int c[2];
    memset(c, 0, sizeof(c));
    for(int j=0; j<2; j++){
        for(int k=0; k<2; k++){
            c[j] = add(c[j], mul(f[k],a[k][j],mod),mod);
        }
    }
    memcpy(f, c, sizeof(c));
}
void mulself(int a[2][2],int mod){
    int c[2][2];
    memset(c, 0, sizeof(c));
    for(int i=0; i<2; i++){
        for(int j=0; j<2; j++){
            for(int k=0; k<2; k++){
                c[i][j] = add(c[i][j],mul(a[i][k],a[k][j],mod),mod);
            }
        }
    }
    memcpy(a, c, sizeof(c));
}
void solve(int f[2], int a[2][2], int n, int mod){
    for(; n; n>>=1){
        if(n&1) mul(f, a,mod);
        mulself(a,mod);
    }
}

#define gc() getchar()
inline int read()
{
    int now=0,f=1; char c=gc();
    for(; !isdigit(c); c=='-'&&(f=-1),c=gc());
    for(; isdigit(c); now=now*10+c-48,c=gc());
    return now*f;
}

int n, m, k;
int a[N], b[N];

int main(){
    n = read(); m = read(); k = read();
    for(int i=1; i<=n; i++){
        a[i] = read(); a[i] %= m;
        a[i+n] = a[i];
    }
    for(int i=1; i<=n; i++){
        b[i] = read(); b[i] %= m;
    }
    for(int i=1; i<=2*n-2; i++) {
        s[i-1] = sub(a[i+1],a[i],m);
    }
    for(int i=1; i<=n-1; i++) {
        p[i-1] = sub(b[i],b[i+1],m);
    }
    slen = 2*n-2; plen = n-1;
    kmp();

    int f[2] = {1,0};
    int a[2][2] = {{0,1},
                   {n-1,n-2}};
    solve(f,a,k,mod);
    printf("%d\n",add(mul(cnt1,f[0],mod),mul(cnt2,f[1],mod),mod));
    return 0;
}
```

****