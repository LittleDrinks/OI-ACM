---
tags:
  - 题解
aliases:
  - AcWing 242. 一个简单的整数问题
  - 一个简单的整数问题
speed:
---
## [242. 一个简单的整数问题](https://www.acwing.com/problem/content/248/)

同 [[P3368|【模板】树状数组 2]]

#### [AC代码]()

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=1e5+5;
int n, m, a[N];

struct BIT {
    int t[N];
    int lowbit(int x) { return x&-x; }
    void modify(int x, int d)
    {
        for (; x <= n; x += lowbit(x)) {
            t[x] += d;
        }
    }
    int query(int x)
    {
        int res = 0;
        for (; x; x -= lowbit(x)) {
            res += t[x];
        }
        return res;
    }
} T;

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        cin >> a[i];
        T.modify(i, a[i]-a[i-1]);
    }
    while (m--) {
        char op; cin >> op;
        if (op == 'Q') {
            int x; cin >> x;
            cout << T.query(x) << endl;
        } else {
            int l, r, d;
            cin >> l >> r >> d;
            T.modify(l, d);
            T.modify(r+1, -d);
        }
    }
}
```
