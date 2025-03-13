---
tags:
  - 题解
  - 并查集
aliases:
---
## [[ABC238E] Range Sums](https://www.luogu.com.cn/problem/AT_abc238_e)

建图，$l-1$ 和 $r$ 连边，$\begin{cases}w(l-1,r)=\sum_{i=l}^ra_i\\w(r,l-1)=-\sum_{i=l}^ra_i\end{cases}$，$[x,y]$ 的区间和等价于图上 $x-1,y$ 两点间路径。
显然，如果 $x-1,y$ 在一个连通块内，则 $[x,y]$ 可求。

[***AC 代码***](https://atcoder.jp/contests/abc238/submissions/63697392)

```cpp
#include <bits/stdc++.h>

using namespace std;

struct dsu {
    int n;
    vector<int> f;
    dsu(int n): n(n), f(vector<int>(n+1)) {
        iota(f.begin(), f.end(), 0);
    }
    int find(int x) {
        return f[x]==x? f[x]: f[x]=find(f[x]);
    }
    bool same(int x, int y) {
        return find(x) == find(y);
    }
    void merge(int x, int y) {
        x = find(x);
        y = find(y);
        f[x] = y;
    }
};

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
    int n, q;
    cin >> n >> q;
    dsu d(n);
    for (int i = 1; i <= q; ++i) {
        int l, r;
        cin >> l >> r;
        d.merge(l-1, r);
    }
    cout << (d.same(0,n)?"Yes":"No") << "\n";
}

```
