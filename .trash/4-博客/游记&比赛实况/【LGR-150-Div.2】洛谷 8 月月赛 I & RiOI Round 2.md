---
tags:
  - 博客
---
# 比赛实况
赛前看了眼难度分布，红橙黄绿，感觉随便杀（爆我）

顺序开题，先看 A 题，没仔细读，一眼以为单次操作只能翻转一位，写了个十进制转二进制找不同，结果 `WA` 了。
再看了一眼题，发现题干定义的操作可以一次操作很多位，然后一个操作是把 0 变 1，另一个是把 1 变 0。
所以只需要看两个数二进制对应位置上的数分别是 `0,1` 还是 `1,0`，每种需要一步操作，至多两步，A 题 `AC`。

------------

然后看 B，一眼瞪出了个二分做法，每次操作时枚举每一列，在每一列上做二分。
反正代码很简单，都没算复杂度直接开打，一交，50pts。
回来看了眼数据范围，发现这么做铁定 `tle`，然后想了想决定离线做，把时间复杂度搞到 $O(n^2)$。
然后就写炸了……，看了两眼 C 题，看到树就有点不太想开，回来再调了会儿，依然是全 `WA`。
不过好歹没 `tle`，就先抛下来去看 C 题，准备之后再调一调。

------------

C 题看了一会儿，发现跟树没什么关系，做一遍 `dfs` 把深度处理出来，然后就跟树没关系了。
首先想到的判断无解的方法是对深度求和，看奇偶性，如果和是奇数直接判无解。
然后凑数字的部分也还挺好做的，跟用二进制表示一个数，以及求 lca 的时候倍增上跳很像，从大到小拿深度往上加就行了。
很快写好代码，一交，85pts。
看到两个点 `WA`，第一反应没开 `long long`，改完再一交，95pts，有点难受。
这个时候心态还算稳定，开始静态查错，发现深度居然没排序，这能干到 95pts 也真是神奇，补上排序一交，`AC`。

------------

然后看 D，一眼感觉是个贪心，第二眼把自己否定了。
因为模模糊糊感觉单次操作不仅仅和 `i` 与 `i+1` 有关，如果 `i` 不够还可以从前面转换补一点过来。
然后马上决定看数据范围，先打部分分，看到 $x_i=10^9$，第一眼不明所以，稍微想了想，这不是根本不用操作？！
然后就炸了。
心态大崩，回去调 B，各种调调不出来，决定重写，然后写到一半突然发现我的 `sort` 写错了……
应该是 `sort(qr+1, qr+q+1, cmp)`，我写成了 `sort(qr+1, qr+n+1, cmp)`。
样例里面 $n=q$，怎么测都测不出来。警钟敲烂：两个大小不一样的数组分别做排序，要分清大小，不要顺手都写 $n$。

然后改完一交 B，发现最后一个 subtask 里面有两个点 `tle` 了，而且只超了一秒以内，马上把 `cin/cout` 换成 `scanf/printf`，`AC`。
然后突然想到，不会二分的做法也是因为这个挂了吧。
赶紧回头一测，还好改完还是挂成 50pts，不然心态真的要崩了。

------------

300pts，本来以为稳得不得了，一看榜，好家伙 rk300+。
到这里这场比赛已经变成 ABC 手速场，以及 D 部分分大赛了，当下决定去 D 搞点分先。

再读了一遍题，看到了题干里的【非负数】，心里一惊，好家伙数据范围看错了，没看到 $c=0$ 和 $v=0$ 是可以的……
然后改了改代码，反正如果这个位置 $v=0$，那就操作一次丢到后面去，一交，依然 `WA`。
心态雪崩，回来看了眼数据范围，嗷又没开 `long long` ……
成功搞到 5pts，排名直接变成 200+，不愧是部分分大赛……

然后看 subtask2，$x=1$，一眼贪心，全部转到往后 $v$ 最大的地方就行了。
本来以为很好写，没想到又是各种 `WA`。
再看了一眼数据范围，感觉如果全部操作完最后再算，能把 `long long` 撑爆开。
于是换写法，直接计算 `c[i]` 对答案的贡献，反正操作完再算和这个一样，结果又 `WA`。
想着反正就靠这题部分分了，心态稳定了不少，结果发现是写 `for` 循环的时候 `<=` 写成了 `<`，5pts->20pts。

特殊性质 3 不明所以，直接想 $n\leq300$ 的暴力。
各种想怎么立方枚举，好久没想出来，决定先按照之前的 hack 思路先搓几个数据出来。
解法嗑了好久，先是想什么从 $i$ 到 $j$ 的最小 $v$ 值，再是想转换过去的收益，然后往枚举上面靠，最后靠写注释理清了思路。
> 枚举 i、j，每次看看从 i 转换到 j 可不可行，赚不赚钱
> 如果可行又赚钱，那么必须转

是否可行，以及具体操作，都可以用模拟解决，反正复杂度能到立方。
结果写完一交发现是自己自信了，subtask3 全 T 了。
（这里加了 $sid$ 的特判，丝毫没有意识到自己应该写的是 subtask4……）

然后一想，如果枚举 $i\rightarrow j$，结果中间有一步 $i\rightarrow k$ 已经转换不了了，那么就没有继续枚举下去的必要了，直接跳出。
成功地再搞到了 15pts，rk 涨到了 100+。
然后终于发现了，应该写的是 subtask4，怎么 subtask3 莫名其妙过了，心里一喜，这不是 subtask3 随便过？！
然后就 `WA` 了…………………………………………………………
一直哇到比赛结束，各种调，怎么都写不出来……
比赛结束，rk129，跑完反作弊可能会再涨一点。
最后几分钟刷了几下榜，发现竟然有人 ak 了，\%\%\%

------------

为什么 2D 是一道黑题啊？？？
蚌埠住了，红—橙—黄—黑，这恒河里。

# 赛时代码
## A (100pts)
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e5+5;
int t;

/*
*/

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);
    // freopen("A_00.in", "r", stdin);

    // I.N.
    cin >> t;
    while (t--) {
        long long a, b, s1=0, s2=0;
        cin >> a >> b;
        while (a || b) {
            if (a%2 != b%2) {
                s1 |= a%2;
                s2 |= b%2;
            }
            a/=2; b/=2;
        }
        cout << s1+s2 << endl;
    }

    // E.D.
    return 0;
}
```

## B (50pts)
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e3+5;
int n, q, a[MAXN][MAXN];

/*
*/

int main()
{
    // cin.tie(nullptr) -> sync_with_stdio(false);
    // freopen("B_00.in", "r", stdin);

    // I.N.
    // cin >> n >> q;
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            // cin >> a[i][j];
            scanf("%d", &a[i][j]);
        }
        sort(a[i]+1, a[i]+n+1);
    }

    // Q.E.
    while (q--) {
        int v, sum=0;
        // cin >> v;
        scanf("%d", &v);
        for (int i = 1; i <= n; ++i) {
            int idx = lower_bound(a[i]+1, a[i]+n+1, v)-(a[i]+1);
            sum += n-idx;
            if (sum >= n) { break; }
        }
        // cout << min(sum, n) << endl;
        printf("%d\n", min(sum, n));
    }

    // E.D.
    return 0;
}
```

## B (100pts)
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e3+5, MAXQ=5e5+5;
int n, q, a[MAXN][MAXN], ans[MAXQ];

/*
*/

struct query {
    int idx, v;
} qr[MAXQ];

bool cmp_v(query a, query b)
{
    return a.v < b.v;
} 

int main()
{
    // cin.tie(nullptr) -> sync_with_stdio(false);
    // freopen("B_00.in", "r", stdin);

    // I.N.
    // cin >> n >> q;
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; ++i) {
        a[i][0] = 0;
        for (int j = 1; j <= n; ++j) {
            // cin >> a[i][j];
            scanf("%d", &a[i][j]);
        }
        sort(a[i]+1, a[i]+n+1);
    }
    for (int i = 1; i <= q; ++i) {
        qr[i].idx=i;
        // cin >> qr[i].v;
        scanf("%d", &qr[i].v);
    }
    sort(qr+1, qr+q+1, cmp_v);

    // 离线
    int cnt_to_limit = 0;
    for (int i = 1; i <= q; ++i) {
        int sum = 0;
        if (cnt_to_limit < n) {
            for (int j = 1; j <= n; ++j) {
                if (a[j][0] > n) { continue; }
                while ( (a[j][0] <= n) && (a[j][ a[j][0] ] < qr[i].v) ) { ++a[j][0]; }
                cnt_to_limit += (a[j][0]>n);
                sum += n-a[j][0]+1;
            }
        }
        ans[qr[i].idx] = min(sum, n);
    }

    // E.D.
    for (int i = 1; i <= q; ++i) {
        // cout << ans[i] << "\n";
        printf("%d\n", ans[i]);
    }
    return 0;
}
```

## C (100pts)
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=1e6+5;
int n, cnt=1, head[MAXN], c[MAXN];

struct edge {
    int v, nxt;
} e[MAXN<<1];

struct node {
    int idx, depth;
} nd[MAXN];

bool cmp_d(node a, node b)
{
    return a.depth < b.depth;
}

void add_edge(int u, int v)
{
    e[++cnt] = {v, head[u]};
    head[u] = cnt;
}

void dfs(int u, int fa, int d)
{
    nd[u] = {u, d};
    for (int i = head[u]; i; i=e[i].nxt) {
        int v = e[i].v;
        if (v != fa) { dfs(v, u, d+1); }
    }
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);
    // freopen("C_01.in", "r", stdin);

    // I.N.
    cin >> n;
    for (int i = 1; i < n; ++i) {
        int u, v; cin >> u >> v;
        add_edge(u, v);
        add_edge(v, u);
    }

    // dfs 求深度
    dfs(1, 0, 1);
    sort(nd+1, nd+n+1, cmp_d);

    // 对深度求和
    long long sum = 0;
    for (int i = 1; i <= n; ++i) { sum += nd[i].depth; }
    
    // 如果和是奇数，那么不行，否则可行
    if (sum & 1) { cout << -1 << endl; return 0; }

    // 从大到小考虑染色
    long long sum_w = 0;
    for (int i = n; i; --i) {
        if (sum_w + nd[i].depth <= sum/2) {
            sum_w += nd[i].depth;
            c[nd[i].idx] = 1;
        }
    }

    // 输出
    if (sum_w != sum-sum_w) { cout << -1 << endl; return 0; }
    for (int i = 1; i <= n; ++i) { cout << c[i] << " "; }

    // E.D.
    return 0;
}
```

## D (35pts)
```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN=2e5+5, MOD=998244353;
int sid, t, n;
long long v[MAXN], c[MAXN], x[MAXN];

/*
*/

void in()
{
    // I.N.
    cin >> n;
    for (int i = 1; i <= n; ++i) { cin >> v[i]; }
    for (int i = 1; i <= n; ++i) { cin >> c[i]; }
    for (int i = 1; i < n; ++i) { cin >> x[i]; }
}

long long calc_sum()
{
    long long sum = 0;
    for (int i = 1; i <= n; ++i) {
        sum = (sum + 1LL*v[i]*c[i]) % MOD;
    }
    return sum;
}

namespace case1
{
    void solve()
    {
        while (t--) {
            in();

            // 操作
            for (int i = 1; i < n; ++i) {
                if ( (c[i] >= x[i]) && (v[i] == 0) ) {
                    c[i] -= x[i];
                    ++c[i+1];
                }
            }

            // E.D.
            cout << calc_sum() << endl;
        }
    }
}

namespace case2
{
    long long gt[MAXN];
    long long maxv=0;

    void solve()
    {
        while (t--) {
            in();

            // 判断后方钱币中 v 的最大值
            // 如果 gt[i]>=v[i]，表示后方有 v 更大的钱币，在 x=1 的情况下需要往后转移，答案加上 gt[i]*c[i]
            // 否则答案加上 v[i]*c[i]
            maxv = gt[n] = v[n];
            for (int i = n-1; i; --i) {
                gt[i] = maxv;
                maxv = max(maxv, v[i]);
            }

            // 按照 gt[]操作
            long long sum = 0;
            for (int i = 1; i <= n; ++i) {
                sum = (sum + max(gt[i], v[i])*c[i]) % MOD;
            }

            // E.D.
            cout << sum << endl;
        }
    }
}

namespace case3
{
    /*
    枚举 i、j，每次看看从 i 转换到 j 可不可行，赚不赚钱
    如果可行又赚钱，那么必须转
    */

    bool f;

    long long profit(int s, int t)
    {
        long long p=0, curc=c[s];
        for (int i = s+1; i <= t; ++i) {
            p -= (curc-curc%x[i-1]) * v[i-1];
            p += (curc/x[i-1]) * v[i];
            if (curc / x[i-1] == 0) { f=false; return 0; }
            curc = c[i] + curc / x[i-1];
        }
        return p;
    }

    void operate(int s, int t)
    {
        for (int i = s+1; i <= t; ++i) {
            c[i] += c[i-1] / x[i-1];
            c[i-1] = c[i-1] % x[i-1];
        }
    }
   
    void solve()
    {
        while (t--) {
            in();

            // 枚举，把 i 转移到 j
            for (int i = 1; i <= n; ++i) {
                f = true;
                for (int j = i+1; j <= n; ++j) {
                    long long p = profit(i,j);
                    if (!f) { break; }
                    if (p > 0) { operate(i,j); }
                }
            }

            // E.D.
            cout << calc_sum() << endl;
        }
    }
}

namespace case4
{
    bool f;

    long long profit(int s, int t)
    {
        long long p=0, curc=c[s];
        for (int i = s+1; i <= t; ++i) {
            p -= (curc-curc%x[i-1]) * v[i-1];
            p += (curc/x[i-1]) * v[i];
            if (curc / x[i-1] == 0) { f=false; return 0; }
            curc = c[i] + curc / x[i-1];
        }
        return p;
    }

    void operate(int s, int t)
    {
        for (int i = s+1; i <= t; ++i) {
            c[i] += c[i-1] / x[i-1];
            c[i-1] = c[i-1] % x[i-1];
        }
    }
   
    void solve()
    {
        while (t--) {
            in();

            // 计算初始的和
            long long sum = calc_sum();

            // 枚举，把 i 转移到 j
            for (int j = 1; j <= n; ++j) {
                for (int i = 1; i < j; ++i) {
                    long long p = profit(i,j);
                    if (p > 0) { operate(i,j); sum = (sum+p)%MOD; }
                }
            }

            // E.D.
            cout << sum%MOD << endl;
        }
    }
}

int main()
{
    cin.tie(nullptr) -> sync_with_stdio(false);
    // freopen("D_03.in", "r", stdin);

    // I.N.
    cin >> sid >> t;
    switch (sid) {
        case 1: case1::solve(); break;
        case 2: case2::solve(); break;
        case 3: case3::solve(); break;
        default: case4::solve();
    }
    
    // E.D.
    return 0;
}
```
