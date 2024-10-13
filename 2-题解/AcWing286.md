---
tags:
  - 题解
  - DP
  - DP/树形DP
  - DP/背包
aliases:
  - AcWing286. 选课
  - P2014 [CTSC1997] 选课
speed:
---
## [286. 选课]()

对于每个节点，做一次背包。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/37381694/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=305;
int n, m, f[N][N];
vector <int> G[N];

void dfs(int u, int fa)
{
    for (int v: G[u]) {
        if (v != fa) {
            dfs(v, u);
            for (int w = m+1; w; --w) {
                for (int x = 1; x < w; ++x) {
                    f[u][w] = max(f[u][w], f[u][w-x]+f[v][x]);
                }
            }
        }
    }
}

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        int fa;
        cin >> fa >> f[i][1];
        for (int j = 2; j <= m+1; ++j) { f[i][j] = f[i][1]; }
        G[fa].push_back(i);
    }
    dfs(0, n+1);
    cout << f[0][m+1] << endl;
}
```
