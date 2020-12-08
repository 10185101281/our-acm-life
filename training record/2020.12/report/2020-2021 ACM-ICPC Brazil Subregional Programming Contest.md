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