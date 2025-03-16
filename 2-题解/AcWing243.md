---
tags:
  - 题解
  - 树状数组
aliases:
  - AcWing243. 一个简单的整数问题2
  - 一个简单的整数问题2
speed:
---
## [243. 一个简单的整数问题2](https://www.acwing.com/problem/content/244/)

用树状数组维护区间修改+区间查询的操作。
记 $a$ 的差分数组为 $d$，$a_i=\displaystyle\sum_{j=1}^id_j$，把前缀和转化为对差分数组求和：
$$
\begin{array}l
d_1 & d_2 & d_3 & \cdots & d_n\\
\color{red}{d_1} & d_2 & d_3 & \cdots & d_n\\
\color{red}{d_1} & \color{red}{d_2}  & d_3 & \cdots & d_n\\
\cdots\\
\color{red}{d_1} & \color{red}{d_2}  & \color{red}{d_3} & \color{red}{\cdots} & \color{red}{d_n}\\
\end{array}
$$
红色部分是实际的答案，将问题转化为整个矩阵减去黑色部分，即：
$$
S_n=(r-l+1)\times\sum_{i=1}^nd_i-\sum_{i=1}^{n}id_i
$$
使用两个树状数组，一个维护 $d_i$，一个维护 $id_i$。

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
