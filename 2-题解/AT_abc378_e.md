---
tags:
  - 题解
  - 前缀和
  - 逆序对
  - 树状数组
aliases:
---
## [\[ABC378E\] Mod Sigma Problem](https://www.luogu.com.cn/problem/AT_abc378_e)

题目中的 $\displaystyle\sum_{l \leq i \leq r} A_i$ 可以转换为前缀和 $s_r-s_{l-1}$ 的形式，即
$$
 \sum_{1 \leq l \leq r \leq N} \left( \left(s_r-s_{l-1}\right) \mathbin{\mathrm{mod}} M \right)
$$
令 $s_i\leftarrow s_i\mathbin{\rm{mod}} M$，对于一个固定的右端点 $r$，该点对答案的贡献为
$$
\begin{align}
& \sum_{l=1}^r 
\begin{cases}
s_r-s_{l-1} & s_r\geq s_{l-1}\\
s_r-s_{l-1}+M & s_r<s_{l-1}
\end{cases}
\\
&=r\times s_r-\sum_{l=1}^r (s_{l-1}) + M\times \sum_{l=1}^r[s_r<s_{l-1}]
\end{align}
$$
其中，$r\times s_r$ 和 $\sum_{l=1}^r (s_{l-1})$ 两部分可以分别通过前缀和、前缀和的前缀和求出，而 $\sum_{l=1}^r[s_r<s_{l-1}]$ 等价于求逆序对，可以用树状数组求出。

#### [AC代码](https://www.luogu.com.cn/record/187890842)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int N=2e5+5;
int n;
ll m, a[N], s, ss, ans;

struct BIT {
    int t[N];
    int lowbit(int x) { return x&-x; }
    void modify(int x) {
        for (; x <= m; x+=lowbit(x)) { ++t[x]; }
    }
    ll query(int x) {
        ll res = 0;
        for (; x; x-=lowbit(x)) { res += t[x]; }
        return res;
    }
} T;

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    T.modify(1);
    for (int i = 1; i <= n; ++i) {
        s = (s + a[i]) % m;
        ans += s*i + (i-T.query(s+1))*m - ss;
        T.modify(s+1);
        ss += s;
    }
    cout << ans << endl;
}
```