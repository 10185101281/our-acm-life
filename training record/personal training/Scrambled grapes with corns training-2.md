# Scrambled grapes with corns training-2

----

## link

https://vjudge.net/contest/413512#overview

----

## D Emotional Fisherman

### 思路

​	DP后效性处理、数学。

​	将$a$排序，考虑填满一个长度为n的空位序列。

​	设$f_i$表示当前最大值是$a_i$的填充方案数，$lim_i$表示最大的$j$满足$2a_j\le a_i$。

​	考虑如何转移。假设当前枚举到$i$，考虑枚举前一个最大值的位置$j$。对于$j$，已经填空位数是$lim_j+1$，它的方案数是$f_j$。此时$a_i$一定是放在剩余空位的最前面的位置，这样在它前面的数就确定且一定合法了。然后考虑还有$lim_i-lim_j-1$个数需要放置，此时剩余$n-(lim_j+1)-1$个空位，那么方案是$A(n-lim_j-2,lim_i-lim_j-1)$的。剩余的$i-(lim_i+1)$个数，我们暂时先不放置它们，如果最后存在合法方案的话，那么最后剩余的数一定是0。

### 代码

```c++
#include <bits/stdc++.h>

using namespace std;
#define pb push_back
#define fir first
#define sec second
#define ll long long
#define pii pair<int, int>
#define mp make_pair

const int mod = 998244353;
inline int add(int a,int b){return a+b>=mod? a+b-mod: a+b;}
inline int mul(int a,int b){return 1LL*a*b%mod;}

const int N = 5e3+10;
int inv[N], fac[N], ifac[N];
void init(int n){
    inv[1] = fac[0] = fac[1] = ifac[0] = ifac[1] = 1;
    for(int i=2; i<=n; i++){
        inv[i] = mul(mod-mod/i,inv[mod%i]);
        fac[i] = mul(fac[i-1],i);
        ifac[i] = mul(ifac[i-1], inv[i]);
    }
}
int A(int n,int m){
    if(n<0 || m<0 || n<m) return 0;
    return mul(fac[n],ifac[n-m]);
}

ll a[N];
int lim[N], f[N];
int main(){
    int n; cin>>n;
    for(int i=1; i<=n; i++) {
        scanf("%lld",a+i);
        a[i] *= 2;
    }
    sort(a+1,a+1+n);
    for(int i=1; i<=n; i++){
        lim[i] = upper_bound(a+1,a+1+n,a[i]/2)-a-1;
    }
    if(lim[n] != n-1){
        cout<<0<<endl;
        return 0;
    }
    init(n);
    memset(f, 0, sizeof(f));
    f[0] = 1; lim[0] = -1;
    for(int i=1; i<=n; i++){
        for(int j=0; j<=lim[i]; j++){
            f[i] = add(f[i], mul(f[j], A(n-2-lim[j],lim[i]-lim[j]-1)));
        }
    }
    cout<<f[n]<<endl;
    return 0;
}
```

