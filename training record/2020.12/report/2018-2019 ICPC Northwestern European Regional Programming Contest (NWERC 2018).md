# 2018-2019 ICPC Northwestern European Regional Programming Contest (NWERC 2018)

-----

## A Access Points

### 思路

​	首先，x、y是相互独立的，可以分开计算。

​	以x为例。问题转化为，构造一个单调不减的序列p，使得$\sum_{i=1}^n{(p_i-x_i)^2}$。

- 当x是单调不减的，令$p_i=x_i$；
- 当x是单调递减的，令$p_i=avg(x)$。
- 否则如果x不是单调的，考虑从前往后将x中的数加入序列中，将x划分为若干段，每一段满足这一段的均值$\ge$上一段的均值是最优的。
    - 【证明】
    - 首先将一个连续的区间抽象成块，值等于平均值。任意两个块入如果前面的块比后面的块值更大，合并后答案更优。
    - 然后考虑这样贪心的合并是最优的。假设对于某个时刻已经完成合并。在两个块的边界处，存在一个点可以划分到前一块中，仍然满足块的单调性。不妨设两个块的值为$h_1,h_2$，将这个点划给前一个块后，两个块的值为$h_1',h_2'$，这个点的值为a，我们有$h_1<h_1'\le h_2'\le h_2<a$，因此将这个点划分到第二个块中对整体答案一定是更优的。所以满足最优子结构，因此贪心是正确的。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define pb push_back
#define fir first
#define sec second
#define ll long long
#define mp make_pair

const int N = 1e5+10;
int n;
double x[N], y[N];
pair<double, int> stk[N];
double cal(double *p){
    int tp = 0;
    for(int i=1; i<=n; i++){
        double d = p[i];int len = 1;
        while(tp && stk[tp].fir*len >= stk[tp].sec*d){
            len += stk[tp].sec;
            d += stk[tp].fir;
            tp--;
        }
        stk[++tp] = mp(d, len);
    }
    int nw = n;
    double ret = 0;
    while(tp){
        double d = stk[tp].fir; int len = stk[tp].sec;
        double avg = d/len;
        for(int i=nw-len+1; i<=nw; i++){
            ret += (p[i]-avg)*(p[i]-avg);
        }
        tp--;
        nw -= len;
    }
    return ret;
}
int main(){
    cin>>n;
    for(int i=1; i<=n; i++) scanf("%lf%lf",x+i,y+i);
    printf("%.10lf\n",cal(x)+cal(y));
    return 0;
}
```

