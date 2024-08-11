---
tags:
  - 题解
  - 排序
  - 贪心
aliases:
  - AcWing104
speed:
---
## [104. 货仓选址](https://www.acwing.com/problem/content/106/)

设货仓左侧有 $p$ 家商店，右侧有 $q$ 家商店
每次货仓右移 $1$ 个单位，距离之和都会增加 $p-q$
即当 $p<q$ 时，应该右移；当 $p>q$ 时，应该左移；当 $p=q$ 时，不移动
为保证左侧与右侧商店数相同，应该选取中位数作为选址
设有n家商店，从小到大排序
若n是奇数，那么应该放在 $x[(n+1)/2]$
若n是偶数，那么选址坐标 $x[n/2] <= x <= x[1+n/2]$

[AC代码提交记录](https://www.acwing.com/problem/content/submission/code_detail/36422131/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=100005;
int n, a[MAXN], ans;

int main()
{
	cin >> n;
	for (int i = 1; i <= n; ++i) { cin >> a[i]; }
	sort(a+1, a+n+1);
	for (int i = 1; i <= n; ++i) { ans += abs(a[i]-a[(n+1)/2]); }
	cout << ans << endl;
}
```

