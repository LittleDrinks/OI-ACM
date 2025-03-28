---
tags:
---
```cpp
struct EBCC {
    int n, totdfn=0, colcnt=0;
    stack<int> stk;
    vector<pii> B;
    vector<int> dfn, low, col;
    vector<vector<pii>> G;
    EBCC(int n): n(n) {
        G.assign(n+1, {});
        dfn.assign(n+1, 0);
        low.assign(n+1, 0);
        col.assign(n+1, 0);
    }
    void add_edge(int u, int v, int id) {
        G[u].emplace_back(v, id<<1);
        G[v].emplace_back(u, id<<1|1);
    }
    void dfs(int u, int uid) {
        stk.push(u);
        dfn[u] = low[u] = ++totdfn;
        for (auto [v, vid]: G[u]) {
            if ((uid^vid) != 1) {
                if (!dfn[v]) {
                    dfs(v, vid);
                    low[u] = min(low[u], low[v]);
                    if (low[v] > dfn[u]) {  // 求桥
                        B.emplace_back(min(u,v), max(u,v));
                    }
                } else if (!col[v] && dfn[v] < dfn[u]) {
                    low[u] = min(low[u], dfn[v]);
                }
            }
        }
        if (low[u] == dfn[u]) {
            col[u] = ++colcnt;
            int v;
            do {
                v = stk.top(); stk.pop();
                col[v] = colcnt;
            } while (v != u);
        }
    }
    vector<pii> work() {  // 桥
        for (int i = 1; i <= n; ++i) {
            if (!dfn[i]) { dfs(i, 0); }
        }
        return B;
    }
	struct Graph {
	    int n;
	    vector<array<int,2>> E;
	    vector<int> siz, cnt;
	    Graph(int n): n(n) {
	        siz.assign(n+1, 0);  // EBCC 内部点的数量
	        cnt.assign(n+1, 0);  // EBCC 内部边的数量
	    }
	};
	Graph compress() {
	    Graph g(colcnt);
	    for (int i = 1; i <= n; ++i) {
	        ++g.siz[col[i]];
	        for (auto [v, vid]: G[i]) {
	            if (col[v] < col[i]) {
	                g.E.push_back( { col[i], col[v] } );
	            } else if (i < v) {
	                ++g.cnt[col[i]];
	            }
	        }
	    }
	    return g;
	}
};
```