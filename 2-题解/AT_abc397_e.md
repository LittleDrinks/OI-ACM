---
tags:
  - 题解
  - DP
  - DP/树形DP
aliases:
---
## [AT_abc397_e - Path Decomposition of a Tree](https://atcoder.jp/contests/abc397/tasks/abc397_e)

树上问题，考虑在父结点 $u$ 处进行判断。计算在这棵子树内未分配路径的节点个数，和 $u$ 子节点的个数。
显然，如果子树内的未分配路径的节点个数大于 $k$，或者子节点个数大于 $3$，那么没法通过一条经过 $u$ 的路径让子树内的这些节点符合条件，直接输出 `No` 即可。
如果子节点个数等于 $2$，此时必须构造一条经过 $u$ 的路径将这些点串起来，此时如果未分配路径的节点个数小于 $k$，直接输出 `No`。否则将子树中未分配路径的节点置为 $0$。
最后检查 `siz[1]` 是否为零即可。

[***AC 代码***](https://atcoder.jp/contests/abc397/submissions/63855102)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=2e5+5;
int n, k, siz[N];
vector<int> G[N];

void dfs(int u, int fa)
{
    siz[u] = 1;
    int son = 0;
    for (int v: G[u]) {
        if (v != fa) {
            dfs(v, u);
            if (siz[v] > 0) { ++son; }
            siz[u] += siz[v];
        }
    }
    if (siz[u] > k || son >= 3) { cout << "No\n"; exit(0); }
    else if (siz[u] < k && son >= 2) { cout << "No\n"; exit(0); }
    else if (siz[u] == k) { siz[u] = 0; }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    cin >> n >> k;
    for (int i = 1; i < n*k; ++i) {
        int u, v;
        cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1, 0);
    if (siz[1] == 0) { cout << "Yes\n"; }
    else { cout << "No\n"; }
}
```
