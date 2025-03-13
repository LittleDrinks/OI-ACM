---
tags:
  - 模板
---
初次访问节点 $u$ 和回溯到节点 $u$ 时记录，所产生的序列即为欧拉序。
欧拉序长度为 $2n-1$。
在欧拉序区间 $[first[u],first[v]]$ 上深度最小的节点即为 $lca(u,v)$。
```cpp
// 见 ST 表板子
// 用 pair 实现深度比较，比较函数为 min
template <typename T>
struct ST {};

int first[N], dep[N];
vector <int> G[N];
vector <pii> eular;
void dfs(int u, int fa)
{   // 预处理深度和欧拉序
    first[u] = eular.size();
    dep[u] = dep[fa] + 1;
    eular.push_back( {dep[u], u } );
    for (int v: G[u]) {
        if (v != fa) {
            dfs(v, u);
            eular.push_back( {dep[u], u } );
        }
    }
}

ST<pii> st;
int lca(int u, int v)
{
    u = first[u];
    v = first[v];
    if (u > v) { swap(u, v); }
    return st.query(u, v).second;
}

int main()
{   
	// 预处理欧拉序，构建 ST 表，之后就可以直接调用 lca 进行求解
	dfs(s, 0);
    st = ST<pii>(eular);
}
```
