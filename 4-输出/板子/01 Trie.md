---
tags:
---
求 $\displaystyle \max_x \{x\oplus y\}$。求树上最长异或路径。
```cpp
struct Trie {
	int tot=0, ch[N*I][2];	
	void insert(int x)
	{
		int now = 0;
		for (int i = I; i >= 0; --i) {
			int b = (x>>i)&1;
			if (!ch[now][b]) { ch[now][b] = ++tot; }
			now = ch[now][b];
		}
	}
	ll query(int x)
	{
		int now=0;
		ll ans=0;
		for (int i = I; i >= 0; --i) {
			int b = x>>i&1;
			if (ch[now][b^1]) {
				ans = ans*2+1;
				now = ch[now][b^1];
			} else {
				ans = ans*2;
				now = ch[now][b];
			}
		}
		return ans;
	}
} T;
```