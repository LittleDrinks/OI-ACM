---
tags:
  - 题解
  - tarjan/PBCC
  - 二分图
aliases:
---
## [AcWing365. 圆桌骑士](https://www.acwing.com/problem/content/367/)

建补图，可以相邻的骑士连边。

> [!note] 引理 1：如果图上两个点不在一个点双连通分量内，则这两个点必然不能被一个环包含，反之必然可以被一个环包含
> 由点双连通分量定义得证

> [!note] 引理 2：如果一个点双连通分量内存在至少一个奇环，则这个点双连通分量内所有的点都可以被包含在一个奇环内
> 考虑奇环上的两点 $u,v$，以及环外部的一个点 $x$，记环上 $uv$ 距离为 $w_1$，环外 $ux$ 和 $vx$ 距离和为 $w_2$
> 如果 $w_1$ 和 $w_2$ 奇偶性相同，则将环上 $uv$ 间的路径替换为 $uxv$ 同样是一个奇环
> 如果 $w_1$ 和 $w_2$ 奇偶性不同，则 $w_1$ 和 $w_2$ 组成一个新的奇环

点双缩点，每个点双不能二部图染色则合法，能二部图染色则不合法。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/42087229/)

*AcWing 上面只有一个测试点，但是牛客和 SPOJ 测评下来都没问题。*

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef __int128 i128;
typedef long double db;

const int N=1e5+5;
int n, m;
vector<pair<int,int>> G[N];

int totdfn, colcnt, dfn[N], low[N];
vector<int> stk;
vector<vector<int>> pbcc;
int root;
void tarjan(int u, int uid)
{
    int son = 0;
    stk.push_back(u);
    dfn[u] = low[u] = ++totdfn;
    for (auto [v, vid]: G[u]) {
        if (!dfn[v]) {
            ++son;
            tarjan(v, vid);
            low[u] = min(low[u], low[v]);
            if (low[v] >= dfn[u]) {
                pbcc.push_back({});
                while (stk.back() != v) {
                    pbcc.back().push_back(stk.back()); stk.pop_back();
                }
                pbcc.back().push_back(v); stk.pop_back();
                pbcc.back().push_back(u);
            }
        } else if (uid != vid) {
            low[u] = min(low[u], dfn[v]);
        }
    }
    if (u == 1 && !son) {
        pbcc.push_back({u});
    }
}
void work()
{
    for (int i = 1; i <= n; ++i) {
        if (!dfn[i]) {
            root = i;
            tarjan(i, 0);
        }
    }
}

bool valid[N];
void check()
{
    vector<bool> now(n+1);
    vector<int> col(n+1);
    for (auto C: pbcc) {
        bool ok = 0;
        for (auto x: C) { now[x] = 1; }
        queue<pair<int,int>> q;
        q.emplace(C.front(), 1);
        while (!q.empty()) {
            auto [u, c] = q.front(); q.pop();
            col[u] = c;
            for (auto [v, vid]: G[u]) {
                if (!now[v]) { continue; }
                if (!col[v]) { q.emplace(v, 3-c); }
                else if (col[v] != 3-c) { ok = 1; break; }
            }
        }
        for (auto x: C) {
            now[x] = col[x] = 0;
            if (ok) { valid[x] = 1; }
        }
    }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    while ((cin >> n >> m) && !(n==0 && m==0)) {
        vector adj(n+1, vector<bool>(n+1, 1));
        for (int i = 1; i <= m; ++i) {
            int u, v;
            cin >> u >> v;
            adj[u][v] = 0;
        }
        int totedg = 0;
        for (int i = 1; i <= n; ++i) {
            for (int j = i+1; j <= n; ++j) {
                if (adj[i][j]) {
                    ++totedg;
                    G[i].emplace_back(j, totedg);
                    G[j].emplace_back(i, totedg);
                }
            }
        }
        work();
        check();
        int ans = 0;
        for (int i = 1; i <= n; ++i) {
            ans += !valid[i];
        }
        cout << ans << "\n";

        totdfn = colcnt = 0;
        pbcc.clear();
        for (int i = 1; i <= n; ++i) {
            G[i].clear();
            valid[i] = 0;
            dfn[i] = low[i] = 0;
        }
    }
}
```
