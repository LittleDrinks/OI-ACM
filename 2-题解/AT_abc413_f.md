---
tags:
  - 题解
aliases:
  - No Passage
---
## [AT_abc413_f - No Passage](https://atcoder.jp/contests/abc413/tasks/abc413_f?lang=en)

当一个点可以向至少两个方向走到目标点时，Takahashi 无法被拦住，此时 Aoki 肯定会防止 Takahashi 走向距离最小的那个方向，因此次大值将会被计入答案。
从目标点向外 bfs，当某个点第二次被遍历到时将其加入队列，同时将两次遍历到时距离的较大值计入答案。

[***AC 代码***](https://atcoder.jp/contests/abc413/submissions/68259439)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

constexpr int inf = 1e9;
const int dx[] = {1, 0, -1, 0};
const int dy[] = {0, 1, 0, -1};

void solve()
{
    int n, m, k;
    cin >> n >> m >> k;

    vector vis(n+1, vector<int>(m+1));
    vector dis(n+1, vector<int>(m+1, inf));
    queue<pair<int,int>> q;
    for (int i = 1; i <= k; ++i) {
        int x, y;
        cin >> x >> y;
        dis[x][y] = 0;
        vis[x][y] = 2;
        q.emplace(x, y);
    }

    while (!q.empty()) {
        auto [x, y] = q.front(); q.pop();
        for (int d = 0; d < 4; ++d) {
            int nx = x + dx[d];
            int ny = y + dy[d];
            if (1 <= nx && nx <= n && 1 <= ny && ny <= m) {
                ++vis[nx][ny];
                if (vis[nx][ny] == 1) {
                    dis[nx][ny] = dis[x][y] + 1;
                }
                if (vis[nx][ny] == 2) {
                    dis[nx][ny] = max(dis[nx][ny], dis[x][y] + 1);
                    q.emplace(nx, ny);
                }
            }
        }
    }

    ll ans = 0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (vis[i][j] >= 2) {
                ans += dis[i][j];
            }
        }
    }
    cout << ans << "\n";
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    // cin >> t;
    while (t--) { solve(); }
}

```
