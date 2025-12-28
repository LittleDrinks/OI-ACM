---
tags:
  - 题解
  - 倍增
aliases:
  - Heavy Buckets
---
## [AT_abc438_e. Heavy Buckets](https://atcoder.jp/contests/abc438/tasks/abc438_e)

想到倍增直接就是模板题
$O(1)$ 超级难写，$O(\log n)$ 超级好写

[***AC 代码***](https://atcoder.jp/contests/abc438/submissions/72049241)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

const int I = 35;

void solve()
{
    int n, q;
    cin >> n >> q;
    vector dp(I, vector<ll>(n + 1));
    vector to(I, vector<int>(n + 1));
    iota(dp[0].begin(), dp[0].end(), 0);
    for (int i = 1; i <= n; ++i) {
        cin >> to[0][i];
    }
    for (int i = 1; i < I; ++i) {
        for (int j = 1; j <= n; ++j) {
            to[i][j] = to[i-1][ to[i-1][j] ];
            dp[i][j] = dp[i-1][j] + dp[i-1][ to[i-1][j] ];
        }
    }
    while (q--) {
        ll t, b;
        cin >> t >> b;
        ll ans = 0;
        for (int i = I - 1; i >= 0; --i) {
            if (t >> i & 1) {
                ans += dp[i][b];
                b = to[i][b];
            }
        }
        cout << ans << "\n";
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
