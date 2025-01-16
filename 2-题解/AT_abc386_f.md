---
tags:
  - 题解
  - DP
aliases:
---
## [[ABC386F] Operate K](https://www.luogu.com.cn/problem/AT_abc386_f)

前置题：[[P2758|编辑距离]]
记 $f(i,j)$ 表示 $s[1:i]$ 和 $t[1:j]$ 之间的编辑距离。根据前置题可以做到 $O(nm)$ 的编辑距离。
不难发现当 $|i-j|>k$ 时，$f(i,j)>k$，此时具体的步数是无用的，无需继续计算。
优化无用状态后，总状态数为 $n\cdot(2k+1)$，时空复杂度均为 $O(nk)$。
具体实现时，可以使用记忆化搜索，通过 `int& dp()` 将 $i,j$ 映射到实际存储的数组当中。

#### [AC 代码](https://atcoder.jp/contests/abc386/submissions/61732401)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int inf=1e9+5, N=5e5+5, K=25<<1, P=22;
int k, n, m, f[N][K];
bool vs[N][K];
string s, t;

bool& vis(int u, int v) { return vs[u][u-v+P]; }
int& dp(int u, int v) { return f[u][u-v+P]; }

int dfs(int u, int v)
{
    if (abs(u-v) > k) { return inf; }
    if (vis(u, v)) { return dp(u,v); } 
    vis(u,v) = true;
    if (u == 0) { return dp(u,v) = v; }
    if (v == 0) { return dp(u,v) = u; }
    return dp(u,v) = min( { dfs(u-1,v)+1, dfs(u,v-1)+1, dfs(u-1,v-1)+(s[u]!=t[v]) } );
}

int main()
{
    cin.tie(nullptr)->sync_with_stdio(false);
    cin >> k >> s >> t;
    n = s.length(); s=" "+s;
    m = t.length(); t=" "+t;
    cout << (dfs(n, m)<=k? "Yes": "No") << "\n";
}
```
