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

### 代码