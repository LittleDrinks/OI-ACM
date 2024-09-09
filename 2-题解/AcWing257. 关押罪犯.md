---
tags:
  - 题解
aliases:
  - AcWing257
  - 关押罪犯
speed:
---
## [257. 关押罪犯](https://www.acwing.com/problem/content/259/)

### 并查集做法



### 二分图匹配做法

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36896272/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=2e4+5, M=1e5+5;
int n, m, c[N], flag;
vector <pair<int,int>> G[N];

void dfs(int u, int cl, int lim)
{
    c[u] = cl;
    for (auto [v,w]: G[u]) {
        if (w <= lim) { continue; }
        if (!c[v])           { dfs(v, 3-cl, lim); }
        else if (c[v] == cl) { flag=0; }
    }
}

bool check(int x)
{
    flag = 1;
    memset(c, 0, sizeof(c));
    for (int i = 1; i <= n; ++i) {
        if (!c[i]) { dfs(i, 1, x); }
    }
    return flag;
}

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= m; ++i) {
        int a, b, c;
        cin >> a >> b >> c;
        G[a].push_back( {b,c} );
        G[b].push_back( {a,c} );
    }
    int L=-1, R=1e9+5, mid;
    while (L != R-1) {
        mid = (L + R) >> 1;
        if (check(mid)) {
            R = mid;
        } else {
            L = mid;
        }
    }
    cout << R << endl;
}
```
