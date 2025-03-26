---
tags:
---
求一棵树中有多少子树，满足其中存在的每种颜色的结点个数都相同。
```cpp
const int N=2e5+5;
int n, c[N], siz[N], hvs[N];
int L[N], R[N], dfn[N], totdfn;
vector<int> G[N];

void dfs_init(int u, int fa)
{   // 预处理 dfn 和重儿子
	siz[u] = 1;
	L[u] = ++totdfn;
	dfn[totdfn] = u;
	for (int v: G[u]) {
		if (v != fa) {
			dfs_init(v, u);
			siz[u] += siz[v];
			if (hvs[u] == 0 || siz[v] > siz[hvs[u]]) {
				hvs[u] = v;
			}
		}
	}
	R[u] = totdfn;
}

void add(int u, int dt)
{
	--ccnt[cnt[c[u]]];
	cnt[c[u]] += dt;
	++ccnt[cnt[c[u]]];
}

void dfs_solve(int u, int fa, bool keep)
{
	for (auto v: G[u]) {  // 先做轻儿子，不保留
		if (v != fa && v != hvs[u]) {
			dfs_solve(v, u, false);
		}
	}
	if (hvs[u]) {  // 再做重儿子，保留
		dfs_solve(hvs[u], u, true);
	}
	for (auto v: G[u]) {  // 再把轻儿子的信息加上
		if (v != fa && v != hvs[u]) {
			for (int i = L[v]; i <= R[v]; ++i) {
				add(dfn[i], 1);
			}
		}
	}
	add(u, 1);
	ans += (cnt[c[u]] * ccnt[cnt[c[u]]] == siz[u]);
	if (keep) { return; }
	for (int i = L[u]; i <= R[u]; ++i) {
		add(dfn[i], -1);
	}
}
```
