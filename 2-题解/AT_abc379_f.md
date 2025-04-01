---
tags:
  - 题解
  - 栈/单调栈
aliases:
---
## [[ABC379F] Buildings 2](https://www.luogu.com.cn/problem/AT_abc379_f)

从 $L$ 向右侧可以看到哪些建筑，可以用一个单调栈维护
这些建筑中在 $R$ 右侧的必然能被 $R$ 看见，直接单调栈上二分即可。

#### [AC代码](https://www.luogu.com.cn/record/189243087)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=2e5+5;
int n, q, h[N], ans[N], st[N], sz;
vector <pair<int,int>> rq[N];

int main()
{
    cin >> n >> q;
    for (int i = 1; i <= n; ++i) { cin >> h[i]; }
    for (int i = 1; i <= q; ++i) {
        int l, r;
        cin >> l >> r;
        rq[l].push_back({r,i});
    }
    for (int i = n; i; --i) {
        for (auto [r, id]: rq[i]) {
            ans[id] = lower_bound(st+1, st+sz+1, r, greater<int>())-st-1;
        }
        while (sz>0 && h[i]>h[st[sz]]) { --sz; }
        st[++sz] = i;
    }
    for (int i = 1; i <= q; ++i) {
        cout << ans[i] << endl;
    }
}
```