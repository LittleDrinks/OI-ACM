---
tags:
  - 题解
  - 最短路
aliases:
  - 最优贸易
---
## [AcWing341. 最优贸易](https://www.acwing.com/problem/content/343/)

从 $1$ 走到 $i$，路径上最低买入价格记为 $pmin$。
从 $i$ 走到 $n$，路径上最大卖出价格记为 $pmax$。
枚举 $i$，$\displaystyle\max_{i=1}^n(pmax_i-pmin_i)$ 即为答案。
因为这可能是一张有环图，有后效性，所以无法采用 DP 的方式维护上述两个量。
考虑最短路，最一般的最短路维护的是路径上的 $sum$ 性质，而本题维护的是 $\max$ 和 $\min$ 性质。考虑 $(5,10),\ (6,11),\ (7,1)$ 的这样一个环，从 $5\to6\to7\to5$ 绕一圈，节点 $5$ 的 $pmin$ 会从 $11$ 变小为 $1$，相当于“负环”，因此无法使用 Dijkstra 算法，只能用 SPFA。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/41824857/)

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    int n, m;
    cin >> n >> m;

    vector<int> p(n+1), mx(n+1, -0x3f3f3f3f), mn(n+1, 0x3f3f3f3f);
    for (int i = 1; i <= n; ++i) {
        cin >> p[i];
    }

    vector<array<int,3>> E(m);
    for (auto& [x, y ,z]: E) {
        cin >> x >> y >> z;
    }

    auto build = [&](bool flip) -> vector<vector<int>> {
        vector G(n+1, vector<int>());
        for (auto [x, y, z]: E) {
            if (flip) { swap(x, y); }
            G[x].push_back(y);
            if (z == 2) { G[y].push_back(x); }
        }
        return G;
    };

    auto spfa = [&](int s, bool flip, auto check) -> void {
        auto G = build(flip);
        queue<int> q;
        vector<bool> iq(n+1);
        mx[s] = max(mx[s], p[s]);
        mn[s] = min(mn[s], p[s]);
        q.push(s);
        iq[s] = 1;
        while (!q.empty()) {
            auto u = q.front(); q.pop();
            iq[u] = 0;
            for (auto v: G[u]) {
                if (check(u, v)) {
                    if (!iq[v]) {
                        iq[v] = 1;
                        q.push(v);
                    }
                }
            }
        }
    };

    spfa(1, false, [&](int u, int v) -> bool {
        if (mn[v] > mn[u]) {
            mn[v] = min(p[v], mn[u]);
            return true;
        }
        return false;
    });

    spfa(n, true, [&](int u, int v) -> bool {
        if (mx[v] < mx[u]) {
            mx[v] = max(p[v], mx[u]);
            return true;
        }
        return false;
    });

    int ans = 0;
    for (int i = 1; i <= n; ++i) {
        ans = max(ans, mx[i]-mn[i]);
    }
    cout << ans << "\n";
}
```
