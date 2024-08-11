---
tags:
  - 题解
  - 离散化
aliases:
  - AcWing103
speed:
---
## [103. 电影](https://www.acwing.com/problem/content/105/)

离散化模板题。注意一下写法：
- 数组下标从 `1` 开始
- 去重时写 `len=unique(a+1, a+n+1)-(a+1)`
- 二分时写 `lower(a+1, a+n+1, x)-a`

[AC代码提交记录](https://www.acwing.com/problem/content/submission/code_detail/36421747/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=2e5+5;
int n, m, a[MAXN], b[MAXN], c[MAXN];
int lan[MAXN*3], num_lan, cnt[MAXN*3];
int ans=1, ansb, ansc;

int get_lan_id(int x)
{
	return lower_bound(lan+1, lan+num_lan+1, x)-lan;
}

int main()
{
//	freopen("1.in", "r", stdin);
	cin >> n;
	for (int i = 1; i <= n; ++i) {
		cin >> a[i];
		lan[i] = a[i];
	}
	cin >> m;
	for (int i = 1; i <= m; ++i) {
		cin >> b[i];
		lan[n+i] = b[i];
	}
	for (int i = 1; i <= m; ++i) {
		cin >> c[i];
		lan[n+m+i] = c[i];
	}
	sort(lan+1, lan+n+2*m+1);
	num_lan = unique(lan+1, lan+n+2*m+1) - (lan+1);
	for (int i = 1; i <= n; ++i) {
		++cnt[get_lan_id(a[i])];
	}
	for (int i = 1; i <= m; ++i) {
		int cntb = cnt[get_lan_id(b[i])];
		int cntc = cnt[get_lan_id(c[i])];
		if ( (cntb>ansb) || (cntb==ansb && cntc>ansc) ) {
			ans=i, ansb=cntb, ansc=cntc;
		}
	}
	cout << ans << endl;
}
```
