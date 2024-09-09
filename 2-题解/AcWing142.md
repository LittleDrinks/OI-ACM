---
tags:
  - 题解
aliases:
  - 前缀统计
  - AcWing142. 前缀统计
speed:
---
## [142. 前缀统计](https://www.acwing.com/problem/content/144/)

Trie 模板题

#### [AC代码](https://www.acwing.com/problem/content/submission/code_detail/36868892/)

```cpp
#include <bits/stdc++.h>
typedef long long ll;

using namespace std;

const int N=1e5+5;
int n, m, tot, ch[N][26], cnt[N];

int main()
{
	cin >> n >> m;
	for (int i = 1; i <= n; ++i) {
		string s;
		cin >> s;
		int len=s.length(), now=0;
		for (int j = 0; j < len; ++j) {
			if (!ch[now][s[j]-'a']) { ch[now][s[j]-'a'] = ++tot; }
			now = ch[now][s[j]-'a'];
			if (j == len-1) { ++cnt[now]; }
		}
	}
	for (int i = 1; i <= m; ++i) {
		string s;
		cin >> s;
		int len=s.length(), now=0, ans=0;
		for (int j = 0; j < len; ++j) {
			now = ch[now][s[j]-'a'];
			ans += cnt[now];
			if (!now) { break; }
		}
		cout << ans << endl;
	}
}
```
