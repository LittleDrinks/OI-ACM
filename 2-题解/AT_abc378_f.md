---
tags:
  - 题解
  - 并查集
aliases:
---
## [\[ABC378F\] Add One Edge 2](https://www.luogu.com.cn/problem/AT_abc378_f)

原题等价于求“以度数为 $2$ 的节点为端点，中间全是度数为 $3$ 的节点”的路径数。
用并查集维护若干个度数为 $3$ 的节点的连通块，并记录与连通块直接相连的度数为 $2$ 的节点个数，记为 $cnt$，则答案为：
$$
\sum C_{cnt}^2
$$

#### [AC代码](https://www.luogu.com.cn/record/187870508)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int N=2e5+5;
int n, deg[N];
vector <int> G[N];
ll ans;

struct Dsu {
    int fa[N];
    ll cnt[N];
    void init() {
        for (int i = 1; i <= n; ++i) { fa[i]=i; }
    }
    int find(int x) {
        return fa[x]==x? fa[x]: find(fa[x]);
    }
    void merge(int x, int y) {
        x = find(x);
        y = find(y);
        fa[y] = x;
        cnt[x] += cnt[y];
    }
    void add(int x) {
        ++cnt[find(x)];
    }
} D;

void dfs(int u, int fa)
{
    if (deg[u]==3 && deg[fa]==2) { D.add(u); } 
    for (int v: G[u]) {
        if (v != fa) {
            if (deg[u]==3 && deg[v]==3) { D.merge(u, v); }
            if (deg[u]==3 && deg[v]==2) { D.add(u); }
            dfs(v, u);
        }
    }
}

int main()
{
    cin >> n; D.init();
    for (int i = 1; i < n; ++i) {
        int u, v;
        cin >> u >> v;
        G[u].push_back(v); ++deg[u];
        G[v].push_back(u); ++deg[v];
    }
    dfs(1, 0);
    for (int i = 1; i <= n; ++i) {
        int fa=D.find(i);
        ans += D.cnt[fa]*(D.cnt[fa]-1)/2;
        D.cnt[fa] = 0;
    }
    cout << ans << endl;
}
```
