---
tags:
  - 题解
  - 数论分块
aliases:
  - Count A%B=C
---
## [AT_abc414_e - Count A%B=C](https://atcoder.jp/contests/abc414/tasks/abc414_e?lang=en)

固定三元组的任意两维，整个三元组随之固定。这里选择根据 $a,b$ 讨论。

- 当 $a\le b$ 时，$c=a$ 或 $c=0$，不符合要求。
- 当 $a> b$ 时，进一步讨论。
	- 若 $a$ 是 $b$ 的倍数，则 $a \equiv0\pmod b$，违反 $c \geq 1$ 的条件
	- 否则 $c\in [1, b-1]$，满足所有条件。

于是题目中三元组的数量就等于：满足 $a > b$ 的有序对总数 $T = \binom{N}{2} = \frac{N(N-1)}{2}$ 减去 $a$ 是 $b$ 的倍数的情况。
这里很容易给出一种 $O(n)$ 的暴力，即枚举 $b$，将答案减去 $\lfloor n/b\rfloor-1$。
这个形式可以采用数论分块优化，在 $O(\sqrt{n})$ 的时间内遍历所有 $\lfloor n/b\rfloor$ 相同的块 $[l,r]$，答案减去 $(r-l+1)\times(\lfloor n/l\rfloor-1)$ 即可。

[***AC 代码***](https://atcoder.jp/contests/abc414/submissions/68094382)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

const int MOD=998244353;
ll add(ll x, ll y) { x%=MOD; y%=MOD; return (x + y) % MOD; }
ll del(ll x, ll y) { x%=MOD; y%=MOD; return add(x, MOD-y); }
ll mul(ll x, ll y) { x%=MOD; y%=MOD; return (x * y) % MOD; }
ll qpow(ll a, ll b = MOD - 2) {
    ll res = 1;
    for (; b; b >>= 1) {
        if (b & 1) { res = mul(res, a); }
        a = mul(a, a);
    }
    return res;
}

void solve()
{
    ll n;
    cin >> n;
    ll ans = mul(mul(n, n-1), qpow(2));
    ll l=1, r=0;
    while (l <= n) {
        r = n / (n/l);
        ans = del(ans, mul(r-l+1, n/l-1));
        l = r + 1;
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
