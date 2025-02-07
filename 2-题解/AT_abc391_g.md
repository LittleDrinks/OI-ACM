---
tags:
  - 题解
  - DP/DP套DP
  - DP
  - DP/状压DP
aliases:
---
## [[ABC391G] Many LCS](https://www.luogu.com.cn/problem/AT_abc391_g)

本题为 DP 套 DP 模板题，内层为 LCS，外层为状压 DP。

> [!note] LCS
> 记 $dp(i,j)$ 表示 $LCS(s[1:i],t[1:j])$，则
> $$
> dp(i,j)=\max\begin{cases}
> dp(i,j-1)\\
> dp(i-1,j)\\
> dp(i-1,j-1)+[s_i=t_j]\\
> \end{cases}
> $$

将上文的 $t$ 视作长度为 $m$ 的待求字符串。当模式串 $S$ 枚举到 $i$ 时，如果我们已经知道 $dp(i-1)$ 和 $t_j$，我们就可以通过上文的递推式求出 $dp(i)$。
所谓 DP 套 DP，就是在外层 DP 中记录内层 DP 过程中的方案数，或者说通过内层 DP 计算外层 DP 应该如何转移。具体而言，记 $f(i,dp(i-1))$ 表示与 $S[1:i]$ 匹配结果为 $dp(i-1)$ 的 $t$ 的数量。则有递推式：
$$
f(i+1,dp(i))\overset{+}{\leftarrow}f(i,dp(i))
$$
其中，$t_j$ 共有 $|\sum|=26$ 种取值，$dp(i-1)$ 是一个长度为 $n$ 的数组，并且 $dp(i-1)$ 的差分数组只会有 $0,1$ 两种取值，通过枚举差分数组可以枚举出所有 $2^n$ 种可能的 $dp(i-1)$，总状态数为 $O(n\cdot2^n)$，实现时可以对 $dp(i-1)$ 的差分数组进行状压，也可以直接使用 `array<int>` 和 `map`。外层单次转移需要枚举 $t_j$ 并通过内层 DP 由 $dp(i-1)$ 推得 $dp(i)$，复杂度为 $O(m|\sum|)$。总时间复杂度为 $O(nm|\sum|2^n)$。
记 $popcount(dp)$ 表示 $dp$ 数组中 $1$ 的数量，最终 $LCS=i$ 的方案数为：
$$\sum_{popcount(dp(n-1))=i}f(n,dp(n-1))$$


#### [AC 代码](https://www.luogu.com.cn/record/201883903)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MOD=998244353;
void add(int &x, int y) { if ((x+=y) >= MOD) { x -= MOD; } }

int main()
{   
    cin.tie(nullptr)->sync_with_stdio(false);
    int n, m; string s;
    cin >> n >> m >> s;
    vector f(2, vector<int>(1<<n));
    vector dp(2, vector<int>(n+1));
    f[0][0] = 1;
    for (int i = 1; i <= m; ++i) {
        f[i%2].assign(1<<n, 0);
        for (char ch = 'a'; ch <= 'z'; ++ch) {
            for (int j = 0; j < (1<<n); ++j) {
            	// 将状态转回 dp[i-1]
                dp[0][0] = 0;
                for (int k = 1; k <= n; ++k) { dp[0][k] = dp[0][k-1] + (j>>(k-1)&1); }
                // 内层 DP
                dp[1][0] = 0;
                for (int k = 1; k <= n; ++k) {
                    dp[1][k] = max(dp[1][k-1], dp[0][k]);
                    if (ch == s[k-1]) { dp[1][k] = max(dp[1][k], dp[0][k-1]+1); }
                }
                // 对 dp[i] 进行状压
                int msk = 0;
                for (int i = n; i; --i) { msk = msk * 2 + (dp[1][i]-dp[1][i-1]); }
                // 外层 DP 转移
                add(f[i%2][msk], f[(i-1)%2][j]);
            }
        }
    }
    vector<int> res(n+1);
    for (int i = 0; i < (1<<n); ++i) { add(res[__builtin_popcount(i)], f[m%2][i]); }
    for (int i = 0; i <= n; ++i) { cout << res[i] << " \n"[i==n]; }
}
```
