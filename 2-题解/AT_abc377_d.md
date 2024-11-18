---
tags:
  - 题解
aliases:
  - "[ABC377D] Many Segments 2"
---
## [\[ABC377D\] Many Segments 2]()

一共只有 $M\leq2\times10^5$ 个可能的右端点。对于每个右端点 $r$，将所有 $R_i<r$ 的 $i$ 存入 $S$，这就是可能覆盖的所有区间。如果不想覆盖这些区间中的任意一个，则左端点必须满足
$$
l>\max_{i\in S}\{L_i\}
$$
将区间按照右端点排序，枚举右端点 $r$ 时维护左端点最大值即可。

#### [AC代码](https://www.luogu.com.cn/record/188473096)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=2e5+5;
typedef long long ll;
int n, m, L, R;
ll ans;
pair<int,int> p[N];

int main()
{
    cin >> n >> m;
    for (int i = 1; i <= n; ++i) {
        cin >> p[i].first >> p[i].second;
    }
    sort(p+1, p+n+1, [](auto a, auto b){
        if (a.second!=b.second) {
            return a.second<b.second;
        } else {
            return a.first>b.first;
        }
    });
    for (int i = 1; i <= n; ++i) {
        if (p[i-1].second==p[i].second) { continue; }
        while (R < p[i].second) { ans += R-L; ++R; }
        L = max(L, p[i].first);
    }
    while (R <= m) { ans += R-L; ++R; }
    cout << ans << endl;
}
```
