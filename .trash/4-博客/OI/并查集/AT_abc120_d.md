---
tags:
  - 碎片
  - OI
  - OI/图
  - OI/并查集
---
## [\[ABC120D\] Decayed Bridges](https://www.luogu.com.cn/problem/AT_abc120_d)

用并查集维护所有的连通块，$x$ 所在连通块的大小记作 $sz_x$。每次把 $x$ 和 $y$ 断开时：

- 如果 $x$ 和 $y$ 在同一个连通块内，答案不变；
- 否则，可以从第 $x$ 和第 $y$ 个连通块内分别挑出一个点 $i,j$，满足 $D(i,j)=1$，计入答案。

反向观察所有的删边操作。对于 $n$ 个点的图，在一开始一条边都没有的情况下，$ans=C_n^2$。每次把删的边加回去，并且如果一条边两个端点 $x$ 和 $y$ 不在一个连通块内，答案减去 $sz_x\times sz_y$。

注意在计算 `ans` 的时候要开 `long long`，$n$ 或 $sz$ 如果用 `int` 存，计算时要先乘上 `1LL`。

[AC 代码提交记录](https://www.luogu.com.cn/record/128972896)

```cpp
#include <bits/stdc++.h>
#define ll long long

using namespace std;

const int MAXN=1e5+5;
int n, m, fr[MAXN], to[MAXN], f[MAXN], sz[MAXN];
ll tmp, ans[MAXN];

int find(int x)
{
	return f[x]==x? x: f[x]=find(f[x]);
}

int main()
{
	cin >> n >> m;
	for (int i = 1; i <= m; ++i) {
		cin >> fr[i] >> to[i];
	}
	for (int i = 1; i <= n; ++i) {
		f[i] = i;
		sz[i] = 1;
	}
	tmp = 1LL*n*(n-1)/2;
	for (int i = m; i; --i) {
		ans[i] = tmp;
		int fu=find(fr[i]), fv=find(to[i]);
		if (fu != fv) {
			tmp -= 1LL * sz[fv] * sz[fu];
			sz[fv] += sz[fu];
			sz[fu] = 0;
			f[fu] = fv;
		}
	}
	for (int i = 1; i <= m; ++i) {
		cout << ans[i] << endl;
	}
	return 0;
}
```
