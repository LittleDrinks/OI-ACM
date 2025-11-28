---
tags:
  - 题解
aliases:
---
## [AT_abc433_E. Max Matrix 2](https://atcoder.jp/contests/abc433/tasks/abc433_e)

每个位置唯一的限制是 $a_{i,j}\le \min(X_i,Y_j)$，
倒着填。有三种情况。

- 如果 $v$ 在 $X,Y$ 中同时出现了，那么它的位置唯一确定
- 如果 $v$ 在 $X,Y$ 中只出现了一次，那么行或列固定了，随便找一个符合条件的都可以
- 否则，随便找一个填上

由于是倒着做的，合法的坑位只会越来越多，所以后两种情况随便填就行了。

[***AC 代码***](https://atcoder.jp/contests/abc433/submissions/71174165)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

void solve()
{
    int n, m;
    cin >> n >> m;

    vector<int> Xmin(n), Ymin(m);
    vector<int> Xpos(n * m + 1, -1), Ypos(n * m + 1, -1);
    for (int i = 0; i < n; ++i) { cin >> Xmin[i]; Xpos[Xmin[i]] = i; }
    for (int i = 0; i < m; ++i) { cin >> Ymin[i]; Ypos[Ymin[i]] = i; }

    if (set<int>(Xmin.begin(), Xmin.end()).size() != n) { cout << "No\n"; return; }
    if (set<int>(Ymin.begin(), Ymin.end()).size() != m) { cout << "No\n"; return; }

    vector pos(n * m + 1, vector<pair<int,int>>());
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            pos[min(Xmin[i], Ymin[j])].emplace_back(i, j);
        }
    }

    vector a(n, vector<int>(m));
    queue<pair<int,int>> q;
    for (int v = n * m; v; --v) {
        int tx, ty;
        if (Xpos[v] == -1 && Ypos[v] == -1) {
            if (q.empty()) { cout << "No\n"; return; }
            auto [x, y] = q.front();
            q.pop();
            tx = x;
            ty = y;
        } else if (Xpos[v] == -1 || Ypos[v] == -1) {
            if (pos[v].empty()) { cout << "No\n"; return; }
            auto [x, y] = pos[v].back();
            pos[v].pop_back();
            tx = x;
            ty = y;
        } else {
            a[Xpos[v]][Ypos[v]] = v;
            tx = Xpos[v];
            ty = Ypos[v];
        }

        a[tx][ty] = v;
        for (auto &[x, y]: pos[v]) {
            if (x != tx || y != ty) {
                q.emplace(x, y);
            }
        }
    }

    cout << "Yes\n";
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            cout << a[i][j] << " \n"[j + 1 == m];
        }
    }
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    cin >> t;
    while (t--) { solve(); }
}
```
