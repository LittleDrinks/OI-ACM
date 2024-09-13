---
tags:
  - 题解
aliases:
  - AcWing244. 谜一样的牛
  - 谜一样的牛
speed:
---
## [244. 谜一样的牛](https://www.acwing.com/problem/content/245/)

建立权值数组 $t[i]=0/1$ 表示排位为 $i$ 的牛是否已被找到。记第 $i$ 头牛的排位为 $rk[i]$。
从后往前考虑，某头牛的排位 $rk[i]$ 满足：
$$
\begin{cases}
t[rk[i]] = 1 \\
\sum_{k=1}^{rk[i]}t[k] = a[i]
\end{cases}
$$
每次找到这样的牛，将 $t[rk[i]]$ 设为 $0$，继续考虑前一头牛即可。
每次在树状数组上倍增即可在 $O(\log n)$ 的时间内完成单次查询，总时间复杂度 $O(n\log n)$。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36965625/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=1e5+5, I=30, TEST=0;
int n, a[N], rk[N];

struct BIT {
    int t[N];
    int lowbit(int x) { return x&-x; }
    void modify(int x, int d)
    {
        for (; x <= n; x += lowbit(x)) {
            t[x] += d;
        }
    }
    int sum(int x)
    {
        int res = 0;
        for (; x; x -= lowbit(x)) { res += t[x]; }
        return res;
    }
    int query(int x)
    {
        ++x;
        int ans=0, sum=0;
        for (int i = I; i >= 0; --i) {
            if (ans+(1<<i) <= n && sum+t[ans+(1<<i)] < x) {
                ans += (1<<i);
                sum += t[ans];
            }
        }
        return ans + 1;
    }
} T;

int main()
{
    cin >> n;
    a[1] = 0;
    for (int i = 2; i <= n; ++i) { cin >> a[i]; }
    for (int i = 1; i <= n; ++i) { T.modify(i, 1); }
    for (int i = n; i; --i) {
        T.query(9);
        rk[i] = T.query(a[i]);
        T.modify(rk[i], -1);
        if (TEST) {
            cout << "DEBUG : ";
            for (int j = 1; j <= n; ++j) { cout << T.sum(j)-T.sum(j-1) << " "; }
            cout << endl;
            cout << "DEBUG : ";
            for (int j = 1; j <= n; ++j) { cout << rk[j] << " "; }
            cout << endl << endl;
        }
    }
    for (int i = 1; i <= n; ++i) {
        cout << rk[i] << endl;
    }
}
```
