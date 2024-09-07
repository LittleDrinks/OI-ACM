---
tags:
  - 题解
aliases:
  - AcWing144
  - 最长异或值路径
speed:
---
## [144. 最长异或值路径](https://www.acwing.com/problem/content/146/)

前置题：[[AcWing143. 最大异或对|最大异或对]]

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36870653/)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const int N=1e5+5, I=31;
int n, sx[N];
vector <pair<int,int>> G[N];

struct Trie {
	int ch[N*I][2], tot;
	void insert(int x)
	{
		int now = 0;
		for (int i = I; i >= 0; --i) {
			int b = x>>i&1;
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

void dfs(int u, int fa)
{
	for (auto [v,w]: G[u]) {
		if (v != fa) {
			sx[v] = sx[u] ^ w;
			dfs(v, u);
		}
	}
}

int main()
{
	cin >> n;
	for (int i = 1; i < n; ++i) {
		int u, v, w;
		cin >> u >> v >> w;
		++u, ++v;
		G[u].push_back( {v,w} );
		G[v].push_back( {u,w} );
	}
	dfs(1, 0);
	for (int i = 2; i <= n; ++i) { T.insert(sx[i]); }
	ll ans = 0;
	for (int i = 1; i <= n; ++i) {
		ans = max(ans, T.query(sx[i]));
	}
	cout << ans << endl;
}
```
