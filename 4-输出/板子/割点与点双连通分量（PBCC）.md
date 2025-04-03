---
tags: 
aliases:
---
```cpp
struct PBCC {
    int n, root, totdfn=0, colcnt=0;
    stack<int> stk;
    vector<pii> B;
    vector<int> dfn, low;
    vector<bool> ver;
    vector<vector<int>> pbcc;
    vector<vector<pii>> G;
    PBCC(int n): n(n) {
        G.assign(n+1, {});
        dfn.assign(n+1, 0);
        low.assign(n+1, 0);
        ver.assign(n+1, 0);
    }
    void add_edge(int u, int v, int id) {
        G[u].emplace_back(v, id);
        G[v].emplace_back(u, id);
    }
    void dfs(int u, int uid) {
        stk.push(u);
        int son = 0;
        dfn[u] = low[u] = ++totdfn;
        for (auto [v, vid]: G[u]) {
            if (!dfn[v]) {
                ++son;
                dfs(v, vid);
                low[u] = min(low[u], low[v]);
                if (low[v] >= dfn[u]) {
                    if (u != root) { ver[u] = true; }
                    pbcc.push_back({});
                    while (stk.top() != v) {
                        pbcc.back().push_back(stk.top()); stk.pop();
                    }
                    pbcc.back().push_back(v); stk.pop();
                    pbcc.back().push_back(u); 
                }
            } else if (uid != vid) {
                low[u] = min(low[u], dfn[v]);
            }
        }
        if (u == root) {
            if (son >= 2) { ver[u] = true; }
            if (!son) { pbcc.push_back( { u } ); }
        }
    }
    vector<int> work() {  // 求割点
        vector<int> V;
        for (int i = 1; i <= n; ++i) {
            if (!dfn[i]) { root=i; dfs(i, 0); }
            if (ver[i]) { V.push_back(i); }
        }
        return V;
    }
};
```