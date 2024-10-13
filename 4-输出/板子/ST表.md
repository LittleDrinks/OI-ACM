---
tags:
  - 输出
  - 模板
  - ST表
---
```cpp
const int N=1e5+5, I=20;

struct ST {
	int st[N][I+5], Log[N];
	void init()
	{
		Log[1] = 0;
		for (int i = 2; i <= n; ++i) { Log[i] = Log[i/2] + 1; }
		for (int j = 1; j <= n; ++j) {
			st[j][0] = read();
		}
		for (int i = 1; i <= I; ++i) {
			for (int j = 1; j+(1<<(i-1)) <= n; ++j) {
				st[j][i] = max( st[j][i-1], st[j+(1<<(i-1))][i-1] );
			}
		}
	}
	int query(int l, int r)
	{
		int s = Log[r-l+1];
		return max(st[l][s], st[r-(1<<s)+1][s]);
	}
} st;
```
