---
tags:
  - 题解
  - DP
aliases:
  - Hungry Takahashi
---
## [AT_abc415_e. Hungry Takahashi](https://atcoder.jp/contests/abc415/tasks/abc415_e?lang=en)

带 $\log$ 的做法是显然的，这里记录一种严格线性的做法。
记 $dp(i,j)$ 表示从 $(i,j)$ 走到 $(n,m)$ 最少需要带多少钱。不妨设边界外的值无穷大，有状态转移方程
$$
dp(i,j) = \begin{cases}
\infty & {i>n\lor j>m} \\
p(n,m)-a(n,m) & i=n\land j=m \\
\max\bigg\{0,\min(dp(i+1,j),dp(i,j+1)) + p(i,j)-a(i,j)\bigg\} & other
\end{cases}
$$
时间复杂度 $O(nm)$

[***AC 代码***](https://atcoder.jp/contests/abc415/submissions/68071201)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

const ll inf = 1e18;

void solve()
{
	int n, m;
	cin >> n >> m;
    vector a(n+1, vector<ll>(m+1));
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            cin >> a[i][j];
        }
    }
    vector<ll> p(n+m);
    for (int i = 1; i < n+m; ++i) {
        cin >> p[i];
    }
    vector dp(n+2, vector<ll>(m+2, inf));
    dp[n][m] = max(0LL, p[n+m-1]-a[n][m]);
    for (int i = n; i; --i) {
        for (int j = m; j; --j) {
            if (i == n && j == m) { continue; }
            dp[i][j] = max(0LL, min(dp[i+1][j], dp[i][j+1]) + p[i+j-1] - a[i][j]);
        }
    }
    cout << dp[1][1] << "\n";
}

int main()
{
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
	int t = 1;
	// cin >> t;
	while (t--) { solve(); }
}
```
