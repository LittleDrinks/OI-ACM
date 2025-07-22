---
tags:
  - 题解
  - DP/数位DP
aliases:
  - 月之迷
---
## [AcWing311. 月之谜](https://www.acwing.com/problem/content/313/)

枚举各位数字之和 $sum$，记 $f(p,S,sum,r)$ 表示填完 $[p+1,len]$，模数为 $S$，已经填的数字和为 $sum$，已经填的数字对 $S$ 取模余 $r$ 的数字个数。
记忆化搜索，记 $limit$ 表示所填数字是否到达上限。在 $f$ 中记录 $[p+1,len]$ 未达上限的数字个数。
总状态数 $O(R^3L^4)$

[***AC 代码***]()

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using db = long double;
using i128 = __int128;

const int L=10, S=9*L;
int a[L+1], sum;
ll f[L+1][S+1][S+1][S+1];

ll dfs(int p, bool limit, bool lead0, int s, int r)
{
    if (!p) { return !lead0 && s == sum && r == 0; }
    auto& now = f[p][sum][s][r];
    if (!limit && !lead0 && ~now) {
        return now;
    }
    int up = limit ? a[p] : 9;
    ll res = 0;
    for (int i = 0; i <= up; ++i) {
        ll add = dfs(p-1, limit&&(i==up), lead0&&(i==0), s+i, (r*10+i)%sum);
        res += add;
    }
    if (!limit && !lead0) {
        now = res;
    }
    return res;
}

ll query(ll x)
{
    int len = 0;
    for (; x; x /= 10) {
        a[++len] = x % 10;
    }
    ll res = 0;
    for (sum = 1; sum <= S; ++sum) {
        ll add = dfs(len, true, true, 0, 0);
        res += add;
    }
    return res;
}

int main()
{
	ios::sync_with_stdio(0); cin.tie(0); cout.tie(0); 
    memset(f, -1, sizeof(f));
    ll a, b;
	while (cin >> a >> b) {
        cout << query(b)-query(a-1) << "\n";
    }
}

```
