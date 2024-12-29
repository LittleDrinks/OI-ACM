---
tags:
  - 模板
---
```cpp
const int N=5e5+5, I=20;
int n, m, s, dep[N], f[N][I+5];
vector <int> G[N];

void dfs(int u, int fa)
{   // 预处理深度和父节点信息
    dep[u] = dep[fa] + 1;
    f[u][0] = fa;
    for (int i = 1; i <= I; ++i) {
        f[u][i] = f[f[u][i-1]][i-1];
    }
    for (int v: G[u]) {
        if (v != fa) { dfs(v, u); }
    }
}

int lca(int u, int v)
{   // 求 lca
    if (dep[u] < dep[v]) { swap(u, v); }
    for (int i = I; i >= 0; --i) {
        if (dep[f[u][i]] >= dep[v]) { u = f[u][i]; }
    }
    if (u == v) { return u; }
    for (int i = I; i >= 0; --i) {
        if (f[u][i] != f[v][i]) { u=f[u][i]; v=f[v][i]; }
    }
    return f[u][0];
}
```
