---
tags:
  - 题解
aliases:
  - SP1805
  - Largest Rectangle in a Histogram
speed:
---
## [HISTOGRA - Largest Rectangle in a Histogram](https://www.luogu.com.cn/problem/SP1805)

![[SP1805图示1]] 
显而易见的一个贪心策略是：以当前矩形的高度，尽可能地向左右延伸，如上图前五幅图所示。而第六幅图没有延伸到最右端，显然没有第一幅图来得优。
于是问题就转变为了：对每个位置，求出左侧和右侧第一个高度小于自己的矩形位置，然后更新答案。
这是单调栈的模板题。时间复杂度 $O(n)$。

一种可行的做法是：维护一个严格单调递增的栈 `s`，一旦栈顶 `h[s.top()]` 大于当前位置 `h[i]`，就说明 `s.top()` 右侧第一个比它小的矩形是 `i`。直到最终 `h[s.top()]`。
*注意：这种做法维护出的 `L,R` 在遇到一段连续 `h` 相等的情况下是不完全正确的，但在这段连续的矩阵内至少有一个点可以算出正确的答案，因此最终答案是正确的。*

[AC 代码提交记录](https://www.acwing.com/problem/content/submission/code_detail/36437772/)

```cpp
#include <bits/stdc++.h>
#define ll long long

using namespace std;

const int MAXN=1e5+5, MAXH=1e9;
int n, L[MAXN], R[MAXN];
ll h[MAXN], ans;

int main()
{
//	freopen("2.in", "r", stdin);
	while ( (cin>>n) && (n) ) {
		for (int i = 1; i <= n; ++i) { cin >> h[i]; }
		stack <int> s;
		h[n+1] = 0;
		for (int i = 1; i <= n+1; ++i) {
			while ( (!s.empty()) && (h[s.top()]>=h[i]) ) {
				R[s.top()] = i-1;
				s.pop();
			}
			L[i] = s.empty()? 1: s.top()+1;
			s.push(i);
		}
		ans = 0;
		for (int i = 1; i <= n; ++i) { ans = max(ans, h[i]*(R[i]-L[i]+1)); }
		cout << ans << endl;
	}
}
```
