---
tags:
  - 题解
  - 栈
  - 栈/单调栈
aliases:
  - AcWing41. 包含min函数的栈
  - 包含min函数的栈
speed: 
---
## [41. 包含min函数的栈](https://www.acwing.com/problem/content/90/)

单调栈模板题，另外搞一个栈存储前缀最小值即可。

#### [AC 代码](https://www.acwing.com/problem/content/submission/code_detail/36434273/)

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    int cnt=0, a[105], m[105];
    
    MinStack() {
    }
    
    void push(int x) {
        ++cnt;
        a[cnt] = x;
        if (cnt == 1) { m[cnt] = x; }
        else          { m[cnt] = min(m[cnt-1], x); }
    }
    
    void pop() {
        --cnt;
    }
    
    int top() {
        return a[cnt];
    }
    
    int getMin() {
        return m[cnt];
    }
};
```
