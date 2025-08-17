---
tags:
  - 题解
  - DP
aliases:
  - Subarray Sum Divisibility
---
## [AT_abc419_e - Subarray Sum Divisibility](https://atcoder.jp/contests/abc419/tasks/abc419_e)

假设 $a_1\sim a_L$ 已经符合条件，当区间从 $[1,L]$ 移动到 $[2,L+1]$ 时，和的变化量是 $a_{L+1}-a_1$，因此必须有
$$
a_1\equiv a_{L+1}\pmod m
$$
记 $f(i,j)$ 表示将 $i,i+L,i+2L,\ldots$ 全部调整为模 $m$ 余 $j$ 的代价。这一部分可以在 $O(nm)$ 的时间内求出。
记 $dp(i,j)$ 表示将序列调整为前 $i$ 个位置的和模 $m$ 为 $j$，且 $[kL+1,kL+i]$ 与对应位置同余的最小代价。
可以模仿背包写出状态转移方程。
$$
dp(i,s)=\min_{j+k=s}\bigg\{ dp(i-1,j)+f(i,k)\bigg\}
$$
时间复杂度 $O(nm+Lm^2)$

[***AC 代码***](https://atcoder.jp/contests/abc419/submissions/68588394)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

constexpr int inf = 0x3f3f3f3f;

void solve()
{
	int n, m, L;
	cin >> n >> m >> L;
	vector<int> a(n);
	for (auto &x: a) { cin >> x; }
	
	vector<int> dp(m, inf);
	dp[0] = 0;
	for (int i = 0; i < L; ++i) {
		vector<int> ndp(m, inf);
		for (int j = 0; j < m; ++j) {
			int cost = 0;
			for (int k = i; k < n; k += L) {
				cost += (j - a[k] + m) % m;
			}
			for (int k = 0; k < m; ++k) {
				ndp[(j+k)%m] = min(ndp[(j+k)%m], dp[k] + cost);
			}
		}
		dp = move(ndp);
	}
	
	cout << dp[0] << "\n";
}

int main()
{
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
	int t = 1;
	// cin >> t;
	while (t--) { solve(); }
}
```
