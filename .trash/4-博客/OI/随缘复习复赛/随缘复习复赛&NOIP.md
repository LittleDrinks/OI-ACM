---
tags:
  - 博客
  - OI
---
明天就要下午考复赛了，突然慌起来了，随缘复习下板子，顺带记录下考试策略和注意事项。

update：2023-11-17，明天考 NOIP 了，再掏出来复习一下。

# 祖传板子

附一份[别人的模板总结](https://www.luogu.com.cn/blog/lijunxi2009/mu-ban-zong-jie)

## 并查集

见[这篇](https://www.cnblogs.com/LittleDrinks/p/17763322.html)。

```cpp
int f[MAXN];

int find(int x)
{
	return f[x]==x? x: f[x]=find(f[x]);
}

void merge(int x, int y)
{
	f[find(x)] = find(y);
}
```

## 堆

见[这篇](https://www.cnblogs.com/LittleDrinks/p/17737926.html)。

## 分块

见[这篇](https://www.cnblogs.com/LittleDrinks/p/17839701.html)。

## 线段树
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e5+5;
int n, m;
long long a[MAXN];

struct segment_tree
{
	int l, r;
	long long sum, tag;
} t[MAXN<<3];

void push_up(int pos)
{
	int lson=pos*2, rson=pos*2+1;
	t[pos].sum = t[lson].sum + t[rson].sum;
}

void spread_down(int pos)
{
	int lson=pos*2, rson=pos*2+1;
	t[lson].tag += t[pos].tag;
	t[lson].sum += t[pos].tag * (t[lson].r-t[lson].l+1);
	t[rson].tag += t[pos].tag;
	t[rson].sum += t[pos].tag * (t[rson].r-t[rson].l+1);
	t[pos].tag = 0;
}

void build(int pos, int l, int r)
{
	t[pos].l = l;
	t[pos].r = r;
	if (l == r) {
		t[pos].sum = a[l];
		return;
	}
	int lson=pos*2, rson=pos*2+1, mid=(l+r)>>1;
	build(lson, l, mid);
	build(rson, mid+1, r);
	push_up(pos);
}

void modify(int pos, int l, int r, long long delta)
{
	if ( (l <= t[pos].l) && (t[pos].r <= r) ) {
		t[pos].tag += delta;
		t[pos].sum += delta * (t[pos].r - t[pos].l + 1);
		return;
	}
	spread_down(pos);
	int lson=pos*2, rson=pos*2+1, mid=(t[pos].l+t[pos].r)>>1;
	if (l <= mid) {
		modify(lson, l, r, delta);
	}
	if (mid+1 <= r) {
		modify(rson, l, r, delta);
	}
	push_up(pos);
}

long long query(int pos, int l, int r)
{
	spread_down(pos);
	if ( (l <= t[pos].l) && (t[pos].r <= r) ) {
		return t[pos].sum;	
	}
	int lson=pos*2, rson=pos*2+1, mid=(t[pos].l+t[pos].r)>>1;
	long long sum = 0;
	if (l <= mid) {
		sum += query(lson, l, r);
	}
	if (mid+1 <= r) {
		sum += query(rson, l, r);
	}
	return sum;
}

int main()
{
	cin.tie(nullptr) -> sync_with_stdio(false);
	cin >> n >> m;
	for (int i = 1; i <= n; ++i) {
		cin >> a[i];
	}
	build(1, 1, n);
	for (int i = 1; i <= m; ++i) {
		int op, x, y;
		cin >> op >> x >> y;
		if (op == 1) {
			long long k;
			cin >> k;
			modify(1, x, y, k);
		} else {
			cout << query(1, x, y) << "\n";
		}
	}
	return 0;
}
```

## 平衡树

见[这篇](https://www.cnblogs.com/LittleDrinks/p/17629948.html)。

## 链式前向星
```cpp
const int MAXN=1e5+5;
int head[MAXN], cnt;

struct edge {
	int v, nxt;
} e[MAXN<<1];

void add_edge(int u, int v)
{
	e[++cnt] = { v, head[u] };
	head[u] = cnt;
}
```

## 邻接表
```cpp
const int MAXN=1e5+5;
int n, m;
int head[MAXN], fr[MAXN], to[MAXN], val[MAXN], nxt[MAXN];

void build()
{
	memset(head, 0, sizeof(head));
	for (int i = 1; i <= m; ++i) {
		nxt[i] = head[fr[i]];
		head[fr[i]] = i;
	}
}

int main()
{
	cin >> n >> m;
	for (int i = 1; i <= m; ++i) { cin >> fr[i] >> to[i] >> val[i]; }
	build();
}
```

## lca
```cpp
int get_LCA(int u, int v)
{
	if (depth[u] < depth[v]) { swap(u,v); }
	for (int i = MAXI; i >= 0; --i) {
		if (depth[f[u][i]] >= depth[v]) { u=f[u][i]; }
	}
	if (u == v) { return u; }
	for (int i = MAXI; i >= 0; --i) {
		if (f[u][i] != f[v][i]) {
			u = f[u][i];
			v = f[v][i];
		}
	}
	return f[u][0];
}
```

## 最小生成树
```cpp
const int MAXN=5005;
int n, m, f[MAXN], cnt;
long long ans;

struct node {
	int u, v;
	long long val;
	bool operator < (const node &a) const
	{
		return val > a.val;
	}
};

priority_queue <node> q;

int find(int x)
{
	return f[x]==x? x: f[x]=find(f[x]);
}

int main()
{
	for (int i = 1; i <= n; ++i) {
		f[i] = i;
	}
	for (int i = 1; i < n; ++i) {
		while ( (!q.empty()) && (find(q.top().u)==find(q.top().v)) ) { q.pop(); }
		if (!q.empty()) {
			f[find(q.top().u)] = find(q.top().v);
			ans += q.top().val;
			++cnt;
			q.pop();
		}
	}
}
```

## 最短路相关

见[这篇](https://www.cnblogs.com/LittleDrinks/p/17592593.html)

## KMP

见[这篇](https://www.cnblogs.com/LittleDrinks/p/17647076.html)。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e6+5;
int n, m, nxt[MAXN];
string s1, s2;

int main()
{
	cin >> s1 >> s2;
	n = s1.length();
	s1 = " " + s1;
	m = s2.length();
	s2 = " " + s2;
	for (int i=2, j=0; i <= m; ++i) {
		while ( (j) && (s2[i] != s2[j+1]) ) { j=nxt[j]; }
		if (s2[i] == s2[j+1]) { ++j; }
		nxt[i] = j;
	}
	for (int i=1, j=0; i <= n; ++i) {
		while ( (j) && (s1[i] != s2[j+1]) ) { j=nxt[j]; }
		if (s1[i] == s2[j+1]) { ++j; }
		if (j == m) { cout << i-m+1 << "\n"; }
	}
	for (int i = 1; i <= m; ++i) {
		cout << nxt[i] << " ";
	}
	return 0;
}
```

## 关于对拍

见 [oi-wiki](https://oi-wiki.org/contest/common-tricks/#%E5%AF%B9%E6%8B%8D)

随机数记得加 `srand(time(nullptr))`。

拍子的模板：

```cpp
#include <bits/stdc++.h>

using namespace std;

int main()
{
	while (true) {
		system("gen > test.in");
		system("test1.exe < test.in > a.out");
		system("test2.exe < test.in > b.out");
		if (system("fc a.out b.out")) {
			system("pause");
			return 0;
		}
	}
}
```

# 如何骗分

这里就要掏出祖传的[骗分导论](https://www.luogu.com.cn/blog/jerrycyx/how-to-get-more-scores)了。

除此之外，再记录几道我自己之前碰到的题目。

## [P8867 [NOIP2022] 建造军营](https://www.luogu.com.cn/problem/P8867)

爆搜的 15 分可以方便拿下，两层 dfs 不要写炸就行

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=5e5+5, MAXM=1e6+5, MOD=1e9+7, BF_M=35;
int n, m, ans, fa[MAXN];
bitset <BF_M> ple, plp;
vector < pair<int,int> > e;

int find(int x)
{
    return fa[x]==x? x: fa[x]=find(fa[x]);
}

bool check()
{
    // 枚举边摧毁
    for (int dse = 0; dse < m; ++dse) {
        // 清空并查集
        for (int i = 1; i <= n; ++i) { fa[i] = i; }

        // 按照当前方案将能连通的城市合并
        for (int i = 0; i < m; ++i) {
            if ( (i==dse) && (!ple[i]) ) { continue; }
            fa[find(e[i].first)] = find(e[i].second);
        }

        // 找到第一个军营
        int fst;
        for (int i = 1; i <= n; ++i) {
            if (plp[i-1]) { fst=i; break; }
        }

        // 检查军营之间是否相互连通
        for (int i = 1; i <= n; ++i) {
            if ( (plp[i-1]) && (find(i)!=find(fst)) ) { return false; }
        }
    }
    return true;
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);

    // I.N.
    cin >> n >> m;
    for (int i = 0; i < m; ++i) {
        int u, v; cin >> u >> v;
        e.push_back(make_pair(u, v));
    }

    // 枚举
    for (int i = 0; i < (1<<m); ple=(++i)) {
        plp = 0;
        for (int j = 0; j < (1<<n); plp=(++j)) {
            if ( (plp.any()) && (check())) { ++ans; }
        }
    }
    cout << ans << endl;

    // E.D.
    return 0;
}
```

## [P8866 [NOIP2022] 喵了个喵](https://www.luogu.com.cn/problem/P8866)

一道构造题，初见会很吓人，碰都不想碰。

但观察数据范围：

|   测试点    |  $T=$  |    $n$     |  $k=$  | $m \leq$ |
| :---------: | :----: | :--------: | :----: | :------: |
|  $1\sim 3$  | $1001$ | $\leq 300$ | $2n-2$ |  无限制  |
|  $4\sim 6$  | $1002$ |    $=2$    | $2n-1$ |  无限制  |
| $7\sim 10$  |  $3$   |    $=3$    | $2n-1$ |   $14$   |
| $11\sim 14$ | $1004$ |    $=3$    | $2n-1$ |  无限制  |
| $15\sim 20$ | $1005$ | $\leq 300$ | $2n-1$ |  无限制  |

可以看到，在第 1~3 个点处，$n$ 反常得大，而且 $k=2n-2$ 与其他值不同，于是可以猜测，当 $k=2n-2$ 的时候必然有一个非常简单的构造策略。这里可以拿下 15 分。

然后观察到第 7~10 个点有 $m\leq14$，于是可以 dfs 枚举，又可以拿下 20 分。

之后 $m$ 的范围都比较大，大概率是和正解思考过程相关，但前面的 35 分可以无脑拿下，就是写着有点烦。还有注意，输出量过大，用 `endl` 可能会超时，要换成 `"\n"`。

附上丑陋的部分分代码。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e5+5, MAXK=605;
int t, n, m, k;
deque <int> hp[MAXK];
vector <string> ans;

void in()
{
    ans.clear();
    cin >> n >> m >> k;
    hp[0].clear();
    for (int i = 1; i <= m; ++i) {
        int a; cin >> a;
        hp[0].push_back(a);
    }
}

namespace case1
{
    void solve()
    {
        while (t--) {
            in();

            // 把图案为i的卡牌放到第(i+1)/2个牌堆
            for (int i = 0; i < m; ++i) {
                int tar_idx = (hp[0][i]+1)/2;
                if (hp[tar_idx].empty()) {  // 目标牌堆现在没有牌
                    ans.push_back("1 "+to_string(tar_idx));
                    hp[tar_idx].push_back(hp[0][i]);
                } else if (hp[tar_idx].back() == hp[0][i]) {  // 放到牌顶可以消
                    ans.push_back("1 "+to_string(tar_idx));
                    hp[tar_idx].pop_back();
                } else if (hp[tar_idx].front() == hp[0][i]) {  // 放到辅助堆，可以把底下消掉
                    ans.push_back("1 "+to_string(n));
                    ans.push_back("2 "+to_string(tar_idx)+" "+to_string(n));
                    hp[tar_idx].pop_front();
                } else {  // 放到牌堆顶或牌堆底都没法消，直接丢进去就行
                    ans.push_back("1 "+to_string(tar_idx));
                    hp[tar_idx].push_back(hp[0][i]);
                }
            }

            // 输出答案
            cout << ans.size() << "\n";
            for (string progress: ans) { cout << progress << "\n"; }
        }
    }
}

namespace case3
{
    const int MAXM=14;
    int mi[MAXM+5];

    bool check(int pl)
    {
        for (int i=0; i < m; ++i) {
            // 加入目标牌堆
            int tar_idx = (pl%3) + 1;
            ans.push_back("1 "+to_string(tar_idx));

            // 看能不能消
            if ((!hp[tar_idx].empty()) && (hp[tar_idx].back() == hp[0][i])) {  // 可以在牌堆顶消
                hp[tar_idx].pop_back();
            } else {
                bool f = false;
                for (int j = 1; j <= 3; ++j) {  // 可以在牌堆底消
                    if (j == tar_idx) { continue; }
                    if ( (!hp[j].empty()) && (hp[j].front() == (hp[tar_idx].empty()? hp[0][i]: hp[tar_idx].front())) ) {
                        ans.push_back("2 "+to_string(tar_idx)+" "+to_string(j));
                        hp[j].pop_front();
                        f=true; break;
                    }
                }
                if (!f) { hp[tar_idx].push_back(hp[0][i]); }
            }

            // 三进制代替dfs
            pl /= 3;
        }

        // 看是否所有牌堆都被清空
        for (int i = 1; i <= n; ++i) {
            if (!hp[i].empty()) { return false; }
        }
        return true;
    }

    void solve()
    {
        // 预处理3的幂
        mi[0] = 1;
        for (int i = 1; i <= MAXM; ++i) {
            mi[i] = 3 * mi[i-1];
        }

        while (t--) {
            in();

            // 一共3个栈，枚举每一张牌放在哪里，O(m)验证
            for (int i = 0; i < mi[m]; ++i) {
                ans.clear(); hp[1].clear(); hp[2].clear(); hp[3].clear();
                if (check(i)) {
                    cout << ans.size() << "\n";
                    for (string progress: ans) { cout << progress << "\n"; }
                    break;
                }
            }
        }
    }
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);
    // freopen("meow3.in", "r", stdin);

    // I.N.
    cin >> t;
    switch (t % 10) {
        case 1: case1::solve(); break;
        case 3: case3::solve(); break;
    }

    // E.D.
    return 0;
}
```

## [P8820 [CSP-S 2022] 数据传输](https://www.luogu.com.cn/problem/P8820)

这道题一看就是很复杂的图论，没什么思路，依然是观察数据范围：

|    测试点    |      $n \le$      |      $Q \le$      | $k =$ | 特殊性质 |
| :----------: | :---------------: | :---------------: | :---: | :------: |
|     $1$      |       $10$        |       $10$        |  $2$  |    是    |
|     $2$      |       $10$        |       $10$        |  $3$  |    是    |
|     $3$      |       $200$       |       $200$       |  $2$  |    是    |
|  $4 \sim 5$  |       $200$       |       $200$       |  $3$  |    是    |
|  $6 \sim 7$  |      $2000$       |      $2000$       |  $1$  |    否    |
|  $8 \sim 9$  |      $2000$       |      $2000$       |  $2$  |    否    |
| $10 \sim 11$ |      $2000$       |      $2000$       |  $3$  |    否    |
| $12 \sim 13$ | $2 \times {10}^5$ | $2 \times {10}^5$ |  $1$  |    否    |
|     $14$     | $5 \times {10}^4$ | $5 \times {10}^4$ |  $2$  |    是    |
| $15 \sim 16$ |     ${10}^5$      |     ${10}^5$      |  $2$  |    是    |
| $17 \sim 19$ | $2 \times {10}^5$ | $2 \times {10}^5$ |  $2$  |    否    |
|     $20$     | $5 \times {10}^4$ | $5 \times {10}^4$ |  $3$  |    是    |
| $21 \sim 22$ |     ${10}^5$      |     ${10}^5$      |  $3$  |    是    |
| $23 \sim 25$ | $2 \times {10}^5$ | $2 \times {10}^5$ |  $3$  |    否    |

留意其中特别小的值，以及特殊的值。

首先，当 $n,Q$ 较小的时候，可以直接连边处理 $k\geq2$ 的情况，转化为多源最短路问题，跑一遍 Floyd 再回答询问即可，可以处理 $n\leq200$ 的所有数据。

观察到 $k$ 的值只有 $1,2,3$，判断 $k$ 是一个切入点，可能会有分类讨论。

当 $k=1$ 的时候，显然题干所求就是树上两点间距离，倍增求一下 lca，时间复杂度 $O(Q\log n)$，可以拿下所有 $k=1$ 的测试点。

当 $k=2$ 的时候，显然选取的这些点依然在树上两点路径上，只能删去一些点，但不能跳出去。把这条链拉出来做 dp 就行了。

设树的深度为 $h$，则时间复杂度为 $O(Qh)$。极端情况下是 $O(Qn)$，可以拿下测试点 8~9。同时，特殊性质决定了这棵树是随机的，期望深度是 $O(\log n)$，可以再拿下测试点 14~16。

然后 $k=3$ 的情况比较复杂，可以直接抛掉，也可以顺着 $k=2$ 继续想（其实就是复杂一点的 dp）。再加上 $k=2$ 的情况没有拿满，可能需要一些神奇的数据结构优化，正解思路就出来了。

这里提供一份只讨论 $k=1,k=2$，不加多源最短路的代码，44 分。
加上多源最短路能再拿 12 分（测试点 2、4、5）。
如果 $k=3$ 的 dp 想出来的话，能拿到 76 分。
最后加上本题正解，矩阵优化，才能拿到 100 分。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=2e5+5, MAXI=20;
int n, q, k, f[MAXN][MAXI+5], depth[MAXN];
long long v[MAXN], sv[MAXN];
vector <int> G[MAXN];

void dfs(int u, int fa)
{
	f[u][0] = fa;
	for (int i = 1; i <= MAXI; ++i) {
		f[u][i] = f[f[u][i-1]][i-1];
	}
	depth[u] = depth[fa] + 1;
	sv[u] = sv[fa] + v[u];
	for (int son: G[u]) {
		if (son != fa) { dfs(son, u); }
	}
}

int get_LCA(int x, int y)
{
	if (depth[x] < depth[y]) { swap(x,y); }
	for (int i = MAXI; i >= 0; --i) {
		if (depth[f[x][i]] >= depth[y]) {
			x = f[x][i];
		}
	}
	if (x == y) { return x; }
	for (int i = MAXI; i >= 0; --i) {
		if (f[x][i] != f[y][i]) {
			x = f[x][i];
			y = f[y][i];
		}
	}
	return f[x][0];
}

namespace case1
{
	void solve()
	{
		for (int i = 1; i <= q; ++i) {
			int x, y;
			cin >> x >> y;
			int LCA = get_LCA(x, y);
			cout << sv[x]+sv[y]-sv[LCA]-sv[f[LCA][0]] << endl;
		}
	}
}

namespace case2
{
	vector <int> t;
	vector <long long> dp;
	
	void solve()
	{
		for (int i = 1; i <= q; ++i) {
			int x, y;
			cin >> x >> y;
			int LCA = get_LCA(x, y);
			
			t.clear();
			while (x != LCA) { t.push_back(x); x=f[x][0]; }
			t.push_back(LCA);
			int sz = t.size();
			while (y != LCA) { t.push_back(y); y=f[y][0]; }
			reverse(t.begin()+sz, t.end());
			
			dp.resize(t.size());
			dp[0] = v[t[0]];
			dp[1] = v[t[0]] + v[t[1]];
			for (int i = 2; i < t.size(); ++i) {
				dp[i] = v[t[i]] + min(dp[i-1], dp[i-2]);
			}
			cout << dp[t.size()-1] << endl;
		}
	}
}

int main()
{
//	freopen("transmit3.in", "r", stdin);
//	freopen("transmit.out", "w", stdout);
	cin >> n >> q >> k;
	for (int i = 1; i <= n; ++i) {
		cin >> v[i];
	}
	for (int i = 1; i < n; ++i) {
		int a, b;
		cin >> a >> b;
		G[a].push_back(b);
		G[b].push_back(a);
	}
	dfs(1, 0);
	if (k == 1) { case1::solve(); }
	if (k == 2) { case2::solve(); }
}
```

# 考场策略和注意事项

附上 [SH 通知](https://www.noi.cn/gs/xw/sh/2023-10-13/796287.shtml)，[洛谷上的技术问题汇总](https://www.luogu.com.cn/blog/StudyingFather/noi-technical-faq)，还有大佬们写的注意事项，存一下。

-  [CSP注意事项 1](https://www.cnblogs.com/FXT1110011010OI/p/16571764.html)
-  [CSP 注意事项 2](https://www.cnblogs.com/codingxu/p/15433809.html)
-  [CSP 注意事项 3](https://www.cnblogs.com/milkycoffee/p/csp-s-2021-zhuyi.html)
-  [CSP 注意事项 4](https://www.cnblogs.com/dysyn1314/p/13934854.html)
- [CSP 注意事项 5](https://www.luogu.com.cn/discuss/714884)


## 明天要带的东西：

- 小袋子（准考证、身份证、笔）
- 充满电的手机（+充电宝+充电线）
- 手表
- 水、吃的
- 方便带进考场的大袋子
- 脑子
- 信仰

## 我自己的 Dev-c++配置：

- 右上角调成 `TDM-GCC 4.9.2 64-bit Debug`
- 工具——编译选项——编译时加入如下命令 `-std=c++14 -O0`，不要直接开 O2，本地 debug 的时候会抽搐
- 工具——编辑器选项——去除结尾空白、右边缘
- 工具——编辑器选项——语法——comment——不要斜体
- 工具——编辑器选项——代码补全——不允许代码补全、不启用符号配对
- 工具——编辑器选项——自动保存——启用编辑器自动保存

## 代码相关

- `bits/stdc++.h` 不要打成反斜杠。
- 开 `long long`！不开见祖宗！一旦做乘法就要警惕，防止两个 `int` 乘起来爆炸！
- 文件输入输出！freopen！freopen！
- 多测要清空，输出要换行，换行用 `"\n"`。
- 变量名不要用 `y0`，`y1`，也不要用完整的单词（如 `next`、`hash` 等），可能会冲突。

## 考试策略

- 按照我的考点，文件夹放 E 盘，文件夹命名：“SH-编号+首字母全拼”。最后把编译出来的 exe 删掉。
- 开考后的十分钟内只读题，别急着写代码。沉着冷静，题目读三遍，不要被巨长的题干吓到。水别喝太快，要是太慌就申请上厕所，去洗把脸。
- 写代码之前先手动跑一遍，不要写到一半发现做法假了，很浪费时间的。大模拟、线段树放到最后写，先把能拿的全拿了。
- 如果一道题暴力解法超过 30 分钟还没调出来，或者超过 15 分钟没思路，先冷静冷静，上个厕所、吃点东西、看看别的题。
- 除了给的样例，额外写 3 组特殊数据来测试，如果可以再对个拍。
- 把写完的代码放到 linux 里面，拿 Geany 编译一下，确保不要出事情。
- 数据分段别卡得太死，有些正解做法假了就不要拿来正解刚后面的测试点了，暴力剪枝说不定能对更多。
- 结束前最后检查一遍 `freopen`，千万不要因为这个爆零。


# 结语

> 友情提示：昨天，你既然经历了艰苦的学习，今天，你必将赢得可喜的收获！  
> 祝你：诚实守信，沉着冷静，细致踏实，自信自强，去迎接胜利！

[CSP 2023 RP++！！！](https://www.bilibili.com/video/BV1dN4y1C7T9/)

update：2023-11-17，NOIP2023 RP++