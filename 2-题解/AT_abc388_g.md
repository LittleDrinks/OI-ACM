---
tags:
  - 题解
  - ST表
  - 贪心
  - 二分
aliases:
---
## [G - Simultaneous Kagamimochi 2](https://atcoder.jp/contests/abc388/tasks/abc388_g)

$O(qn)$ 解法见 [[AT_abc388_e]]。
假设在给定的饼列后面无限地跟着无限大的饼，考虑为了将连续的 $K$ 个饼都作为上层饼来制作 $K$ 个饼，所需要的下层饼的最小索引。
定义 $B_i$ 为将第 $i$ 个饼作为上层饼时，作为下层饼可能的最小索引 $(1\leq i\leq N)$。
那么对于查询 $(L,R)$，答案就是满足 $\displaystyle L+K-1+\max\left\lbrace\max _ {L\leq i\lt L+K}\lbrace B _ i-i\rbrace,K\right\rbrace\leq R$ 的最大 $K$。
左侧关于 $K$ 是单调的，因此可以用 ST 表维护 $B_i-i$ 的区间最值，然后二分查找。
时间复杂度为 $O(n\log n+q\log n)$。

#### [AC 代码](https://atcoder.jp/contests/abc388/submissions/61619796)

```cpp
#include <bits/stdc++.h>

using namespace std;

template <typename T>
struct ST {
    int n=0, I=0;
    vector<int> Log;
    vector<vector<T>> st;
    ST() { }
    ST(const vector<T>& a) {
        n = a.size();
        Log.assign(n+1, 0);
        for (int i = 2; i <= n; ++i) { I=Log[i]=Log[i/2]+1; }
        st.assign(I+1, vector<T>(n));
        copy(a.begin(), a.end(), st[0].begin());
        for (int i = 1; i <= I; ++i) {
            for (int j = 0; j+(1<<(i-1)) < n; ++j) {
                st[i][j] = max( st[i-1][j], st[i-1][j+(1<<(i-1))] );
            }
        }
    }
    T query(int l, int r) {  // 注意下标从 1 开始
        --l, --r;
        int s = Log[r-l+1];
        return max( st[s][l], st[s][r-(1<<s)+1] );
    }
};

int main()
{
    int n; cin >> n;
    vector<int> a(n), d(n);
    for (auto &x: a) { cin >> x; }
    for (int i=0, j=0; i < n; ++i) {
        while (j < n && a[i]*2 > a[j]) { ++j; }
        d[i] = j-i;
    }
    ST<int> st(d);
    int q; cin >> q;
    while (q--) {
        int l, r; cin >> l >> r;
        int ll=0, rr=(r-l+1)/2+1, k;
        while (ll != rr-1) {
            k = (ll + rr) >> 1;
            int p = l+k-1+max(st.query(l,l+k-1), k);
            if (p <= r) { ll = k; }
            else { rr = k; }
        }
        cout << ll << "\n";
    }
}
```
