---
tags:
  - 题解
aliases:
  - "[ARC187B] Sum of CC"
---
## [[ARC187B] Sum of CC](https://www.luogu.com.cn/problem/AT_arc187_b)

$\displaystyle\min_{k=1}^i{a_i}>\max_{k=i+1}^n{a_i}$ 时，$a_i$ 和 $a_{i+1}$ 会被分到两个不同的连通块内。
记 $pmn(i)$ 表示 $b_i$ 中非 $-1$ 部分的前缀最小值，$pmn(0)=m$。
记 $smx(i)$ 表示 $b_i$ 中非 $-1$ 部分的后缀最大值。
记 $pc(i)$ 表示 $b$ 前 $i$ 个数中 $-1$ 的个数。
记 $sc(i)$ 表示 $b$ 最后 $i$ 个数中 $-1$ 的个数。
记 $f(i,j)$ 表示 $b$ 中前 $i$ 个数，最小值大于等于 $j$ 的方案数，则：
$$
f(i,j)=(m-j+1)^{pc(i)}\ (pmn(i)\leq j\leq m)
$$
记 $g(i,j)$ 表示 $b$ 中后 $i$ 个数，最大值小于等于 $j$ 的方案数，则：
$$
g(i,j)=j^{sc(i)}\ (1\leq j\leq smx(i))
$$
$b$ 可能的总数是 $f(n,1)=m^{pc(n)}$，每种情况的答案至少为 $1$。在此基础上，如果 $a_i$ 和 $a_{i+1}$ 被分到了两个不同的连通块内，则答案加一。考虑 $smx(i+1)=j-1$ 的情况，这种可能的方案数为 $g(i+1,j-1)-g(i+1,j-2)$，此时 $b_i$ 和 $b_{i+1}$ 被分到了两个不同的连通块的情况总数为 $f(i,j)$。推广一下，答案的总增量为：
$$
\sum_{i=1}^n\sum_{j=2}^m f(i,j)\times(g(i+1,j-1)-g(i+1,j-2))
$$

#### [AC代码](https://www.luogu.com.cn/record/190288884)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;

const int N=2005, MOD=998244353;

ll qpow(ll a, ll b)
{
    ll res=1;
    for (; b; b>>=1) {
        if (b & 1) { res = res*a%MOD; }
        a = a*a%MOD;
    }
    return res%MOD;
}

int main()
{
    int n, m;
    cin >> n >> m;
    vector <int> b(n+2,0), pmn(n+2,m), smx(n+2,0), pc(n+2,0), sc(n+2,0);
    vector <ll> gmin(m+2,0), gmax(m+2,0);
    for (int i = 1; i <= n; ++i) {
        cin >> b[i];
        pc[i] = pc[i-1] + (b[i] == -1);
        if (b[i] == -1) { pmn[i] = pmn[i-1]; }
        else            { pmn[i] = min(pmn[i-1], b[i]); }
    }
    for (int i = n; i; --i) {
        sc[i] = sc[i+1] + (b[i] == -1);
        if (b[i] == -1) { smx[i] = smx[i+1]; }
        else            { smx[i] = max(smx[i+1], b[i]); }
    }
    ll ans=0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j)        { gmin[j] = gmax[j] = 0; }
        for (int j = 1; j <= pmn[i]; ++j)   { gmin[j]=qpow(m-j+1, pc[i]); }
        for (int j = smx[i+1]; j <= m; ++j) { gmax[j]=qpow(j, sc[i+1]); }
        for (int j = 2; j <= m; ++j)        { ans = (ans + gmin[j]*(gmax[j-1]-gmax[j-2])%MOD)%MOD; }
    }
    ans = ((ans + qpow(m, pc[n])) % MOD + MOD) % MOD; 
    cout << ans << endl;
}
```