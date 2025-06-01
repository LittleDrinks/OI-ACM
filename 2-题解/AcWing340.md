---
tags:
  - 题解
  - 最短路
  - 二分
aliases:
  - 通信线路
---
## [AcWing340. 通信线路](https://www.acwing.com/problem/content/342/)

题目可以转换为求最小的第 $k+1$ 大路径边权 $x$。
考虑二分答案。二分所求的最小的第 $k$ 大边权 $x$，将边权不大于 $x$ 的边重设为 $0$，大于 $x$ 的边重设为 $1$。
$x$ 越大，新图上 $a$ 到 $b$ 最短路就越小。此时令 $a$ 到 $b$ 最短路小于等于 $k$ 的最小的 $x$ 即为所求。
由于这张图是 0/1 边权图，所以可以使用双端队列 BFS 在 $O(m)$ 的时间内求出最短路。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/41824275/)

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    int n, m, k;
    cin >> n >> m >> k;
    vector<array<int,3>> E(m);
    for (auto& [u, v, w]: E) {
        cin >> u >> v >> w;
    }

    auto check = [&](int x){
        vector G(n+1, vector<pair<int,int>>());
        for (auto [u, v, w]: E) {
            w = (w > x);
            G[u].emplace_back(v, w);
            G[v].emplace_back(u, w);
        }

        deque<pair<int,int>> q;
        vector<int> dis(n+1, INT_MAX);
        q.emplace_front(1, 0);
        while (!q.empty()) {
            auto [u, d] = q.front(); q.pop_front();
            if (dis[u] != INT_MAX) {
                continue;
            }
            dis[u] = d;
            for (auto [v, w]: G[u]) {
                if (dis[v] != INT_MAX) {
                    continue;
                }
                if (w == 0) {
                    q.emplace_front(v, d);
                } else {
                    assert(w == 1);
                    q.emplace_back(v, d+w);
                }
            }
        }

        return dis[n]<=k;
    };

    int L=-1, R=1000001;
    while (L < R-1) {
        int m = (L + R) >> 1;
        if (check(m)) {
            R = m;
        } else {
            L = m;
        }
    }
    if (R < 1000001) { cout << R << "\n"; }
    else { cout << "-1\n"; }
}
```
