---
tags:
  - 题解
  - DP/背包/退背包
aliases:
  - Must Buy
---
## [AT_abc441_f. Must Buy](https://atcoder.jp/contests/abc441/tasks/abc441_f)

最大价值 $V$ 可以用背包求出。
对于物品 $i$，记 $f_i(x)$ 表示去掉这件物品，花费不超过 $x$ 时的最大价值。
可以用背包求出 $f_i$，然后通过 $f_i(m-p_i)+v_i$ 强制选择这件物品。于是题目中三种分类对应的条件为：

- A 类：$f_i(m)<V$
- B 类：$f_i(m)=V\land f_i(m-p_i)+v_i=V$
- C 类：$f_i(m)=V\land f_i(m-p_i)+v_i<V$

直接做 $n$ 遍背包的复杂度是 $O(n^2m)$。
我们可以用“前缀和+后缀和”的形式维护这个退背包。
设 $dp_1(i,j)$ 和 $dp_2(i,j)$ 表示前 $i$ 件物品和后 $i$ 件物品，价格不超过 $j$ 时的最大价值。可以用一次 $O(m)$ 的卷积求出 $f_i(x)$，也就是：
$$
f_i(x)=\max_{j=0}^x \bigg\{ dp_1(i-1,j)+dp_2(i+1,x-j)\bigg\}
$$
我们只需要 $f_i(m)$ 和 $f_i(m-p_i)$ 就可以判断物品 $i$ 的类别，于是原问题可以在 $O(nm)$ 的时间内解决。

[***AC 代码***](https://atcoder.jp/contests/abc441/submissions/72612246)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;
template<typename T> void gmax(T&x,T y){ x = max(x, y); }

void solve()
{
    int n, m;
    cin >> n >> m;
    vector<ll> p(n + 1), v(n + 1);
    for (int i = 1; i <= n; ++i) {
        cin >> p[i] >> v[i];
    }
    vector dp1(n + 1, vector<ll>(m + 1));
    for (int i = 1; i <= n; ++i) {
        copy(dp1[i - 1].begin(), dp1[i - 1].end(), dp1[i].begin());
        for (int j = p[i]; j <= m; ++j) {
            gmax(dp1[i][j], dp1[i-1][j-p[i]] + v[i]);
        }
    }
    vector dp2(n + 2, vector<ll>(m + 1));
    for (int i = n; i; --i) {
        copy(dp2[i + 1].begin(), dp2[i + 1].end(), dp2[i].begin());
        for (int j = p[i]; j <= m; ++j) {
            gmax(dp2[i][j], dp2[i+1][j-p[i]] + v[i]);
        }
    }
    ll mx = dp1[n][m];
    for (int i = 1; i <= n; ++i) {
        ll dp[2] = {0, 0};
        for (int j = 0; j <= m - p[i]; ++j) {
            gmax(dp[0], dp1[i - 1][j] + dp2[i + 1][m - p[i] - j]);
        }
        for (int j = 0; j <= m; ++j) {
            gmax(dp[1], dp1[i - 1][j] + dp2[i + 1][m - j]);
        }
        if (dp[1] < mx) {
            cout << "A";
        } else if (dp[0] + v[i] < mx) {
            cout << "C";
        } else {
            cout << "B";
        }
    }
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    // cin >> t;
    while (t--) { solve(); }
}
```
