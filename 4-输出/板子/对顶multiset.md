---
tags:
  - 模板
  - 堆/对顶堆
---
```cpp
struct PairingHeap {
    int sz;
    multiset <int> minH, maxH;
    ll minS, maxS;
    void clear()
    {
        minH.clear(); maxH.clear();
        sz = minS = maxS = 0;
    }
    void move()
    {
        while ((int)minH.size() < (sz+1)/2) {
            minS += *maxH.rbegin();
            minH.insert(*maxH.rbegin());
            maxS -= *maxH.rbegin();
            maxH.erase(maxH.find(*maxH.rbegin()));
        }
        while ((int)minH.size() > (sz+1)/2) {
            maxS += *minH.begin();
            maxH.insert(*minH.begin());
            minS -= *minH.begin();
            minH.erase(minH.find(*minH.begin()));
        }
    }
    void add(int x)
    {  // 向对顶堆中插入一个元素
        ++sz;
        if (minH.empty() || x >= *minH.begin()) {
            minS += x;
            minH.insert(x);
        } else {
            maxS += x;
            maxH.insert(x);
        }
        move();
    }
    void del(int x)
    {  // 从对顶堆中删除一个x
        --sz;
        if (x >= *minH.begin()) {
            minS -= x;
            minH.erase(minH.find(x));
        } else {
            maxS -= x;
            maxH.erase(maxH.find(x));
        }
        move();
    }
    ll med()
    {  // 求中位数
        return *minH.begin();
    }
    ll dis_to_med()
    {  // 求对顶堆中的数字到中位数的距离和
        ll minSZ=(sz+1)/2, maxSZ=sz-minSZ, m=med();
        return (minS-minSZ*m)+(maxSZ*m-maxS);
    }
} p;
```
