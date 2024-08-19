---
tags:
  - 题解
  - 二分
aliases:
  - AcWing102
speed:
---
## [102. 最佳牛围栏](https://www.acwing.com/problem/content/104/)

前置题：[[POJ1050]]

#### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/36395666/)

```cpp
#include <bits/stdc++.h>
#define ll long long

using namespace std;

const int MAXN=1e5+5, L=1, R=2000, Z=1000;
int n, f;
ll c[MAXN], s[MAXN], m[MAXN];

bool valid(int val)
{
    for (int i = 1; i <= n; ++i) { s[i] = s[i-1] + c[i]*Z - val; }
    m[n] = s[n];
    for (int i = n-1; i; --i) { m[i] = max(m[i+1], s[i]); }
    for (int i = 1; i+f-1 <= n; ++i) {
        if (m[i+f-1]-s[i-1] >= 0) { return true; }
    }
    return false;
}

int main()
{
//  freopen("1.in", "r", stdin);
    cin >> n >> f;
    for (int i = 1; i <= n; ++i) { cin >> c[i]; }
    int l = L*Z-1, r=R*Z+1;
    while (l != r-1) {
        int mid = (l+r)>>1;
        if (valid(mid)) { l = mid; }
        else            { r = mid; }
    }
    cout << l << endl;
}
```
