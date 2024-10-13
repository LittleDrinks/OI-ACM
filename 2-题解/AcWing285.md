---
tags:
  - 题解
  - DP
  - DP/树形DP
aliases:
  - AcWing285. 没有上司的舞会
  - P1352 没有上司的舞会
speed:
---
## [285. 没有上司的舞会](https://www.acwing.com/problem/content/287/)

记 $f(i,0/1)$ 表示节点 $i$ 强制不选/选的时候，这棵子树内的最大快乐指数和。
$$
\begin{cases}
f(u,0)=\sum_{v\in son[u]} \max(f(v,0), f(v,1))\\
f(u,1)=\sum_{v\in son[u]} f(v,0)
\end{cases}
$$

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/37381372/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=6005;
int n, h[N], deg[N], f[N][2];
vector <int> G[N];

void dfs(int u, int fa)
{
    f[u][1] = h[u];
    for (int v: G[u]) {
        if (v != fa) {
            dfs(v, u);
            f[u][1] += f[v][0];
            f[u][0] += max(f[v][0], f[v][1]);
        }
    }
}

int main()
{
    cin >> n;
    for (int i = 1; i <= n; ++i) { cin >> h[i]; }
    for (int i = 1; i < n; ++i) {
        int u, v;
        cin >> u >> v;
        ++deg[u];
        G[v].push_back(u);
    }
    int s = 1;
    for (int i = 1; i <= n; ++i) {
        if (!deg[i]) { s=i; }
    }
    dfs(s, 0);
    cout << max(f[s][0], f[s][1]) << endl;
}
```
