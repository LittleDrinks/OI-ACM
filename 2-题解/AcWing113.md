---
tags:
  - 题解
  - 二分
aliases:
  - AcWing113. 特殊排序
  - 特殊排序
speed:
---
## [113. 特殊排序](https://www.acwing.com/problem/content/115/)

二分优化插入排序。

##### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/36397983/)

```cpp
class Solution {
public:
    vector<int> specialSort(int N) {
        vector <int> ans;
        for (int i = 1; i <= N; ++i) { ans.push_back(i); }
        stable_sort(ans.begin(), ans.end(), compare);
        return ans;
    }
};
```

##### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/36397945/)

```cpp
class Solution {
public:
    vector<int> specialSort(int N) {
        vector <int> ans = {1};
        for (int i = 2; i <= N; ++i) {
            int l = -1, r = i-1;
            while (l != r-1) {
                int mid = (l+r)>>1;
                if (compare(i, ans[mid])) { r = mid; }
                else                      { l = mid; }
            }
            ans.insert(ans.begin()+r, i);
        }
        return ans;
    }
};
```