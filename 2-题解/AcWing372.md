---
tags:
  - 题解
aliases:
  - AcWing372. 棋盘覆盖
  - 棋盘覆盖
speed:
---
## [372. 棋盘覆盖]()

将棋盘上的各自按照 $(i+j)$ 的奇偶性分为黑色和白色，每次相当于选择一黑一白相连，将本题转化为二分图匹配。
按照上述含义建图，注意特判不能放置的位置，然后跑二分图匹配。$N^2$ 个点，$C_{N/2}^2$ 条边，时间复杂度 $O(N^4)$.

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36904298/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=105, dx[]={1,0,-1,0}, dy[]={0,1,0,-1};
int n, t, match[N*N], ans;
bool fbd[N*N], vis[N*N];
vector <int> G[N*N];

int id(int x, int y) { return (x-1)*n+y; }

bool in(int x, int y) { return (1<=x && x<=n && 1<=y && y<=n); }

bool dfs(int id)
{
    for (int v: G[id]) {
        if (!vis[v]) {
            vis[v] = true;
            if (!match[v] || dfs(match[v])) {
                match[v] = id; return true;
            }
        }
    }
    return false;
}

int main()
{
    cin >> n >> t;
    for (int i = 1; i <= t; ++i) {
        int x, y; cin >> x >> y;
        fbd[id(x,y)] = true;
    }
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (fbd[id(i,j)] || (i+j)&1) { continue; }
            for (int d = 0; d < 4; ++d) {
                if (in(i+dx[d], j+dy[d]) && !fbd[id(i+dx[d],j+dy[d])]) {
                    G[id(i,j)].push_back( id(i+dx[d],j+dy[d]) );
                }
            }
        }
    }
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (fbd[id(i,j)] || (i+j)&1) { continue; }
            memset(vis, 0, sizeof(vis));
            ans += dfs( id(i,j) );
        }
    }
    cout << ans << endl;
}
```
