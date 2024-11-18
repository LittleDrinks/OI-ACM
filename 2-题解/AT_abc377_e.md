---
tags:
  - 题解
aliases:
  - "[ABC377E] Permute K times 2"
---
## [[ABC377E] Permute K times 2](https://www.luogu.com.cn/problem/AT_abc377_e)

$k\leq10^{18}$ 是在提示往循环节上考虑，那就要考虑清楚它是怎么循环的。
建图，从 $p_i$ 向 $p_{p_i}$ 连单向边，最后必然会形成若干个环，$p_i$ 的变换只可能在环上完成。
考虑第二次操作，$p_{p_i}$ 应该变为两轮前 $p^{3}(i)$ 这个位置上的数，而这个数应该变为两轮前 $P^4(i)$ 这个位置上的数。
一般化地，第 $k$ 次操作会让 $p_i$ 变为原先 $p^{2^k}(i)$ 这个位置上的数字，也就是在环上往前走了 $2^k-1$ 步。用快速幂处理出最后的位置即可。

#### [AC代码](https://www.luogu.com.cn/record/188627433)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int N=2e5+5;
int n, a[N], belong[N], tot, c[N];
ll k;
bool vis[N];
vector <int> G[N], V[N];

void dfs(int u, int fa, int id, int cnt)
{
    vis[u] = true;
    c[u] = cnt;
    belong[u] = id;
    V[id].push_back(u);
    for (int v: G[u]) {
        if (v != fa && !vis[v]) { dfs(v, u, id, cnt+1); }
    }
}

ll qp(ll a, ll b, ll MOD)
{
    ll res=1;
    for (; b; b/=2) {
        if (b & 1) { res = res*a%MOD; }
        a = a*a%MOD;
    }
    return res%MOD;
}

int main()
{
    cin >> n >> k;
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    for (int i = 1; i <= n; ++i) {
        G[a[i]].push_back(a[a[i]]);
    }
    for (int i=1; i <= n; ++i) {
        if (!vis[a[i]]) { dfs(a[i], 0, ++tot, 0); }
    }
    for (int i = 1; i <= n; ++i) {
        ll sz = V[belong[a[i]]].size();
        cout << V[belong[a[i]]][(c[a[i]]%sz+qp(2, k, sz)+sz-1)%sz] << " ";
    }
}
```