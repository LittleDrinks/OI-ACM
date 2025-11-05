---
tags:
  - 题解
  - 贪心
aliases:
  - Octopus
---
## [AT_abc318f. Octopus](https://atcoder.jp/contests/abc318/tasks/abc318_f)

不妨设头在 $k$ 处，第 $i$ 个宝藏能在第 $j$ 次被抓住，需要满足 $k-L_j\le x_i\le k+L_j$
将两侧的 $k$ 移项得到 $-L_j\le x_i-k\le L_j$，或者写作 $|x_i-k|\le L_j$
由此可以得到一个在 $O(n\log n)$ 时间内判断一个 $k$ 是否合法的算法：将所有位置按 $|x_i-k|$ 排序，最小的宝藏用最小的腿抓，次大的用次大的抓，从小到大一一匹配即可。
从 $-L_j\le x_i-k\le L_j$ 出发思考，移项得到 $x_i-L_j \le k\le x_i + L_j$
也就是说，$\{x_i-L_j-1\}\cup \{x_i+L_j\}_{1\le i,j\le n}$ 这 $O(n^2)$ 个端点隔开的每个区间内，宝藏能被哪些腿抓到都是固定的，只需要任选一个点 check 即可。
时间复杂度 $O(n^3\log n)$

[***AC 代码***](https://atcoder.jp/contests/abc318/submissions/70706389)

```cpp
#include <bits/stdc++.h>
using ll = long long;
using db = long double;
using i128 = __int128;

void solve()
{
    int n;
    std::cin >> n;
    std::vector<ll> L(n), x(n);
    for (int i = 0; i < n; ++i) { std::cin >> x[i]; }
    for (int i = 0; i < n; ++i) { std::cin >> L[i]; }

    auto check = [&](ll k) -> bool {
        std::vector<ll> v = x;
        for (int i = 0; i < n; ++i) { v[i] = abs(v[i] - k); }
        std::sort(v.begin(), v.end());
        for (int i = 0; i < n; ++i) {
            if (L[i] < v[i]) {
                return false;
            }
        }
        return true;
    };

    ll ans = 0;
    std::vector<ll> pos;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            pos.push_back(x[i] - L[j] - 1);
            pos.push_back(x[i] + L[j]);
        }
    }
    std::sort(pos.begin(), pos.end());
    ll s = 0;
    for (int i = 1; i < int(pos.size()); ++i) {
        if (check(pos[i])) { s += pos[i] - pos[i - 1]; }
    }
    std::cout << s << "\n";
}

int main()
{
    std::ios::sync_with_stdio(0); std::cin.tie(0); std::cout.tie(0); 
    int t = 1;
    // cin >> t;
    while (t--) { solve(); }
}
```
