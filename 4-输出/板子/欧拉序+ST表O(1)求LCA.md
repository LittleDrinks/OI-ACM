---
tags:
  - 模板
---
初次访问节点 $u$ 和回溯到节点 $u$ 时记录，所产生的序列即为欧拉序。
欧拉序长度为 $2n-1$。
在欧拉序区间 $[first[u],first[v]]$ 上深度最小的节点即为 $lca(u,v)$。
```cpp
struct ST {};  // 把 vector<int> 改为 vector<pii>，用 pair 实现深度比较

int first[N], dep[N];
vector <int> G[N];
vector <pii> eular;
void dfs(int u, int fa)
{
    first[u] = eular.size();
    dep[u] = dep[fa] + 1;
    eular.push_back( {dep[u], u} );
    for (int v: G[u]) {
        if (v != fa) {
            dfs(v, u);
            eular.push_back( {dep[u], u} );
        }
    }
}

int main()
{   // 此处仅给出求 lca 部分的代码
	dfs(s, 0);
    ST st(eular);
    while (q--) {
		int u, v;
		cin >> u >> v;
		u = first[u];
		v = first[v];
		if (u > v) { swap(u, v); }
		cout << st.query(u, v).second << "\n";  // 这里需要输出节点编号
    }
}
```