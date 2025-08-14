---
tags:
  - 题解
  - DP
aliases:
  - XNOR Operation
---
## [AT_abc418_d - XNOR Operation](https://atcoder.jp/contests/abc418/tasks/abc418_d?lang=en)

思考一个字符串是优美字符串的充要条件。
对字符串进行操作的时候，我们总是希望找到操作前后的不变量。
观察操作。`10`、`01` 变成 `0` 不会改变 `0` 的数量，`11` 变成 `1` 也不会改变 `0` 的数量，`00` 变成 `1` 时 `0` 的数量会减 $2$，因此操作过程中 `0` 个数的奇偶性是不变量。有偶数个 `0` 是优美字符串的必要条件。
而只要 `0` 个数奇偶性是偶数，我们就可以先通过 `0` 将序列中的 `1` 全部删除，然后将 `0` 两两合并，最后再将 `1` 全部合并成一个 `1` 的方式构造出一个仅剩 `1` 的字符串。因此有偶数个 `0` 也是优美字符串的充分条件。
找出有偶数个 `0` 的子串个数，这一步可以通过 DP 完成。设 $dp(i,0/1)$ 表示以位置 $i$ 结尾的优美字符串的个数，转移显然。时间复杂度 $O(n)$

[***AC 代码***](https://atcoder.jp/contests/abc418/submissions/68447034)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

void solve()
{
    int n;
    string s;
    cin >> n >> s;
    vector dp(n+1, vector<ll>(2));
    ll ans = 0;
    for (int i = 1; i <= n; ++i) {
        if (s[i-1] == '1') {
            dp[i][0] = dp[i-1][0] + 1;
            dp[i][1] = dp[i-1][1];
        } else {
            dp[i][0] = dp[i-1][1];
            dp[i][1] = dp[i-1][0] + 1;
        }
        ans += dp[i][0];
    }
    cout << ans << "\n";
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    // cin >> t;
    while (t--) { solve(); }
}
```
