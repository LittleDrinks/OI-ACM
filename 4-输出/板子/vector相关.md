---
tags:
---
```cpp
// 多维 vector，需要 c++17，c++14 只能老老实实写 vector<vector<int>>
vector a(n+1, vector (n+1, vector<int>(n+1)));

// 最值
int mx = *min_element(vec.begin(), vec.end(), cmp);
int mn = *min_element(vec.begin(), vec.end(), cmp);

// 去重与离散化
for (int i = 1; i <= n; ++i) { vec.push_back(a[i]); }
sort(vec.begin(), vec.end());
vec.erase(unique(vec.begin(), vec.end()), vec.end());
for (int i = 1; i <= n; ++i) {
	idx[i]=lower_bound(vec.begin(), vec.end(), a[i])-vec.begin()+1;  // 下标从1开始
}

// 前缀和
vector <ll> a(n), s(n);
partial_sum(a.begin(), a.end(), s.begin());
ll sum = accumulate(s.begin(), s.end(), 0LL);

// 填充 1~n，令 a[i]=i
iota(a.begin(), a.end(), 0);
```
