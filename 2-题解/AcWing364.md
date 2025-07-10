---
tags:
  - 题解
  - tarjan/桥
  - tarjan/EBCC
aliases:
  - Network
---
## [AcWing364. 网络](https://www.acwing.com/problem/content/366/)

按边双连通分量将原来的图缩为一棵树。树上所有的边就是原来图中的桥。
每次添加一条边，相当于将树上两点简单路径上的所有边排除，每次直接暴力跳就可以了，时间复杂度 $O(m+qn)$。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/42086488/)

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef __int128 i128;
typedef long double db;

const int N=1e5+5;
int n, m;
vector<pair<int,int>> G[N];
vector<pair<int,bool>> T[N];
int totdfn, colcnt, dfn[N], low[N], col[N], cnt;
vector<int> stk;
set<pair<int,int>> E;

void tarjan(int u, int uid)
{
    stk.push_back(u);
    dfn[u] = low[u] = ++totdfn;
    for (auto [v, vid]: G[u]) {
        if (uid == vid) { continue; }
        if (!dfn[v]) {
            tarjan(v, vid);
            low[u] = min(low[u], low[v]);
        } else if (!col[v] && dfn[v] < dfn[u]) {
            low[u] = min(low[u], dfn[v]);
        }
    } 
    if (low[u] == dfn[u]) {
        col[u] = ++colcnt;
        int v;
        do {
            v = stk.back(); stk.pop_back();
            col[v] = colcnt;
        } while (v != u);
    }
}

int dep[N], f[20][N];
void dfs(int u, int fa)
{
    f[0][u] = fa;
    dep[u] = dep[fa] + 1;
    for (int i = 1; i < 20; ++i) {
        f[i][u] = f[i-1][ f[i-1][u] ];
    }
    for (auto [v, _]: T[u]) {
        if (v == fa) { continue; }
        dfs(v, u);
    }
}
int LCA(int u, int v) 
{
    if (dep[u] < dep[v]) { swap(u, v); }
    for (int i = 19; i >= 0; --i) {
        if (dep[f[i][u]] >= dep[v]) { u = f[i][u]; }
    }
    if (u == v) { return u; }
    for (int i = 19; i >= 0; --i) {
        if (f[i][u] != f[i][v]) {
            u = f[i][u];
            v = f[i][v];
        }
    }
    return f[0][u];
}

void dfs2(int u, int t)
{
    if (u == t) { return; }
    for (auto& [v, stat]: T[u]) {
        if (v == f[0][u]) {
            cnt -= stat;
            stat = 0;
            dfs2(v, t);
        }
    }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    int testcase = 0;
    while (cin >> n >> m && !(n==0 && m==0)) {
        cout << "Case " << ++testcase << ":\n";
        for (int i = 1; i <= m; ++i) {
            int u, v;
            cin >> u >> v;
            G[u].emplace_back(v, i);
            G[v].emplace_back(u, i);
        }
        tarjan(1, 0);
        for (int u = 1; u <= n; ++u) {
            for (auto [v, _]: G[u]) {
                if (col[u] < col[v]) { E.emplace(col[u], col[v]); }
            }
        }
        cnt = E.size();
        for (auto [u, v]: E) {
            T[u].emplace_back(v, 1);
            T[v].emplace_back(u, 1);
        }
        dfs(1, 0);

        int q;
        cin >> q;
        while (q--) {
            int u, v;
            cin >> u >> v;
            u = col[u];
            v = col[v];
            int lca = LCA(u, v);
            dfs2(u, lca);
            dfs2(v, lca);
            cout << cnt << "\n";
        }
        cout << "\n";

        for (int i = 1; i <= n; ++i) {
            G[i].clear();
            T[i].clear();
            low[i] = dfn[i] = col[i] = 0;
            totdfn = colcnt = 0;
            dep[i] = 0;
            cnt = 0;
            E.clear();
        }
    }
}

```
