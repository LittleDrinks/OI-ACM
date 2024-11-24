---
tags:
  - 题解
  - 滑动窗口
aliases:
  - "[ABC381D] 1122 Substring"
---
## [[ABC381D] 1122 Substring](https://www.luogu.com.cn/problem/AT_abc381_d)

对于每个偶数索引 $r$，找到最小的 $l$，使得 $A_l$ 到 $A_r$ 的子序列是一个 $1122$ 序列，如果不存在这样的 $l$，则令 $l=r+1$，表示一个空序列。不难发现，$l$ 单调递增，可以通过滑动窗口法求解。
具体实现时，可以定义 $last[a_i]$ 表示 $a_i$ 最后出现的位置，则 $l=\max(l,last[a_i]+2)$。

#### [AC代码](https://www.luogu.com.cn/record/190806770)

```cpp
#include <bits/stdc++.h>

using namespace std;

int n, ans;
vector <int> a;

void work(int s)
{
    vector <int> last(n+1,-2);
    int l=s;
    for (int i = s; i+1 <= n; i+=2) {
        if (a[i] != a[i+1]) { l=i+2; }
        else { l=max(l, last[a[i]]+2); }
        last[a[i]] = i;
        ans = max(ans, i+2-l);
    }
}

int main()
{
    cin >> n;
    a.resize(n+1);
    for (int i = 1; i <= n; ++i) { cin >> a[i]; }
    work(1);
    work(2);
    cout << ans << "\n";
}
```
