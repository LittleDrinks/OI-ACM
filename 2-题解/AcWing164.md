---
tags:
  - 题解
  - 拓扑排序
aliases:
---
## [AcWing164. 可达性统计](https://www.acwing.com/problem/content/166/)

按逆拓扑序遍历所有点，用 `std::bitset` 存储一个点可以到达哪些点，可以在 $O\left(\dfrac{n^2}{w}\right)$ 的时空复杂度内求解有向无环图中一个点可以到达哪些点。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/41693013/)

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=3e4+5;
int n, m, deg[N];
bitset<N> r[N];
vector<int> G[N];
int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    cin >> n >> m;
    for (int i = 1; i <= m; ++i) {
        int u, v;
        cin >> u >> v;
        G[u].push_back(v);
        ++deg[v];
    }

    queue<int> q;
    for (int i = 1; i <= n; ++i) {
        if (deg[i] == 0) { q.push(i); }
    }

    vector<int> topo;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        topo.push_back(u);
        for (auto v: G[u]) {
            if (--deg[v] == 0) {
                q.push(v);
            }
        }
    }

    assert(topo.size() == n);
    for (int i = n-1; i >= 0; --i) {
        int u = topo[i];
        r[u][u] = 1;
        for (auto v: G[u]) {
            r[u] |= r[v];
        }
    }

    for (int i = 1; i <= n; ++i) {
        cout << r[i].count() << "\n";
    }
}

```
