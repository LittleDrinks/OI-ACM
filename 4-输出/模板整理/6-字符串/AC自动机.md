给你一个文本串 $S$ 和 $n$ 个模式串 $T_{1 \sim n}$，请你分别求出每个模式串 $T_i$ 在 $S$ 中出现的次数。
fail 指针：指向次长真公共后缀的结束位置
```cpp
struct AhoCorasick {
    static constexpr int ALPHABET = 26;
    struct Node {
        int len;
        int link;
        array<int,ALPHABET> next;
        Node(): len{0}, link{0}, next{} { }
    };

    vector<Node> t;

    AhoCorasick() {
        init();
    }

    void init() {
        t.assign(2, Node());
        t[0].next.fill(1);
        t[0].len = -1;
    }

    int newNode() {
        t.emplace_back();
        return t.size() - 1;
    }

    int add(const string &a) {
        int p = 1;
        for (auto c: a) {
            int x = c - 'a';
            if (t[p].next[x] == 0) {
                t[p].next[x] = newNode();
                t[t[p].next[x]].len = t[p].len + 1;
            }
            p = t[p].next[x];
        }
        return p;
    }

    void work() {
        queue<int> q;
        q.push(1);

        while (!q.empty()) {
            int x = q.front();
            q.pop();

            for (int i = 0; i < ALPHABET; ++i) {
                if (t[x].next[i] == 0) {
                    t[x].next[i] = t[t[x].link].next[i];
                } else {
                    t[t[x].next[i]].link = t[t[x].link].next[i];
                    q.push(t[x].next[i]);
                }
            }
        }
    }

    int next(int p, int x) {
        return t[p].next[x];
    }

    int link(int p) {
        return t[p].link;
    }

    int len(int p) {
        return t[p].len;
    }

    int size() {
        return t.size();
    }
};

constexpr int N = 2e6+5;
vector<int> G[N];
int cnt[N];

void dfs(int u) 
{
    for (auto v: G[u]) {
        dfs(v);
        cnt[u] += cnt[v];
    }
}

void solve()
{
	int n;
	cin >> n;

    AhoCorasick AC;
    vector<int> pos(n + 1);
    for (int i = 1; i <= n; ++i) {
        string s;
        cin >> s;
        pos[i] = AC.add(s);
    }
    AC.work();

    string T;
    cin >> T;
    int p = 1;
    for (int i = 0; i < int(T.size()); ++i) {
        int x = T[i] - 'a';
        p = AC.next(p, x);
        ++cnt[p];
    }

    for (int i = 1; i < AC.size(); ++i) {
        G[AC.link(i)].push_back(i);
    }
    dfs(1);

    for (int i = 1; i <= n; ++i) {
        cout << cnt[pos[i]] << "\n";
    }
}
```
