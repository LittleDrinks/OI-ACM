---
tags:
  - 题解
aliases:
  - AcWing241. 楼兰图腾
  - 楼兰图腾
speed:
---
## [241. 楼兰图腾](https://www.acwing.com/problem/content/243/)

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36946872/)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const int N=2e5+5;
int n, y[N];
ll ans1, ans2, pre[N], suf[N];

struct BIT {
    int t[N];
    int lowbit(int x) { return x&-x; }
    void clear()
    {
        for (int i = 1; i <= n; ++i) { t[i] = 0; }
    }
    void modify(int x, int d)
    {
        for (; x <= n; x += lowbit(x)) {
            t[x] += d;
        }
    }
    ll query(int x)
    {
        ll res = 0;
        for (; x; x -= lowbit(x)) {
            res += t[x];
        }
        return res;
    }
} T;

void work(int idx, ll cnt[])
{
    cnt[idx] = T.query(y[idx]-1);
    T.modify(y[idx], 1);
}

int main()
{
    cin >> n;
    for (int i = 1; i <= n; ++i) { cin >> y[i]; }
    for (int i = 1; i <= n; ++i) { work(i, pre); }
    T.clear();
    for (int i = n; i; --i) { work(i, suf); }
    for (int i = 1; i <= n; ++i) {
        ans2 += pre[i]*suf[i];
        ans1 += (i-1-pre[i])*(n-i-suf[i]);
    }
    cout << ans1 << " " << ans2 << endl;
}
```
