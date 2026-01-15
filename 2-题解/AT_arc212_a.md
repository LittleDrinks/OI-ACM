---
tags:
  - 题解
aliases:
---
## [AT_arc212_a. Four TSP](https://atcoder.jp/contests/arc212/tasks/arc212_a)

一共 $\dfrac{P(4,4)}{4}$ 种路径，每种路径的长度都是 4，缺失的两条路径有三种情况：

- $(1,2)(3,4)$
- $(1,3)(2,4)$
- $(1,4)(2,3)$ 

不妨设每部分的权值和为 $A,B,C$，由于每条边的权值都在 $[1,X-1]$ 之间，共有 $(A-1)(B-1)(C-1)$ 种这种图，对应的贡献为 $k-\max(A,B,C)$
直接暴力枚举即可，时间复杂度 $O(k^2)$

[***AC 代码***](https://atcoder.jp/contests/arc212/submissions/72472860)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

const int MOD = 998244353;
ll add(ll x, ll y) { return (x + y) % MOD; }
ll mul(ll x, ll y) { return (x * y) % MOD; }

void solve()
{
    int k;
    cin >> k;
    ll ans = 0;
    for (int A = 2; A <= k; ++A) {
        for (int B = 2; A + B <= k; ++B) {
            int C = k - A - B;
            if (C < 2) { continue; }
            ll f = A - 1;
            f = mul(f, B - 1);
            f = mul(f, C - 1);
            ans = add(ans, mul(f, k-max({A,B,C})));
        }
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
