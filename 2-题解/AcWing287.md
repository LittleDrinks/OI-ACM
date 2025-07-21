---
tags:
  - 题解
  - DP
  - DP/树形DP
  - DP/树形DP/二次扫描
aliases:
  - AcWing287. 积蓄程度
speed:
---
## [287. 积蓄程度](https://www.acwing.com/problem/content/289/)

先一次扫描，求出以 $1$ 为根时的答案。
第二次扫描，将根节点右父节点 $u$ 换为子节点 $v$。
$$
\begin{align}
g[v] = \\
& 以v为根的子树的最大流量 \\
& + \min\{c(u,v), 整棵树以u为根时除去v子树的流量\}\\
\end{align}
$$
注意叶子节点的讨论。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/37383015/)

```cpp
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef pair<int,ll> pil;

const int N=2e5+5;
int n, deg[N];
ll f[N], g[N];
vector <pil> G[N];

void dfs1(int u, int fa)
{
    for (auto [v, w]: G[u]) {
        if (v != fa) {
            dfs1(v, u);
            f[u] += (deg[v]==1? w: min(f[v], w));
        }
    }
}

void dfs2(int u, int fa)
{
    for (auto [v, w]: G[u]) {
        if (v != fa) {
            if (deg[u] == 1) {
                g[v] = f[v] + w;
            } else if (deg[v] == 1) {
                g[v] = f[v] + min(w, g[u]-w);
            } else {
                g[v] = f[v] + min(w, g[u]-min(f[v], w));
            }
            dfs2(v, u);
        }
    }
}

void solve()
{
    cin >> n;
    for (int i = 1; i < n; ++i) {
        int u, v; ll w;
        cin >> u >> v >> w;
        G[u].push_back({v,w});
        G[v].push_back({u,w});
        ++deg[u]; ++deg[v];
    }
    dfs1(1, 0);
    g[1] = f[1];
    dfs2(1, 0);
    ll ans = 0;
    for (int i = 1; i <= n; ++i) { ans = max(ans, g[i]); }
    cout << ans << endl;
    for (int i = 1; i <= n; ++i) {
        G[i].clear();
        deg[i] = f[i] = g[i] = 0;
    }
}

int main()
{
    int t; cin >> t;
    while (t--) { solve(); }
}
```
