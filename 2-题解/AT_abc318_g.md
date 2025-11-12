---
tags:
  - 题解
  - 网络流/最大流
aliases:
  - Typical Path Problem
---
## [AT_abc318_g. Typical Path Problem](https://atcoder.jp/contests/abc318/tasks/abc318_g)

等价于从 $B$ 出发，询问是否存在两条两条不相交的路径 $B\to A$ 和 $B\to C$
限制点的通过次数，考虑网络流。
将每个点 $u$ 拆为入点 $u$ 和出点 $u'$，建一条 $u\to u'$，容量为 $1$ 的边，表示这个点只能经过一次。
从源点 $S$ 向 $B$ 连容量为 $2$ 的边，表示要找两条路径。
从 $A'$ 和 $C'$ 向汇点 $T$ 连容量为 $1$ 的边，表示要找的两条路径的终点。
跑最大流即可，当最大流等于 $2$ 的时候可以找到这样的路径。
最多需要两轮增广，时间复杂度 $O(n)$

[***AC 代码***](https://atcoder.jp/contests/abc318/submissions/70714285)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

template<typename T>
struct Flow {
    struct _edge {
        int to;
        T cap;
        _edge(int to, T cap): to(to), cap(cap) { }
    };

    int n;
    vector<_edge> e;
    vector<vector<int>> g;
    vector<int> cur, h;

    Flow() { }
    Flow(int n): n(n), e(), g(n+1), cur(n+1), h(n+1) { }

    bool bfs(int s, int t) {
        h.assign(n+1, -1);
        queue<int> que;
        h[s] = 0;
        que.push(s);
        while (!que.empty()) {
            const int u = que.front(); que.pop();
            for (auto i: g[u]) {
                auto [v, c] = e[i];
                if (c > 0 && h[v] == -1) {
                    h[v] = h[u] + 1;
                    if (v == t) {
                        return true;
                    }
                    que.push(v);
                }
            }
        }
        return false;
    }

    T dfs(int u, int t, T f) {
        if (u == t) { return f; }
        auto r = f;
        for (int &i = cur[u]; i < int(g[u].size()); ++i) {
            const int j = g[u][i];
            auto [v, c] = e[j];
            if (c > 0 && h[v] == h[u] + 1) {
                auto a = dfs(v, t, min(r, c));
                e[j].cap -= a;
                e[j ^ 1].cap += a;
                r -= a;
                if (r == 0) {
                    return f;
                }
            }
        }
        return f - r;
    }

    void add_edge(int u, int v, T c) {
        g[u].push_back(e.size());
        e.emplace_back(v, c);
        g[v].emplace_back(e.size());
        e.emplace_back(u, 0);
    }

    T maxFlow(int s, int t) {
        T ans = 0;
        while (bfs(s, t)) {
            cur.assign(n+1, 0);
            ans += dfs(s, t, numeric_limits<T>::max());
        }
        return ans;
    }

    vector<bool> minCut() {
        vector<bool> c(n+1);
        for (int i = 0; i <= n; ++i) {
            c[i] = (h[i] != -1);
        }
        return c;
    }

    struct edge {
        int fr;
        int to;
        T cap;
        T flow;
    };
    vector<edge> edges() {
        vector<edge> a;
        for (int i = 0; i < int(e.size()); i += 2) {
            edge x;
            x.fr = e[i + 1].to;
            x.to = e[i].to;
            x.cap = e[i].cap + e[i + 1].cap;
            x.flow = e[i + 1].cap;
            a.push_back(x);
        }
        return a;
    }
};

int main()
{
    std::ios::sync_with_stdio(0); std::cin.tie(0); std::cout.tie(0);
    int n, m, A, B, C;
    std::cin >> n >> m >> A >> B >> C;

    Flow<int> G(2 * n + 2);
    int s = 1, t = 2 * n + 2;
    for (int i = 1; i <= n; ++i) {
        G.add_edge(2 * i, 2 * i + 1, 1);
    }
    G.add_edge(s, 2 * B, 2);
    G.add_edge(2 * B, 2 * B + 1, 1);
    G.add_edge(2 * A + 1, t, 1);
    G.add_edge(2 * C + 1, t, 1);

    for (int i = 1; i <= m; ++i) {
        int u, v;
        std::cin >> u >> v;
        G.add_edge(2 * u + 1, 2 * v, 1);
        G.add_edge(2 * v + 1, 2 * u, 1);
    }

    int f = G.maxFlow(s, t);
    std::cout << (f == 2 ? "Yes": "No") << "\n";
}
```
