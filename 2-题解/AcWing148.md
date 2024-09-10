---
tags:
  - 题解
aliases:
  - AcWing148. 合并果子
  - 合并果子
  - P1090
  - "[NOIP2004 提高组] 合并果子"
  - "[USACO06NOV] Fence Repair G"
speed:
---
## [148. 合并果子](https://www.acwing.com/problem/content/150/)

贪心，每次挑最小的两堆合并，用堆维护。

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36905837/)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const int N=1e4+5;
int n;
ll ans;
priority_queue <ll, vector<ll>, greater<ll>> q;

int main()
{
    cin >> n;
    for (int i = 1; i <= n; ++i) {
        int a; cin >> a;
        q.push(a);
    }
    for (int i = 1; i < n; ++i) {
        int x1 = q.top(); q.pop();
        int x2 = q.top(); q.pop();
        ans += x1 + x2;
        q.push(x1 + x2);
    }
    cout << ans << endl;
}
```
