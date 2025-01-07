---
tags:
  - 输出
  - 模板
  - ST表
---
```cpp
template <typename T>
struct ST {
    int n=0, I=0;
    vector<vector<T>> st;
    vector<int> Log;
    ST() {}
    ST(vector <T> a) {
        n = a.size();
        Log.assign(n+1, 0);
        for (int i = 2; i <= n; ++i) { Log[i] = Log[i/2] + 1; }
        I = Log[n];
        st.assign(I+1, vector<T>(n,0));
        copy(a.begin(), a.end(), st[0].begin());
        for (int i = 1; i <= I; ++i) {
            for (int j = 0; j+(1<<(i-1))<n; ++j) {
                st[i][j] = max( st[i-1][j], st[i-1][j+(1<<(i-1))] );
            }
        }
    }
    T query(int l, int r) {
        --l; --r;
        int s = Log[r-l+1];
        return max( st[s][l], st[s][r-(1<<s)+1] );
    }
	int nxt(int now, T x) {  // 区间最大值大于等于 x
        int ans = now;
        for (int i = I; i >= 0; --i) {
            int p = ans + (1 << i);
            if (p <= n && query(now+1, p) < x) {
                ans = p;
            }
        }
        return ans+1;
    }
};
```
