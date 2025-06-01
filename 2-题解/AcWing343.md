---
tags:
  - 题解
  - 最短路/传递闭包
  - 最短路
aliases:
---
## [AcWing343. 排序](https://www.acwing.com/problem/content/description/345/)

记 $G(u,v)=1$ 表示 $u<v$，不等式的传递性等价于有向图连通性，可以通过 Floyd 传递闭包维护。
每新增一条边，跑一遍 Floyd 传递闭包。
如果发现 $G(u,v)=G(v,u)=1$，说明发现矛盾。
如果对于每个点 $u$ 都满足 $\sum \bigg(G(u,v)+G(v,u)\bigg)=n-1$，说明已经能够确定相对顺序，按照 $\sum G(u,v)$ 从大到小排序即可。

[***AC 代码***](https://www.acwing.com/problem/content/submission/code_detail/41825133/)

```cpp
#include <bits/stdc++.h>
using namespace std;
void work(int n, int m)
{
    vector<bitset<26>> G(26);
    vector<string> r(m);
    for (auto& s: r) { cin >> s; }

    for (int i = 1; i <= m; ++i) {
        auto s = r[i-1];
        int u = s[0] - 'A';
        int v = s[2] - 'A';
        if (s[1] == '>') {
            swap(u, v);
        }
        G[u][v] = 1;
        
        for (int v = 0; v < n; ++v) {
            for (int u = 0; u < n; ++u) {
                if (G[u][v]) { G[u] |= G[v]; }
            }
        }

        for (int u = 0; u < n; ++u) {
            for (int v = 0; v < n; ++v) {
                if (G[u][v] && G[v][u]) {
                    cout << "Inconsistency found after " << i << " relations.\n";
                    return;
                }
            }
        }
        
        bool valid = true;
        vector<pair<int,int>> ans;
        for (int u = 0; u < n; ++u) {
            int cnt = 0;
            for (int v = 0; v < n; ++v) { cnt += G[u][v]; }
            ans.emplace_back(cnt, u);
            for (int v = 0; v < n; ++v) { cnt += G[v][u]; }
            if (cnt != n-1) { valid = false; break; }
        }
        if (valid) {
            sort(ans.begin(), ans.end(), greater<>());
            cout << "Sorted sequence determined after " << i << " relations: ";
            for (auto [t, p]: ans) {
                cout << (char)('A'+p);
            }
            cout << ".\n";
            return;
        }
    }
    cout << "Sorted sequence cannot be determined.\n";
}
int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    int n, m;
    while (cin >> n >> m) {
        if (n!=0 || m!=0) {
            work(n, m);
        }
    } 
}
```
