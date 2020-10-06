# Southwestern Europe Regional Contest (SWERC) 2019

----

## L River Game

### 题意

​	给出一个$N\times N$网络，方格分为3类：空地、湿地、保护区。

​	相连的湿地方格会形成湿地区（上下左右相连），保证湿地区接触网络的左右边界，并且湿地区的大小至多为$2N$。保证两个不同的湿地区之间任意两点距离至少为3。

​	现在两名玩家轮流在空地放置照相机，需要保证：接触湿地方格；接触同一个湿地区的照相机不能互相接触。

​	问先手必胜还是后手必胜。

### 思路

​	问题显然是ICG游戏，考虑设计SG函数。

​	保证两个不同湿地区之间任意两点距离至少为3，说明两个湿地区之间的游戏是独立的。

​	对于一个湿地区接触的空格块，互相不接触的空格联通块的游戏也是独立的。

​	对于每个独立空格联通块，最多只有$2N$，用状压DP处理其$SG$函数。然后根据这些独立联通块的SG函数可以得到整个局面的SG函数。

​	整体复杂度为$O(n\times 2^n)$。

### 代码

````c++
#include <bits/stdc++.h>
#define pii pair<int,int>
#define pb push_back
#define fir first
#define sec second
using namespace std;
const int N = 10;
const int M = 1e7;
int dx[4]{0, 0, 1, -1};
int dy[4]{-1, 1, 0, 0};
char g[N+3][N+3];
int n, v[N+3][N+3], in[N+3][N+3];
inline bool valid(int x,int y){
    return x>=1 && x<=n && y>=1 && y<=n;
}
inline bool isw(int x,int y){
    return  valid(x,y) && g[x][y]=='*';
}
void bfs(int x,int y){
    queue<pii> q;
    v[x][y]=1; q.push(pii{x, y});
    memset(in, 0, sizeof(in));
    while(!q.empty()){
        pii nw = q.front(); q.pop();
        in[nw.fir][nw.sec] = 1;
        for(int k=0; k<4; k++){
            pii nxt = pii{nw.fir+dx[k], nw.sec+dy[k]};
            if(isw(nxt.fir,nxt.sec) && !v[nxt.fir][nxt.sec]){
                v[nxt.fir][nxt.sec]=1; q.push(nxt);
            }
        }
    }
}

bool check(pii a, pii b){
    for(int k=0; k<4; k++){
        int tx=a.fir+dx[k], ty=a.sec+dy[k];
        if(valid(tx,ty) && tx==b.fir && ty==b.sec) return true;
    }
    return false;
}
vector<pii> tmp;
int f[M], cov[N*N+10];
int dp(){
    int sz = tmp.size();
    for(int i=0; i<sz; i++){
        cov[i] = (1<<i);
        for(int j=0; j<sz; j++){
            if(check(tmp[i], tmp[j])){
                cov[i] |= (1<<j);
            }
        }
    }
    int m = (1<<sz)-1;
    f[0] = 0;
    for(int i=1; i<=m; i++){
        int pool[35]{0};
        for(int j=0; j<sz; j++){
            if(i & (1<<j)){
                pool[ f[i & (m-cov[j])] ] = 1;
            }
        }
        for(int j=0; j<sz; j++){
            if(!pool[j]){
                f[i] = j; break;
            }
        }
    }
    return f[m];
}
vector<pii> vec;
int fa[N*N+10], has[N*N+10];
int get(int x){
    if(fa[x] == x) return x;
    return fa[x] = get(fa[x]);
}
int cal(){
    int sz=vec.size();
    for(int i=0; i<sz; i++) fa[i]=i, has[i]=0;
    for(int i=0; i<sz; i++){
        for(int j=i+1; j<sz; j++){
            if(check(vec[i], vec[j])){
                int fi = get(i), fj = get(j);
                fa[fj] = fi;
            }
        }
    }
    int SG=0;
    for(int i=0; i<sz; i++){
        int fi = get(i);
        if(has[fi]) continue;
        has[fi] = 1; tmp.clear();
        for(int j=0; j<sz; j++){
            int fj = get(j);
            if(fi == fj) tmp.pb(vec[j]);
        }
        SG^=dp();
    }
    return SG;
}
int main(){
    scanf("%d",&n);
    for(int i=1; i<=n; i++) scanf("%s",g[i]+1);
    memset(v,0, sizeof(v));
    int SG = 0;
    for(int i=1; i<=n; i++){
        for(int j=1; j<=n; j++){
            if(g[i][j]!='*' || v[i][j]) continue;
            bfs(i, j);
            vec.clear();
            for(int x=1; x<=n; x++){
                for(int y=1; y<=n; y++){
                    if(g[x][y]!='.') continue;
                    int ok=0;
                    for(int k=0; k<4; k++){
                        int tx = x+dx[k], ty = y+dy[k];
                        if(valid(tx,ty) && in[tx][ty]){
                            ok=1; break;
                        }
                    }
                    if(ok) vec.pb(pii{x, y});
                }
            }
            if(!vec.empty()) SG ^= cal();
        }
    }
    if(SG) puts("First player will win");
    else puts("Second player will win");
    return 0;
}
````

