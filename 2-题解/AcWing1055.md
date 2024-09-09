---
tags:
  - 题解
  - 贪心
aliases:
  - AcWing1055. 股票买卖 II
speed:
---
## [1055. 股票买卖 II](https://www.acwing.com/problem/content/1057/)

取每段最大连续上升区间，一头一尾分别买入卖出即可。
$$
\sum_{1\leq i\leq n}\max(price[i+1]-price[i], 0)
$$

#### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/36443496/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXP=1e4+5;
int n, pre=MAXP, cur, ans;

int main()
{
	cin >> n;
	for (int i = 1; i <= n; ++i) {
		cin >> cur;
		ans += max(cur-pre, 0);
		pre = cur;
	}
	cout << ans << endl;
}
```