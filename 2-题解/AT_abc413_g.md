---
tags:
  - 题解
  - 并查集
aliases:
  - Big Banned Grid
---
## [AT_abc413_g - Big Banned Grid](https://atcoder.jp/contests/abc413/tasks/abc413_g?lang=en)

把八向相邻的障碍在并查集中合并在一起。
直观上来说，如果有一组障碍把左上角包住、或者把右下角包住，或者把图一刀切成两个梯形，都无法从 $(1,1)$ 走到 $(n,n)$
形式化地，对于一组障碍，维护四个状态 $S(t,d,l,r)$ 表示是否碰到上、下、左、右边界，当以下四个条件之一成立时无法从 $(1,1)$ 走到 $(n,n)$
$$
\begin{align}
S(l)\land S(r) \\
S(l)\land S(t) \\
S(t)\land S(d) \\
S(d)\land S(r)
\end{align}
$$

[***AC 代码***](https://atcoder.jp/contests/abc413/submissions/68258476)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

struct DSU {
    int n;
    vector<int> f, stat;
    DSU(int n): n(n), f(n+1), stat(n+1) {
        iota(f.begin(), f.end(), 0);
    }
    int find(int x) {
        return f[x] == x? f[x]: f[x] = find(f[x]);
    }
    bool same(int x, int y) { 
        return find(x) == find(y);
    }
    void setStat(int x, int s) {
        stat[find(x)] = s;
    }
    int getStat(int x) {
        return stat[find(x)];
    }
    void merge(int x, int y) {
        x = find(x);
        y = find(y);
        if (x == y) { return; }
        f[x] = y;
        stat[y] |= stat[x];
    }
};

void solve()
{
    int h, w, k;
    cin >> h >> w >> k;
    vector<pair<int,int>> o(k);
    for (auto& [x, y]: o) {
        cin >> x >> y;
    }

    auto v(o);
    sort(v.begin(), v.end());
    v.erase(unique(v.begin(), v.end()), v.end());

    auto find = [&](int x, int y) -> int {
        auto it = lower_bound(v.begin(), v.end(), make_pair(x, y));
        if (it == v.end() || *it != make_pair(x, y)) { return -1; }
        return it - v.begin() + 1;
    };

    DSU d(v.size());
    for (auto [x, y]: o) {
        int i = find(x, y);
        int stat = 0;
        stat |= (1 & (x==1)) << 0;
        stat |= (1 & (x==h)) << 1;
        stat |= (1 & (y==1)) << 2;
        stat |= (1 & (y==w)) << 3;
        d.setStat(i, stat);
    }

    for (auto [x, y]: o) {
        int i = find(x, y);
        for (auto dx: {-1, 0, 1}) {
            for (auto dy: {-1, 0, 1}) {
                int j = find(x+dx, y+dy);
                if (j != -1) { d.merge(i, j); }
            }
        }
    }
    for (int i = 1; i <= int(v.size()); ++i) {
        int stat = d.getStat(i);
        bool valid = false;
        valid |= (stat & 10) == 10;
        valid |= (stat & 12) == 12;
        valid |= (stat & 5) == 5;
        valid |= (stat & 3) == 3;
        if (valid) { cout << "No\n"; return; }
    }
    cout << "Yes\n";
}

int main()
{
    ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    int t = 1;
    // cin >> t;
    while (t--) { solve(); }
}

```
