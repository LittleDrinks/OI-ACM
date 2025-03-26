---
tags:
  - 题解
  - DP
  - DP/状压DP
aliases:
---
## [D. A Simple Task](https://codeforces.com/problemset/problem/11/D)

这个问题与哈密尔顿回路同构，可以用状压 DP 解决。
将环视作一条链，起点是环上编号最小的那个点，且链的终点可以指回起点。记 $f(s,i)$ 表示这条链上包含了点集 $s$ 中的所有点，且以 $i$ 结尾的方案数。则
$$
f(s',j)\overset{+}{\leftarrow}f(s,i)\
\begin{cases}
s<s'\\
\mathbin{\rm{lowbit}}(s)<j\\
s[i]=1\\
s[j]=0\\
s'[i]=1\\
s'[j]=1\\
G[i][j]= 1
\end{cases}
$$
当存在一条由终点 $i$ 指向起点 $\mathbin{\rm{lowbit}}(s)$ 的路径时，将 $f(s,i)$ 累加入答案。
枚举每个状态、此时的终点和下一个终点，时间复杂度 $O(n^22^n)$。
注意，无向图上每一条边都是一个由小编号点指向大编号点的非法环，每一个合法环沿两个方向走的时候答案会被记录两次，故最终的答案为 $\dfrac{ans-m}{2}$。

[***AC 代码***](https://codeforces.com/contest/11/submission/312505336)

```cpp
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;
const int N=20;
int n, m;
ll f[1<<N][N], ans;
bool G[N][N];

int lowbit(int x) { return x&-x; }

int main()
{
	cin.tie(nullptr)->sync_with_stdio(false);
	cin >> n >> m;
	for (int i = 1; i <= m; ++i) {
		int u, v;
		cin >> u >> v;
		G[u][v] = G[v][u] = true;
	}
	for (int i = 1; i <= n; ++i) {
		f[1<<(i-1)][i] = 1;
	}
	for (int s = 0; s < (1<<n); ++s) {
		for (int i = 1; i <= n; ++i) {
			if (!f[s][i]) { continue; }
			for (int j = 1; j <= n; ++j) {
				if (!G[i][j]) { continue; }
				if ((s&-s) > 1<<(j-1)) { continue; }  // 起点不能改
				if(s>>(j-1)&1) {
					if ((s&-s) == 1<<(j-1)) {  // 走回起点
						ans += f[s][i];
					}
				} else {
					f[s|(1<<(j-1))][j] += f[s][i];
				}
			}
		}
	}
	cout << (ans-m)/2 << "\n";
}
```
