---
tags:
  - 碎片
  - OI
  - OI/并查集
  - OI/种类并查集
---
## [iai28 五行学说](https://iai.sh.cn/problem/28)

同 [[P2024]]，只不过这里有五类。

用 $f[x+i\times n]$ 表示 $x$ 所属的属性在图上向后 $i$ 位的那一种元素。

比如一共有 $5$ 个物品，如果第 $3$ 个物品的属性是火，在并查集中，$f[3]$ 表示火，$f[3+5]$ 表示土，$f[3+2\times5]$ 表示金，……。

由题意可知，$f[x]$ 会生 $f[x+n]$，会克 $f[x+2\times n]$。所以如果观察为 `s`，那么需要分别合并 $(\ x+n\times((i+1)\%5),\ y+n\times (i\%5)\ )$。如果观察为 `k`，那么需要分别合并 $(\ x+n\times((i+2)\%5),\ y+n\times (i\%5)\ )$。

[AC 代码提交记录](https://iai.sh.cn/submission/787622)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e5+5;
int n, m, f[MAXN*5], ans;

int find(int x)
{
	return f[x]==x? x: f[x]=find(f[x]);
}

bool check(int x, int y, int expx)
{
	for (int i = 0; i <= 4; ++i) {
		if ( (i != expx) && (find(x+n*i)==find(y)) ) { return false; }
	}
	return true;
}

void merge(int x, int y, int delta)
{
	for (int i = 0; i <= 4; ++i) {
		f[find(x+n*((i+delta)%5))] = find(y+n*i);
	}
}

int main()
{
	cin >> n >> m;
	for (int i = 1; i <= n*5; ++i) {
		f[i] = i;
	}
	for (int i = 1; i <= m; ++i) {
		char t;
		int x, y;
		cin >> t >> x >> y;
		if (t == 's') {
			if (check(x,y,1)) {
				merge(x,y,1);
			} else {
				++ans;
			}
		} else {
			if (check(x,y,2)) {
				merge(x,y,2);
			} else {
				++ans;
			}
		}
	}
	cout << ans << endl;
	return 0;
}
```
