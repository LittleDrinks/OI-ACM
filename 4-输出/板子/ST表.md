---
tags:
  - 输出
  - 模板
  - ST表
---
这是一个 $0$ 下标的封装版 ST 表。但是 $1$ 下标时令 $a[0]=0$ 不会影响答案。
```cpp
template <typename T>
struct ST {  // 标有“下标”的行都是下标改为 1 时需要修改的行
    int n=0, I=0;
    vector<int> Log;
    vector<vector<T>> st;
    ST() { }
    ST(const vector<T>& a) {
        n = a.size();                                 // 下标
        Log.assign(n+1, 0);
        for (int i = 2; i <= n; ++i) { I=Log[i]=Log[i/2]+1; }
        st.assign(I+1, vector<T>(n));                 // 下标
        copy(a.begin(), a.end(), st[0].begin());      // 下标
        for (int i = 1; i <= I; ++i) {
            for (int j = 0; j+(1<<(i-1)) < n; ++j) {  // 下标
                st[i][j] = max( st[i-1][j], st[i-1][j+(1<<(i-1))] );
            }
        }
    }
    T query(int l, int r) {  // 内部下标从 0 开始，注意传参
  		if (l > r) { return -1; } 
        int s = Log[r-l+1];
        return max( st[s][l], st[s][r-(1<<s)+1] );
    }
    int find(int l, T x) {  // 第一个区间 [l,r] 最值大于等于 x 的 r
    	if (l >= n) { return -1; }                    // 下标
        int rl=l-1, rr=n;
        while (rl != rr-1) {
            int mid = (rl + rr) >> 1;
            if (query(l, mid) >= x) { rr = mid; }
            else { rl = mid; }
        }
        if (rr < n) { return rr; }                    // 下标
        return -1;
    }
};
```
