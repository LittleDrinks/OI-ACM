给定一个非负整数序列 $\{a\}$，初始长度为 $N$。  有 $M$ 个操作，有以下两种操作类型：  

1. `A x`：添加操作，表示在序列末尾添加一个数 $x$，序列的长度 $N$ 加 $1$。  
2. `Q l r x`：询问操作，你需要找到一个位置 $p$，满足 $l \le p \le r$，使得：$a[p] \oplus a[p+1] \oplus ... \oplus a[N] \oplus x$ 最大，输出最大值。

```cpp
const int N=2e7+9;
struct Trie {
    int ch[N][2];
    int root[N], cnt[N], top=0, siz=0;
    void insert(int x) {
        root[++top] = ++siz;
        int rt1 = root[top-1], rt2 = root[top];
        for (int i = 24; i >= 0; --i) {
            int b = x >> i & 1;
            ch[rt2][!b] = ch[rt1][!b];
            ch[rt2][b] = ++siz;
            rt1 = ch[rt1][b];
            rt2 = ch[rt2][b];
            cnt[rt2] = cnt[rt1] + 1;
        }
    }
    int query(int l, int r, int x) {
        int ans = 0;
        int rt1 = root[l], rt2 = root[r];
        for (int i = 24; i >= 0; --i) {
            int b = x >> i & 1;
            if (cnt[ch[rt2][!b]] > cnt[ch[rt1][!b]]) {
                ans += (1 << i);
                b = !b;
            }
            rt1 = ch[rt1][b];
            rt2 = ch[rt2][b];
        }
        return ans;
    }
} tr;

void solve()
{
	int n, m;
	cin >> n >> m;
    tr.insert(0);
    int sum = 0; 
    for (int i = 1; i <= n; ++i) {
        int x; cin >> x;
        tr.insert(sum ^= x);
    }
    for (int i = 1; i <= m; ++i) {
        char op; cin >> op;
        if (op == 'A') {
            int x; cin >> x;
            tr.insert(sum ^= x);
        } else {
            int l, r, x;
            cin >> l >> r >> x;
            cout << tr.query(l-1, r, x^sum) << "\n";
        }
    }
}
```
