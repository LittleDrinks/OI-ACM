```cpp
int n, m;
vector<pii> G[N];
int kruskal()
{   // 默认是一张连通图
	vector <array<int,3>> e(m);
	for (auto& [w, u, v]: e) { cin >> u >> v >> w; }
	sort(e.begin(), e.end());
	int ans = 0, cnt = 0;
	d = dsu(n);  // 见并查集模板
	for (auto& [w, u, v]: e) {
		if (d.find(u) != d.find(v)) {
			d.merge(u, v);
			ans += w;
			++cnt;
			// 以下两行为建图，完成后 G 中存放了最小生成树
			G[u].push_back( { v, w } );
			G[v].push_back( { u, w } );
		}
		if (cnt == n-1) { break; }
	}
	return ans;
}
```