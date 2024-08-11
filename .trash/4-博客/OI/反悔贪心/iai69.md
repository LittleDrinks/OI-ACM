---
tags:
  - 碎片
  - OI
  - OI/反悔贪心
---
## [iai69 火车](https://iai.sh.cn/problem/69)

有一个比较显然的贪心思路，就是目的地较近的乘客，优于目的地较远的乘客。

如果 $x$ 名乘客在 $i$ 下车，又有 $x$ 名乘客在 $j\ (i<j)$ 下车，当这 $x$ 名乘客在 $i$ 下车了之后，就可以在 $i$ 处留出 $x$ 个空位以供选择。

- 如果这 $x$ 个位置接着留空，结果其实选 $i$ 和选 $j$ 是一样的；
- 如果这 $x$ 个位置上有人在到达 $j$ 以前有人到达了目的地，那么选 $i$ 就赚了。

**所以说，空位必然优先留给目的地较近的乘客。**

考虑反悔贪心。

每到一站，先让目的地在这里的乘客下车，计入答案。

接下来我们可以假想，把车上剩余的未到其目的地的乘客都【赶】下车，再重新选择。

对于尚未到达目的地的乘客，无论在哪一站被【赶】下车，其结果都是没有计入答案，等价于一开始没有选择。

- 如果此时乘客数量没有超过上限，就把所有乘客再拉上车；
- 如果此时乘客数量超过上限了，就把目的地最远的那一批乘客赶下车。

实现方面，可以开一个堆记录当前车上的乘客的目的地，做懒删除。

[另外的写法](https://iai.sh.cn/contribution/283)是用 `map` 替代 `priority_queue`，既可以求出最远的车站，又方便修改到达终点站下车的那些乘客。

以下给出一种基于堆的实现。

[AC 代码提交记录](https://iai.sh.cn/submission/782825)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e5+5;
int n, s, t, c;
long long cnt[MAXN], sum, ans;
bool pushed[MAXN];

priority_queue <int> q;

int main()
{
	cin >> n >> s;
	for (int i = 1; i < n; ++i) {
		cin >> t >> c;

		// 下车
		sum -= cnt[i];
		ans += cnt[i];
		cnt[i] = 0;

        // 上车
		if (!pushed[t]) { q.push(t); pushed[t]=true; }
		cnt[t] += c;
		sum += c;
		
		// 反悔，把最远的乘客赶下车
		while (sum > s) {
			while (!cnt[q.top()]) { pushed[q.top()]=false; q.pop(); }
			long long d = min(sum-s, cnt[q.top()]);
			sum -= d;
			cnt[q.top()] -= d;
			if (cnt[q.top()] == 0) { pushed[q.top()]=false; q.pop(); }
		}
	}
	cout << ans+sum << endl;
}
```
