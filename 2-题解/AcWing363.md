---
tags:
  - 题解
  - tarjan/割点
aliases:
  - B城
---
## [AcWing363. B城](https://www.acwing.com/problem/content/365/)

对于一个非割点，删去与其相连的所有边后，剩下所有点和这个点无法连通，而剩余点之间的连通性没有影响，答案为 $$2(n-1)$$
对于一个割点，将其删去后会形成若干连通块，每个连通块与剩下所有点都无法连通，该部分的贡献为 $$\sum siz\times (n-siz)$$
注意在搜索树中的父节点，以及这个节点本身也是两个连通块。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/42085757/)

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef __int128 i128;
typedef long double db;

const int N=1e5+5;
int n, m;
vector<int> G[N];
int totdfn, dfn[N], low[N], siz[N];
bool cut[N];
ll ans[N];

void dfs(int u)
{
    dfn[u] = low[u] = ++totdfn;
    siz[u] = 1;
    int flag=0, sum=0;
    for (auto v: G[u]) {
        if (!dfn[v]) {
            dfs(v);
            siz[u] += siz[v];
            low[u] = min(low[u], low[v]);
            if (low[v] >= dfn[u]) {
                ++flag;
                sum += siz[v];
                ans[u] += (ll)siz[v] * (n-siz[v]);
                if (u != 1 || flag > 1) { cut[u] = 1; }
            }
        } else {
            low[u] = min(low[u], dfn[v]);
        }
    }
    if (cut[u]) { ans[u] += (ll)(sum+1) * (n-sum-1) + (n-1); }
    else        { ans[u] = 2 * (n-1); }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    cin >> n >> m;
    for (int i = 1; i <= m; ++i) {
        int u, v;
        cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1);
    for (int i = 1; i <= n; ++i) {
        cout << ans[i] << "\n";
    }
}

```
