---
tags:
  - 题解
aliases:
---
## [\[ABC378D\] Count Simple Paths](https://www.luogu.com.cn/problem/AT_abc378_d)

直接暴力 dfs 即可。


#### [AC代码](https://www.luogu.com.cn/record/187853340)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=15, dx[]={1,0,-1,0}, dy[]={0,1,0,-1};
int n, m, k, ans;
char a[N][N];
bool vis[N][N];

void dfs(int x, int y, int cnt)
{
    if (cnt == k) {
        ++ans;
        return;
    }
    vis[x][y] = true;
    for (int d = 0; d < 4; ++d) {
        int xx=x+dx[d];
        int yy=y+dy[d];
        if (!vis[xx][yy] && a[xx][yy] == '.') { dfs(xx, yy, cnt+1); }
    }
    vis[x][y] = false;
}

int main()
{
    cin >> n >> m >> k;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) { cin >> a[i][j]; }
    }
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (a[i][j] == '#') continue; 
            memset(vis, 0, sizeof(vis));
            dfs(i, j, 0);
        }
    }
    cout << ans << endl;
}
```
