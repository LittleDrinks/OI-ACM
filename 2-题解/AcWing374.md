---
tags:
  - 题解
aliases:
  - AcWing374. 导弹防御塔
  - 导弹防御塔
speed:
---
## [374. 导弹防御塔](https://www.acwing.com/problem/content/376/)

二分图多重匹配问题。
显然，若 $t$ 时间内可以消灭所有的怪，那么更长的时间也可以。二分答案，对于每一个时间上限 $mid$，做一次二分图匹配。炮弹可能发射多次，但怪只有一个，而且怪的总数量也更少，因此选择以怪作为左图，炮弹作为右图。
第 $i$ 个炮台的第 $j$ 发炮弹打死第 $u$ 只怪的最小时间为
$$
j\times\frac{t_1}{60} + (j-1)\times t_2 + \frac{dis(i,u)}{v}
$$
注意 $t_1$ 的单位是秒。
只要这个时间小于等于 $mid$，那么这发炮弹就可以消灭这只怪物，即图上存在一条这样的边。
由于最劣情况下一个炮台也能把所有怪全部消灭，因此最多只需要考虑 $m$ 发炮弹，右图节点数 $O(NM)$，至多存在 $O(NM^2)$ 条边，时间复杂度 $O(NM^3\log T)$.

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36927851/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int N=55, TEST=0;
const double eps=1e-7;
int n, m;
int ax[N], ay[N], bx[N], by[N], match[N][N];
double t1, t2, v, L, R, mid, md;
bool vis[N][N];

double dis(int x, int y)
{
    return sqrt(
        (ax[y]-bx[x]) * (ax[y]-bx[x])
        + (ay[y]-by[x]) * (ay[y]-by[x])
    );
}

bool dfs(int u)
{
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (!vis[i][j] && j*t1/60+(j-1)*t2+dis(i,u)/v <= mid) {
                vis[i][j] = true;
                if (!match[i][j] || dfs(match[i][j])) {
                    match[i][j] = u; return true;
                }
            }
        } 
    }
    return false;
}

bool check()
{
    int ans = 0;
    memset(match, 0, sizeof(match));
    for (int i = 1; i <= m; ++i) {
        memset(vis, 0, sizeof(vis));
        ans += dfs(i);
    }
    return ans==m;
}

int main()
{
    cin >> n >> m >> t1 >> t2 >> v;
    for (int i = 1; i <= m; ++i) { cin >> ax[i] >> ay[i]; }
    for (int i = 1; i <= n; ++i) { cin >> bx[i] >> by[i]; }
    L=0, R=1e9;
    while (R-L >= eps) {
        mid = (L+R)/2;
        bool f = check();
        if (f) { R=mid; }
        else   { L=mid; }
        if (TEST) { cout << mid << " " << f << endl; }
    }
    cout << fixed << setprecision(6) << L << endl;
}
```
