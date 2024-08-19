---
tags:
  - 题解
  - DP
  - DP/状压DP
aliases:
  - "[ARC179B] Between B and B"
speed:
---
## [\[ARC179B\] Between B and B](https://www.luogu.com.cn/problem/AT_arc179_b)

状压 DP。
用二进制数 $S$ 表示某个数是否可选。如果某个数字 $j$ 第一次出现，或者后续出现时满足限制条件，那么 $S$ 的第 $j$ 位就是 $1$，下记 $S[j]=1$。
用 $f[i][S]$ 表示【第 $i$ 位可从 $S[j]=1$ 的数字 $j$ 中选择】的合法方案数。初状态为
$$f[0][(111\cdots)_2]=1$$
在递推的过程中，实现的结果是：根据限制条件从 $(111\cdots)_2$ 不断缩小可选范围。
考虑第 $i$ 位选择填入 $j$ 的情况 $f[i][S]$（即 $S$ 的第 $j$ 位为 $1$）。根据限制条件，我们可以从 $f[i][S]$ 向后推出可行的状态 $f[i+1][S^\prime]$。

- 首先，除非 $X_j=j$，否则第 $i$ 位和第 $i+1$ 位不能都填 $j$，可以先通过异或将 $S[j]$ 设为 $0$。
- 然后，再额外加上满足 $X_B=j$ 的所有 $B$。此处的 $B$ 可以在输入时通过 $msk[j]\overset{|}{\leftarrow}B$ 预处理得到位或一下即可。在这一步中，如果 $X_j=j$，那么 $j$ 会被重新加入选择。

状态转移方程为：
$$
f[i+1][S\oplus(1<<(j-1))|mask[j]] \overset{+}{\leftarrow} f[i][S]
$$

复盘一下。在设计状压 DP 的状态时，所选用的二进制数需要**在有限的复杂度内求出转移方向**，因此像这里将【可选范围】作为状态的设计方法就是比较常见的。

#### [AC 代码](https://www.luogu.com.cn/record/173546683)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXM=11, MAXN=1e4+5, MOD=998244353;
int m, n, msk[MAXM], f[MAXN][1<<MAXM], ans;

int main()
{
	cin >> m >> n;
	for (int i = 1; i <= m; ++i) {
		int x;
		cin >> x;
		msk[x] |= 1<<(i-1);
	}
	f[0][(1<<m)-1] = 1;
	for (int i = 0; i < n; ++i) {
		for (int j = 1; j <= m; ++j) {
			for (int k = 0; k < 1<<m; ++k) {
				if (k>>(j-1)&1) {
					int &res = f[i+1][(k ^ (1<<(j-1))) | msk[j]];
					res += f[i][k];
					res %= MOD;
				}
			}
		}
	}
	for (int i = 0; i < 1<<m; ++i) {
		ans = (ans + f[n][i]) % MOD;
	}
	cout << ans << endl;
}
```
