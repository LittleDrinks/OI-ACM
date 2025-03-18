---
tags:
  - 题解
  - 线段树
aliases:
---
## [AT_abc397_f - Variety Split Hard](https://atcoder.jp/contests/abc397/tasks/abc397_f)

答案由三部分构成，即 $L(i-1)+X(i,j)+R(j+1)$。考虑对于每个 $2\leq i\leq n-2$ 计算 $$\max_{j=i}^{n-1} \bigg(X(i,j)+R(j+1)\bigg)$$ 其中 $R$ 是可以通过 $O(n)$ 预处理得到的。
注意到 $X(i,j)$ 是随 $i$ 单调变化的，记位于 $p_1$ 位置上的数下一次出现在 $p_2$，当 $i$ 从 $p_1$ 移动到 $p_1+1$ 时，$X(i,i),X(i,i+1),\cdots,X(i,p_2-1)$ 都会减少 $1$。
不妨记 $t[j]=X(i,j)+R[j+1]$，$i$ 的每次右移都可以转换为对 $t$ 的 $[p_1,p_2)$ 区间减 $1$，并查询区间 $[i+1,n)$ 的最值，这个操作可以用线段树维护。

[***AC 代码***](https://atcoder.jp/contests/abc397/submissions/63878541)

```cpp
#include <bits/stdc++.h>

using namespace std;

template<typename Info, typename Lazy>
struct segmentTree {
    #define lson (p << 1)
    #define rson (p << 1 | 1)
    #define m ((l + r) >> 1)
    int n;
    vector<Info> info;
    vector<Lazy> lazy;
    void push_up(int p) {
        info[p] = info[lson] + info[rson];
    }
    void apply(int p, const Lazy& v) {
        info[p] = info[p] + v;
        lazy[p] = lazy[p] + v;
    }
    void spread_down(int p) {
        apply(lson, lazy[p]);
        apply(rson, lazy[p]);
        lazy[p] = Lazy();
    }
    segmentTree(int n): n(n), info(vector<Info>(4<<__lg(n))), lazy(vector<Lazy>(4<<__lg(n))) { }
    segmentTree(const vector<Info> &a): segmentTree(a.size()) {
        function<void(int,int,int)> build = [&](int p, int l, int r) {
            if (l == r-1) { info[p] = a[l]; return; }
            build(lson, l, m);
            build(rson, m, r);
            push_up(p);
        };
        build(1, 0, n);
    }
    void rangeApply(int p, int l, int r, int x, int y, const Lazy &v) {
        if (y <= l || r <= x) { return; }
        if (x <= l && r <= y) { apply(p, v); return; }
        spread_down(p);
        rangeApply(lson, l, m, x, y, v);
        rangeApply(rson, m, r, x, y, v);
        push_up(p);
    }
    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (y <= l || r <= x) { return Info(); }
        if (x <= l && r <= y) { return info[p]; }
        spread_down(p);
        return rangeQuery(lson, l, m, x, y) + rangeQuery(rson, m, r, x, y);
    }
    void modify(int x, int y, int k) {
        assert(x <= y);
        rangeApply(1, 0, n, x, y+1, Lazy(k));
    }
    int query(int x, int y) {
        return rangeQuery(1, 0, n, x, y+1).mx;
    }
};
struct Info {
    int mx;
    Info(int mx=0): mx(mx) { }
};
struct Lazy {
    int k;
    Lazy(int k=0): k(k) { } 
};
Info operator+ (const Info &u, const Info &v) {
    return Info(max(u.mx, v.mx));
}
Info operator+ (const Info &u, const Lazy &v) {
    return Info(u.mx + v.k);
}
Lazy operator+ (const Lazy &u, const Lazy &v) {
    return Lazy(u.k + v.k);
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
	int n; cin >> n;
    vector<int> a(n+1), L(n+1), R(n+2);
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
    }
    int mx = ranges::max(a);
    vector<vector<int>> idx(mx+1, vector<int>());
    for (int i = 1; i <= n; ++i) {
        idx[a[i]].push_back(i);
    }
    for (int i = 1; i <= n; ++i) {
        L[i] = L[i-1] + (i == idx[a[i]].front());
    }
    for (int i = n; i >= 1; --i) {
        R[i] = R[i+1] + (i == idx[a[i]].back());
    }
    segmentTree<Info, Lazy> t(n);
    for (int i = 2; i < n; ++i) {
        t.modify(i, i, R[i+1]);
    }
    for (int i = 1; i <= mx; ++i) {
        if (!idx[i].empty() && idx[i].front() < n) {
            t.modify(idx[i].front(), n-1, 1);
        }
    }
    int ans = 0;
    for (int i = 1; i <= n-2; ++i) {
        auto it = upper_bound(idx[a[i]].begin(), idx[a[i]].end(), i);
        int nxt;
        if (it == idx[a[i]].end()) { nxt = n; }
        else { nxt = *it; }
        t.modify(i, nxt-1, -1);
        ans = max(ans, L[i]+t.query(i+1, n-1));
    }
    cout << ans << "\n";
}
```
