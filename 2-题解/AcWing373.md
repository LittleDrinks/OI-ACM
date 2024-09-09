---
tags:
  - 题解
aliases:
  - 車的放置
  - AcWing373. 車的放置
speed:
---
## [373. 車的放置](https://www.acwing.com/problem/content/375/)

以行和列作为二分图的点，边表示在放置一个車。跑二分图匹配即可。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36905434/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=205;
int n, m, t, match[N], ans;
bool fbd[N][N], vis[N];

bool dfs(int u)
{
    for (int v = 1; v <= m; ++v) {
        if (!fbd[u][v] && !vis[v]) {
            vis[v] = true;
            if (!match[v] || dfs(match[v])) {
                match[v] = u; return true;
            }
        }
    }
    return false;
}

int main()
{
    cin >> n >> m >> t;
    for (int i = 1; i <= t; ++i) {
        int x, y; cin >> x >> y;
        fbd[x][y] = 1;
    }
    for (int i = 1; i <= n; ++i) {
        memset(vis, 0, sizeof(vis));
        ans += dfs(i);
    }
    cout << ans << endl;
}
```
