---
tags:
  - 题解
aliases:
  - AcWing143
  - 最大异或对
speed:
---
## [143. 最大异或对](https://www.acwing.com/problem/content/145/)

求 $\displaystyle \max_x \{x\oplus y\}$，由于高位的位权大于所有低位之和，据此贪心即可。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36869478/)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const int I=31, N=1e5+5;
int n, a[N];

struct Trie {
	int tot=0, ch[N*I][2];
	void insert(int x)
	{
		int now = 0;
		for (int i = I; i >= 0; --i) {
			int b = (x>>i)&1;
			if (!ch[now][b]) { ch[now][b] = ++tot; }
			now = ch[now][b];
		}
	}
	
	ll query(int x)
	{
		int now=0;
		ll ans=0;
		for (int i = I; i >= 0; --i) {
			int b = x>>i&1;
			if (ch[now][b^1]) {
				ans = ans*2+1;
				now = ch[now][b^1];
			} else {
				ans = ans*2;
				now = ch[now][b];
			}
		}
		return ans;
	}
} T;

int main()
{
	cin >> n;
	ll ans = 0;
	for (int i = 1; i <= n; ++i) {
		cin >> a[i];
		T.insert(a[i]);
	}
	for (int i = 1; i <= n; ++i) {
		ans = max(ans, T.query(a[i]));
	}
	cout << ans << endl;
}
```
