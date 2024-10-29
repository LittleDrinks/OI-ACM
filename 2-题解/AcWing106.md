---
tags:
  - 题解
  - 堆
  - 排序
  - 堆/对顶堆
aliases:
  - AcWing106. 动态中位数
  - 动态中位数
speed:
---
## [106. 动态中位数](https://www.acwing.com/problem/content/108/)

对顶堆求动态中位数
大根堆在下，小根堆在上，小根堆里的数都比大根堆大，答案为小根堆的堆顶。

#### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/36423057/)

```cpp
#include <bits/stdc++.h>

using namespace std;

int p, id, m;

void work()
{
	cin >> id >> m;
	cout << id << " " << (m+1)/2 << endl;
	priority_queue <int> maxh;
	priority_queue <int,vector<int>,greater<int>> minh;
	int cnt = 0;
	for (int i = 1; i <= m; ++i) {
		int x;
		cin >> x;
		if (maxh.empty() || x>maxh.top()) { minh.push(x); }
		else                              { maxh.push(x); }
		while (!minh.empty() && minh.size() < (i+1)/2) { minh.push(maxh.top()); maxh.pop(); }
		while (!minh.empty() && minh.size() > (i+1)/2) { maxh.push(minh.top()); minh.pop(); }
		if (i & 1) {
			if (cnt == 10) { cout << endl; cnt = 0; }
			cout << minh.top() << " "; ++cnt;
		}
	}
	cout << endl;
}

int main()
{
//	freopen("1.in", "r", stdin);
	cin >> p;
	while (p--) { work(); }
}
```
