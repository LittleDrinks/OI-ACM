---
tags:
  - 题解
  - 树状数组
aliases:
---
## [F - Prefix LIS Query](https://atcoder.jp/contests/abc393/tasks/abc393_f)

可以用树状数组在 $O(n\log n)$ 内求出 LIS。
离线所有询问，不难发现询问 $(r,x)$ 就是在问区间 $[1,r]$ 上以不大于 $x$ 的数作为结尾的 LIS 长度。

#### [AC 代码](https://atcoder.jp/contests/abc393/submissions/62787094)

```cpp
#include <bits/stdc++.h>

using namespace std;

struct BIT {
    int n;
    vector<int> t;
    BIT(int n): n(n) { t.assign(n+1, 0); }
    int lowbit(int x) { return x&-x; }
    void modify(int x, int d) {
        for (; x <= n; x+=lowbit(x)) { t[x] = max(t[x], d); }
    }
    int query(int x) {
        int res = 0;
        for (; x; x -= lowbit(x)) { res = max(res, t[x]); }
        return res;
    }
};

void solve()
{
	int n, q;
    cin >> n >> q;
    vector<int> a(n), b, ans(q);
    vector<array<int,3>> qu(q);
    for (auto &x: a) { cin >> x; b.push_back(x); }
    for (int i = 0; i < q; ++i) {
        cin >> qu[i][0] >> qu[i][1];
        qu[i][2] = i;
        b.push_back(qu[i][1]);
    }
    sort(qu.begin(), qu.end());
    sort(b.begin(), b.end());
    b.erase(unique(b.begin(), b.end()), b.end());
    BIT t(b.size());
    int now = 0;
    for (auto [r, x, id]: qu) {
        while (now < r) {
            int pos = lower_bound(b.begin(), b.end(), a[now])-b.begin()+1;
            int res = 1;
            if (pos-1) { res = t.query(pos-1) + 1; }
            t.modify(pos, res);
            ++now;
        }
        int ask = lower_bound(b.begin(), b.end(), x)-b.begin()+1;
        ans[id] = t.query(ask);
    }
    for (auto x: ans) { cout << x << "\n"; }
}

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
	int t = 1;
	while (t--) { solve(); }
}
```
