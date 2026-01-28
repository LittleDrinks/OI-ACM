---
tags:
  - 题解
  - 贪心
aliases:
  - Sake or Water
---
## [AT_abc441_c. Sake or Water](https://atcoder.jp/contests/abc441/tasks/abc441_c)

最坏的情况是最大的 $k$ 杯都是水，此时从大到小第 $n-k+1$ 杯往后一定是酒。
于是贪心策略是从大往小喝。

[***AC 代码***](https://atcoder.jp/contests/abc441/submissions/72610865)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

void solve()
{
    int n, k;
    ll x;
    cin >> n >> k >> x;
    vector<ll> a(n + 1);
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    sort(a.begin() + 1, a.end(), greater<>());
    partial_sum(a.begin(), a.end(), a.begin());
    for (int i = n-k; i <= n; ++i) {
        if (a[i] - a[n-k] >= x) {
            cout << i << "\n";
            return;
        }
    }
    cout << "-1\n";
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    while (t--) { solve(); }
}

```
