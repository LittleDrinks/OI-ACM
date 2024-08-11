---
tags:
  - 碎片
  - OI/分块
---
## [Acwing252 磁力块](https://www.acwing.com/problem/content/252/)

每一块磁石都可以将在范围内、并且质量不大于它的磁石吸到身边。视磁石质量和磁力不同，可能有的磁石磁力大但是质量小，吸引不了质量大的磁石，反之亦然。因此，防止多维度的最优解被忽略，在极限情况下每块磁石都要被考虑一次，复杂度 $O(n)$。吸到后的磁石就不必再次考虑，可以使用队列维护，开始时，将磁石 $L$ 放入队列。对于剩下的磁石，需要找到一种较优复杂度的做法，快速把一块磁石能吸引的全部加入队列之中。

于是问题转化为了二维偏序问题。

首先，第一想法肯定是通过排序消除一个维度，比如选择根据距离进行排序。此时想要判断磁力的相对关系，只需要一次二分即可。但此时第二个维度质量就变得非常无序，只能暴力扫描，复杂度 $O(n)$，爆了。

为了平衡两个维度的查询时间，我们可以考虑分块。将剩下的磁石按照距离排序，排完序后分块，**块内按照质量排序**。每次去除队列开头的石头 $x$，设其在第 $belong[x]$ 块中。

若磁石在 $belong[x]$ 左侧的块中，距离符合条件，块内找质量满足条件的磁石即可。该步可以维护一个指针 $l\_id$，表示磁石最左侧还未被取走的磁石，再维护一个 $taken[]$，如果符合条件就右移指针，然后在 $taken[]$ 中标记。

若磁石在 $belong[x]$ 右侧的块中，距离不符合条件，不用考虑。在第 $belong[x]$ 块内，直接遍历每一个磁石看是否符合条件。

预处理时先按照距离排序，再在块内按照质量排序。每次用新的磁石进行吸引时，首先需要二分找到块的位置，然后每个块内从左往右将符合条件的磁石加入队列中。块长设置为 $\sqrt{n}$，时间复杂度够用。

[AC 代码提交记录](https://www.acwing.com/problem/content/submission/code_detail/22796093/)

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=250005, MAXK=505;
long long x_0, y_0, ans, dis_max[MAXK];
int n, k, sz, L[MAXK], R[MAXK], belong[MAXN], l_ms_id[MAXK];
queue <long long> q;
bool taken[MAXN];

struct magnet_stone {
	long long dis, m, p, r;
} ms[MAXN];

long long dis2(long long x, long long y)
{
	return (x-x_0)*(x-x_0)+(y-y_0)*(y-y_0);
}

bool cmp_dis(magnet_stone a, magnet_stone b)
{
	return a.dis < b.dis;
}

bool cmp_m(magnet_stone a, magnet_stone b)
{
	return a.m < b.m;
}

void init()
{
	// 分块信息
	k=sqrt(n), sz=n/k;
	for (int i = 1; i <= sz; ++i) {
		L[i]=(i-1)*k+1, R[i]=i*k;
	}
	if (R[sz] < n) {
		++sz, L[sz]=R[sz-1]+1, R[sz]=n;
	}
	for (int i = 1; i <= sz; ++i) {
		l_ms_id[i] = L[i];
		for (int j = L[i]; j <= R[i]; ++j) {
			belong[j] = i;
		}
	}
	
	// 按照距离对ms[]进行排序
	sort(ms+1, ms+n+1, cmp_dis);
	
	// 块内按照质量排序
	for (int i = 1; i <= sz; ++i) {
		dis_max[i] = ms[R[i]].dis;  // 块内距离最大值，用于二分
		sort(ms+L[i], ms+R[i]+1, cmp_m);
	}
}

void attract(int curr)
{
	// 先按照距离，找到最大值严格大于ms[curr].r*ms[curr].r的块x
	int x=upper_bound(dis_max+1, dis_max+sz+1, ms[curr].r*ms[curr].r)-(dis_max);
	// x = (x==0? 1: x);
	
	// 在块x的左侧，距离都符合条件，直接看质量
	for (int i = 1; i < x; ++i) {
		while (l_ms_id[i] <= R[i]) {
			if (taken[l_ms_id[i]]) {  // 已经吸上来了，跳过
				++l_ms_id[i];
			} else if (ms[l_ms_id[i]].m <= ms[curr].p) {  // 符合条件，吸上来
				++ans, taken[l_ms_id[i]]=true, q.push(l_ms_id[i]), ++l_ms_id[i];
			} else {  // 质量不符合条件，向右质量递增，也不可能符合条件，跳出循环
				break;
			}
		}
	}
	
	// 如果x超出了块的范围，直接跳出
	if (x > sz) { return; }
	
	// 对于块x，扫描其中每一个磁石，判断是否符合条件
	for (int i = l_ms_id[x]; i <= R[x]; ++i) {
		if (taken[i]) {  // 已经吸上来了，跳过
			continue;
		} else if ( (ms[i].m<=ms[curr].p) && (ms[i].dis<=ms[curr].r*ms[curr].r) ) {  // 符合条件，吸上来
			++ans, taken[i]=true, q.push(i);
		}
	}
}

int main()
{
	cin.tie(nullptr) -> sync_with_stdio(false);

	// I.N.
	cin >> x_0 >> y_0 >> ms[0].p >> ms[0].r >> n; q.push(0);
	for (int i = 1; i <= n; ++i) {
		long long x,y; cin>>x>>y; ms[i].dis=dis2(x,y);  // 输入距离
		cin >> ms[i].m >> ms[i].p >> ms[i].r;           // 输入其他量
	}
	
	// 预处理分块信息
	init();
	
	// 类bfs吸引磁石
	while (!q.empty()) {
		int curr=q.front(); q.pop();
		attract(curr);
	}
	
	// E.D.
	cout << ans << endl;
	return 0;
}
```