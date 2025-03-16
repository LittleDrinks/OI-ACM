---
tags:
  - 题解
  - 数学
aliases:
---
## [AT_abc397_d - Cubes](https://atcoder.jp/contests/abc397/tasks/abc397_d)

根据立方差公式，$x^3-y^3=(x-y)(x^2+xy+y^2)$，记 $d=x-y$，则 $$x^3-y^3=d(3y^2+3dy+d^2)$$
因为 $n=d(3y^2+3dy+d^2)\geq d^3$，所以 $d\leq\sqrt[3]{n}\leq 10^6$，枚举 $d$ 然后二分查找使 $3y^2+3dy+d^2\leq n/d$ 的最大 $y$，检查这个 $y$ 是否符合条件即可。时间复杂度 $O(\sqrt[3]{n}\log n)$。

[***AC 代码***](https://atcoder.jp/contests/abc397/submissions/63830197)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    ll n; cin >> n;
    auto check = [](ll x, ll d){
        ll l=1, r=x;
        while (l < r-1) {
            __int128 m = (l + r) >> 1;
            if (3*m*m + 3*m*d + d*d <= x) { l = m; }
            else { r = m; }
        }
        return l;
    };
    for (ll d = 1; d*d*d <= n; ++d) {
        if (n % d == 0) {
            ll y = check(n/d, d);
            if (3*y*y + 3*y*d + d*d == n / d) {
                cout << y+d << " " << y << "\n"; return 0;
            }
        }
    }
    cout << "-1\n";
}
```
