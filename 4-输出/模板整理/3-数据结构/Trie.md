---
tags:
  - Trie
  - Trie/01Trie
  - 模板
---
普通 Trie
```cpp
int tot, trie[N][62], val[N];

int newNode()
{
    ++tot;
    fill(trie[tot], trie[tot]+62, 0);
    val[tot] = 0;
    return tot;
}

void add(string s)
{
    int now = 0;
    for (auto c: s) {
        int v;
        if ('a' <= c && c <= 'z') { v = c - 'a'; }
        if ('A' <= c && c <= 'Z') { v = c - 'A' + 26; }
        if ('0' <= c && c <= '9') { v = c - '0' + 52; }
        if (!trie[now][v]) {
            trie[now][v] = newNode();
        }
        now = trie[now][v];
        ++val[now];
    }
}

int query(string s)
{
    int now = 0;
    for (auto c: s) {
        int v;
        if ('a' <= c && c <= 'z') { v = c - 'a'; }
        if ('A' <= c && c <= 'Z') { v = c - 'A' + 26; }
        if ('0' <= c && c <= '9') { v = c - '0' + 52; }
        if (!trie[now][v]) {
            return 0;
        }
        now = trie[now][v];
    }
    return val[now];
}
```
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
