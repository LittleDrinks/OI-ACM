---
tags:
  - 题解
aliases:
  - "[ARC052D] 9"
speed:
---
## [\[ARC052D\] 9](https://www.luogu.com.cn/problem/AT_arc052_d)

当模数 $k\geq B$ 时，直接枚举 $90$ 种可能的各位数字和的取值，然后枚举所有可能的数字进行判断。时间复杂度 $O(90\times \frac{M}{B})$.
当模数 $k<B$ 时，考虑数位 DP。将当前位数 $p$、$v=n\% k$、各位和 $s$ 和是否到达上限 $u$ 作为四个状态，每次枚举 $p-1$ 位可能的取值，通过记忆化搜索转移。时间复杂度 $O(10\times B\times 90\times 10)=O(9000B)$。此处写代码时主要参考了[这份博客](https://www.luogu.com.cn/article/b1zida77)。
最后，当 $90\times\frac{M}{B}=9000B\Rightarrow B=10000$ 时有最小复杂度。

#### [AC代码](https://atcoder.jp/contests/arc052/submissions/57440699)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const ll K=1e10, N=10, SN=90, B=10000;
ll k, m, f[N+5][B+5][SN+5][2];
int d[N+5];

void work1()
{
	ll ans = 0;
	for (ll s = 0; s <= 90; ++s) {
		for (ll x = s + (s<1? k: 0); x <= m; x += k) {
			ll ss = 0, xx = x;
			for (; xx; xx/=10) { ss = (ss + xx%10) % k; }
			if (ss == s) { ++ans; }
		}
	}
	cout << ans << endl;
}

ll dfs(int p, int v, int s, int u)
{
	if (!p) { return s && (s%k==v%k); }
	if (f[p][v][s][u] != -1) { return f[p][v][s][u]; }
	int up = u? d[p]: 9;
	ll ans = 0;
	for (int j = 0; j <= up; ++j) {
		ans += dfs(p-1, (v*10+j)%k, s+j, u&&(j==d[p]));
	}
	return f[p][v][s][u] = ans;
}

void work2()
{
	int cnt = 0;
	for (ll i=1, mm=m; mm; ++i, mm/=10, ++cnt) {
		d[i] = mm%10;
	}
	memset(f, -1, sizeof(f));
	cout << dfs(cnt, 0, 0, 1) << endl;
}

int main()
{
	cin >> k >> m;
	if (k >= B) { work1(); }
	else        { work2(); }
}
```
