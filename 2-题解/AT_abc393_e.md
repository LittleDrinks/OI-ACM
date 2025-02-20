---
tags:
  - 题解
  - 数论
aliases:
---
## [E - GCD of Subset](https://atcoder.jp/contests/abc393/tasks/abc393_e)

$[1,n]$ 所有数的因数个数和是 $O(n\log n)$ 的，可以用如下的代码全部求出：
```cpp
vector d(mx+1, vector<int>());
for (int i = 1; i <= mx; ++i) {
    for (int j = i; j <= mx; j += i) {
        d[j].push_back(i);
    }
}
```
遍历所有 $a_i$，对其因子计数器加一。由于 $\gcd(a_i,a_i)=a_i$，原问题等价于从集合中选择 $k$ 个数，再与 $a_i$ 求最大公约数。强制要求选择 $a_i$ 时可以从大到小遍历其所有因数，如果某个因数的计数器大于等于 $k$，则这个因数可以成为最大公约数。
时间复杂度 $O(n\log n+\sum d_i)$，其中 $d_i$ 为 $a_i$ 的因数个数。
注：$720720$ 是 $10^6$ 内因数最多的数字，其因数个数为 $240$。

#### [AC 代码](https://atcoder.jp/contests/abc393/submissions/62802320)

```cpp
#include <bits/stdc++.h>

using namespace std;

void solve()
{
	int n, k;
    cin >> n >> k;
    vector<int> a(n);
    for (auto &x: a) { cin >> x; }
    int mx = *max_element(a.begin(), a.end());
    vector d(mx+1, vector<int>());
    for (int i = 1; i <= mx; ++i) {
        for (int j = i; j <= mx; j += i) {
            d[j].push_back(i);
        }
    }
    vector<int> cnt(mx+1);
    for (auto x: a) {
        for (auto dv: d[x]) { ++cnt[dv]; }
    }
    for (int i = 0; i < n; ++i) {
        int ans = 1;
        for (auto dv: d[a[i]]) {
            if (cnt[dv] >= k) { ans = dv; }
        }
        cout << ans << "\n";
    }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
	int t = 1;
	while (t--) { solve(); }
}

```
