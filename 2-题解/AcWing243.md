---
tags:
  - 题解
aliases:
  - AcWing243. 一个简单的整数问题2
  - 一个简单的整数问题2
speed:
---
## [243. 一个简单的整数问题2](https://www.acwing.com/problem/content/244/)

用树状数组维护区间修改+区间查询的操作。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36968132/)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const int N=1e5+5;
int n, m;
ll a[N], sa[N];

struct BIT {
    ll t[N];
    int lowbit(int x) { return x&-x; }
    void modify(int x, ll d) {
        for (; x <= n; x += lowbit(x)) { t[x] += d; }
    }
    ll pre_sum(int x)
    {
        ll s = 0;
        for (; x; x -= lowbit(x)) { s += t[x]; }
        return s;
    }
} T1, T2;

ll query(int x)
{
    return (x+1)*T1.pre_sum(x) - T2.pre_sum(x);
}

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
        sa[i] = sa[i-1] + a[i];
    }
    while (m--) {
        char op; cin >> op;
        if (op == 'Q') {
            int l, r;
            cin >> l >> r;
            cout << sa[r]-sa[l-1]+query(r)-query(l-1) << endl;
        } else {
            int l, r; ll x;
            cin >> l >> r >> x;
            T1.modify(l, x);
            T1.modify(r+1, -x);
            T2.modify(l, l*x);
            T2.modify(r+1, (r+1)*(-x));
        }
    }
}
```
