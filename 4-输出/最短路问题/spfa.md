---
tags:
---
一句话：求从 $s$ 出发到其余各点的最短路，每次选一个点 $u$，对到其子节点 $v$ 的路径 $s\rightarrow v$ 进行松弛。spfa 是 Bellman-ford 算法的队列实现版本。

如果 $s\rightarrow v$ 的路径经过 $u$ 中转后边权变小，那么对于 $v$ 的子节点 $x$，路径 $s \rightarrow x$ 也可能会松弛成功，所以每次成功松弛一个节点后，将其所有子节点加入队列（已经在队列中就不用重复加了）。

```cpp
void spfa()
{
	queue<int> q;
	for (int i = 1; i <= n; ++i) {
		dis[i] = inf;
		vis[i] = 0;
	}
	q.push(s); dis[s]=0; vis[s]=true;
	while (!q.empty()) {
		int u=q.front(); q.pop(); vis[u]=0;
		for (int i = head[u]; i; i=e[i].nxt) {
			int v = e[i].to;
			if (dis[v] > dis[u]+e[i].val) {
				dis[v] = dis[u]+e[i].val;
				if (!vis[v]) {
					vis[v] = true;
					q.push(v);
				}
			}
		}
	}
	for (int i = 1; i <= n; ++i) {
		cout << dis[i] << " ";
	}
} 
```
