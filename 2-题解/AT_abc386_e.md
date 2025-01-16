---
tags:
  - 题解
  - 搜索
aliases:
---
## [[ABC386E] Maximize XOR](https://www.luogu.com.cn/problem/AT_abc386_e)

最朴素的搜索方式是直接枚举每一位选择或者不选择，当选择的数字超过 $k$ 时跳出。会 TLE。从结果上考虑，有效的方案数为 $O\left(\dbinom{n}{k}\right)$，我们需要剪枝以使复杂度尽可能逼近 $O\left(\dbinom{n}{k}\right)$。
记 $dfs(u, rest, x)$ 表示当前枚举到第 $u$ 个数字，剩余 $rest$ 个数字待选择，当前异或和为 $x$。
第一种剪枝方法是，观察到随着 $k$ 的增大，$n$ 会急剧减小。当 $n-k<k$ 时，改为选择从 $n$ 个数中选取 $n-k$ 个数丢弃，这样能大幅减小 dfs 树的深度。
第二种剪枝方法是，当 $rest=n-u+1$，即当前未枚举的数字数量正好等于需要选择的数字数量，直接用后缀异或和计算答案。
代码为第二种剪枝方法。

#### [AC 代码](https://atcoder.jp/contests/abc386/submissions/61729635)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

int n, k;
ll ans;
vector<ll> a, s;

void dfs(int u, int rest, ll x)
{
    if (rest == 0) { ans = max(ans, x); return; }
    if (rest == n-u+1) { ans = max(ans, x^s[u]); return; }
    dfs(u+1, rest-1, x^a[u]);
    dfs(u+1, rest, x);
}

int main()
{
    cin.tie(nullptr)->sync_with_stdio(false);
    cin >> n >> k;
    a.resize(n+2); s.resize(n+2);
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    for (int i = n; i; --i) { s[i] = s[i+1] ^ a[i]; }
    dfs(1, k, 0);
    cout << ans << "\n";
}
```
