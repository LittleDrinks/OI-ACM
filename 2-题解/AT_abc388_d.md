---
tags:
  - 题解
  - BIT
aliases:
---
## [D - Coming of Age Celebration](https://atcoder.jp/contests/abc388/tasks/abc388_d)

考虑 pull 和 push。
考虑每个人受到前方哪些人送的石头，等价于考虑每个人会送给后方哪些人石头。当前 $i-1$ 个人的石头都送完后，第 $i$ 个人有多少石头自然就能求出来了。
不难发现，push 型的操作可以转化为区间修改+单点查询。具体而言，如果一个人有 $x$ 块石头，它会给区间 $[i+1,\min(i+x, n)]$ 上每个人都送一块石头。
用树状数组维护即可。

#### [AC 代码](https://atcoder.jp/contests/abc388/submissions/61573187)

```cpp
#include <bits/stdc++.h>

using namespace std;

struct BIT {
    int n; 
    vector<int> t;
    BIT(int n): n(n) { t.assign(n+1, 0); }
    int lowbit(int x) {
        return x&-x;
    }
    void modify(int x, int d) {
        for (; x <= n; x += lowbit(x)) { t[x] += d; }
    } 
    int query(int x) {
        int res = 0;
        for (; x; x -= lowbit(x)) { res += t[x]; }
        return res;
    }
};

int main()
{
    int n; cin >> n;
    BIT t(n);
    vector<int> a(n);
    for (auto &x: a) { cin >> x; }
    for (int i = 1; i <= n; ++i) {
        int now = a[i-1] + t.query(i);
        if (i+1 <= n) { t.modify(i+1, 1); }
        int p = min(i+now, n) + 1;
        if (p <= n) { t.modify(p, -1); }
        a[i-1] = now - (p-i-1);
    }
    for (auto &x: a) { cout << x << " "; }
}
```
