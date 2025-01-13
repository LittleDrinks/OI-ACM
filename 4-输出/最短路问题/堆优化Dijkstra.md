---
tags:
---
一句话：求从 $s$ 出发到其余各点的最短路，每次从尚未选过的节点中，选取权值和最小的路径 $s\rightarrow u$，用 $u$ 松弛其所有的子节点 $v$。

由于每次选的是全局最小，在没有负权边的情况下，以 $u$ 作为中转点对 $s\rightarrow v$ 进行一次松弛，路径权值和肯定是在 $s\rightarrow u$ 的基础上加上一个正权值 $w_{u,v}$，因此每次选择的点 $x$，路径 $s\rightarrow x$ 的权值和总是递增的。

因此以 $u$ 作为中转点的时候，$s\rightarrow u$ 的路径必然是权值和最小的那一条，保证了正确性。同时，再使用 $s\rightarrow u$ 作为中转点进行松弛也不会有效果，所以每个点只需要做一次中转点就可以了。

用优先队列（已经实现好的堆）维护全局权值和最小的路径，同时标记已经做过中转点的点，$m$ 次入堆，$n$ 次出堆，时间复杂度 $O((n+m)\log n)$

```cpp
void heap_dijkstra()
{
	priority_queue< pair<int, int>, vector<pair<int, int> >, greater<pair<int, int> > >q;
	for (int i = 1; i <=n; ++i) {
		dis[i] = inf;
	}
	dis[s]=0;
	q.push(make_pair(0, s));
	while (!q.empty()) {
		int u=q.top().second; q.pop();
		if (vis[u]) continue;
		vis[u]=true;
		for (int i = head[u]; i; i=e[i].nxt) {
			int v = e[i].to;
			if (dis[v] > dis[u]+e[i].val) {
				dis[v] = dis[u]+e[i].val;
				if (!vis[v]) q.push(make_pair(dis[v], v)); 
			}
		}
	}
	for (int i = 1; i <= n; ++i) {
		cout << dis[i] << " ";
	}
}
```
