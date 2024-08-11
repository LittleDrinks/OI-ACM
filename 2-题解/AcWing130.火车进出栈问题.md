---
tags:
  - 题解
  - 栈
  - 卡特兰数
aliases: 
speed:
---
## [130. 火车进出栈问题](https://www.acwing.com/problem/content/132/)

四种方法：$O(2^n)$ 枚举、$O(n^2)$ 递推、$O(n^2)$ 动态规划、$O(n)$ 数学

[AC代码提交记录]()

### 递推

```cpp
#include <bits/stdc++.h>
#define ll long long

using namespace std;

const int MAXN=6e4+5;
int n;
ll f[MAXN];

ll dfs(int x)
{
	if (f[x]) { return f[x]; }
	ll ans = 0;
	for (int i = 1; i <= x; ++i) {
		ans += dfs(i-1) * dfs(x-i);
	}
	return f[x]=ans;
}

int main()
{
	cin >> n;
	f[0] = 1;
	cout << dfs(n) << endl;
}
```

