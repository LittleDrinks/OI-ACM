---
tags:
---
```cpp
struct SCC {
	int n, totdfn=0, colcnt=0;
	stack<int> stk;
	vector<int> dfn, low, col;
	vector<vector<int>> G;
	SCC(int n): n(n) {
		G.assign(n+1, {});
		dfn.assign(n+1, 0);
		low.assign(n+1, 0);
		col.assign(n+1, 0);
	}
	void add_edge(int u, int v) {
		G[u].push_back(v);
	}
	void dfs(int u) {
		stk.push(u);
		dfn[u] = low[u] = ++totdfn;
		for (auto v: G[u]) {
			if (!dfn[v]) {
				dfs(v);
				low[u] = min(low[u], low[v]);
			} else if (!col[v]) {
				low[u] = min(low[u], dfn[v]);
			}
		}
		if (low[u] == dfn[u]) {
			col[u] = ++colcnt;
			int v;
			do {
				v = stk.top(); stk.pop();
				col[v] = colcnt;
			} while(v != u);
		}
	}
	vector<int> work() {
		for (int i = 1; i <= n; ++i) {
			if (!dfn[i]) { dfs(i); }
		}
		return col;
	}
};
```
