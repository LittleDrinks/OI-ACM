---
tags:
  - 输出
  - 模板
  - ST表
---
```cpp
struct ST {
	int n, I=0;
	vector<vector<int>> st;
	vector <int> Log;
	ST(int n, vector<int> a): n(n) {
        while ((1<<I) < n) { ++I; }
		Log.resize(n+1);
		Log[1] = 0;
		for (int i = 2; i <= n; ++i) { Log[i] = Log[i/2] + 1; }
		st.resize(n+1, vector<int>(I+5, 0));
		for (int j = 1; j <= n; ++j) { st[j][0]=a[j]; }
		for (int i = 1; i <= I; ++i) {
			for (int j = 1; j+(1<<(i-1))<=n; ++j) {
				st[j][i] = max(st[j][i-1], st[j+(1<<(i-1))][i-1]);
			}
		}
	}
	int query(int l, int r)
	{
		int s = Log[r-l+1];
		return max(st[l][s], st[r-(1<<s)+1][s]);
	}
};
```
