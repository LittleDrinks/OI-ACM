---
tags:
  - 题解
  - 贪心
aliases:
---
## [E - Simultaneous Kagamimochi](https://atcoder.jp/contests/abc388/tasks/abc388_e)

答案至多为 $\left\lfloor\dfrac{n}{2}\right\rfloor$。
将饼的序列分为前 $\left\lfloor\dfrac{n}{2}\right\rfloor$ 个和后 $\left\lceil\dfrac{n}{2}\right\rceil$ 两部分，最终一定是由前半部分作为上层饼。这是因为以前半部分作为上层饼可以达到答案上界，并且假设最终答案序列有前半部分作为下层饼的情况，后半部分至少有一块多余的饼，此时可以用后半部分的饼进行替换，答案不劣。
假设在给定的饼列后面无限地跟着无限大的饼，考虑为了将连续的 $K$ 个饼都作为上层饼来制作 $K$ 个饼，所需要的下层饼的最小索引。
定义 $B_i$ 为将第 $i$ 个饼作为上层饼时，作为下层饼可能的最小索引 $(1\leq i\leq N)$。，此时第 $i+j$ 块饼可以选择最小的下层饼下标至少为 $i+j+(B_i-i)$。
因此制作 $K$ 个镜饼至少需要前 $\displaystyle K+\max\left\lbrace\max _ {1\leq i\leq K}\lbrace B _ i-i\rbrace,K\right\rbrace$ 块饼。
在求出 $B _ i$ 的同时，从小到大检查 $K$ ，找到使得这个值不超过 $n$ 的最大 $K$ 即可。

#### [AC 代码](https://atcoder.jp/contests/abc388/submissions/61619924)

```cpp
#include <bits/stdc++.h>

using namespace std;

int main()
{
    int n; cin >> n;
    vector<int> a(n), b(n);
    for (auto &x: a) { cin >> x; }
    int ans = 0;
    for (int i=0, j=0, d=0; i < n; ++i) {
        while (j < n && a[i]*2 > a[j]) { ++j; }
        d = max(d, j-i);
        if (i+max(d,i+1) < n) { ans = i+1; }
    }
    cout << ans << "\n";
}
```
