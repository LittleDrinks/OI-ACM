---
tags:
  - 模板
  - 欧拉图
---
有向图中欧拉通路存在条件：起点出度比入度大 $1$，终点入度比出度大 $1$，其余点入度等于出度。
有向图中欧拉回路存在条件：所有点入度等于出度。
```cpp
void dfs(int u)
{   // Hierholzer 算法
    for (auto [v, id]: G[u]) {
        if (!vis[id]) {
            vis[id] = true;
            dfs(v);
        }
    }
    ans.push_back(u);
}
```