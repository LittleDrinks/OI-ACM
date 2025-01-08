---
tags:
  - 模板
---
```cpp
const int N=5e5+5, I=20;
int n, m, s, dep[N], f[I+5][N];
vector <int> G[N];

void dfs(int u, int fa)
{   // 预处理深度和父节点信息
    dep[u] = dep[fa] + 1;
    f[0][u] = fa;
    for (int i = 1; i <= I; ++i) {
        f[i][u] = f[i-1][f[i-1][u]];
    }
    for (int v: G[u]) {
        if (v != fa) { dfs(v, u); }
    }
}

int lca(int u, int v)
{   // 求 lca
    if (dep[u] < dep[v]) { swap(u, v); }
    for (int i = I; i >= 0; --i) {
        if (dep[f[i][u]] >= dep[v]) { u = f[i][u]; }
    }
    if (u == v) { return u; }
    for (int i = I; i >= 0; --i) {
        if (f[i][u] != f[i][v]) { u=f[i][u]; v=f[i][v]; }
    }
    return f[0][u];
}
```
