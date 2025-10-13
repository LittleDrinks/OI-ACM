给定一个只包含小写字母的字符串 $S$。
请你求出 $S$ 的所有出现次数不为 $1$ 的子串的出现次数乘上该子串长度的最大值。
```cpp
//edited by piaoyun from some other's code 
//必须#define int long long 

struct SAM {
    static const int N=1e6+5, S=28;
    int tot=1, last=1, link[N<<1], ch[N<<1][S], len[N<<1], endpos[N<<1];
	// 总点数 tot，点的 index 属于 [1-tot]，空串/根为 1 
	// last 为上一次插入的点
	// link 为点的 parent 树父节点 / 最长 出现位置与自己不同 的后缀
	// ch[n][s] 指节点 n 末尾加字符 s 所转移到的点
	// len 指该节点的串的 最长长度，注意到 最短长度 等于 len[link[n]] + 1 即父节点最长 + 1
	// endpos[n] 参考 get_endpos() 的注释 
    void clear() {
        for (int i = 0; i <= tot; ++i) {
            link[i] = len[i] = endpos[i] = 0;
            for (int k = 0; k < S; ++k) { ch[i][k] = 0; }
        }
        tot = 1; 
        last = 1;
    }

    // 延长一个字符，通常为 [1-26] 
    void extend(int w) {
        int p = ++tot, x = last, r, q;
        endpos[p] = 1;
        for (len[last=p]=len[x]+1; x&&!ch[x][w]; x=link[x]) { ch[x][w]=p; }
        if (!x) { link[p] = 1; }
        else if (len[x] + 1 == len[q=ch[x][w]]) { link[p]=q; }
        else {
            link[r=++tot] = link[q];
            memcpy(ch[r], ch[q], sizeof(ch[r]));
            len[r] = len[x] + 1;
            link[p] = link[q] = r;
            for (; x && ch[x][w] == q; x = link[x]) { ch[x][w] = r; }
        }
    }

    // 注意 vector 占用的空间 
    vector<int> p[N<<1];  //建立 parent 树，以便从上到下 dfs 
    void dfs(int u) {
        int v;
        for (int i = 0; i < int(p[u].size()); ++i) {
            v = p[u][i];
            dfs(v);
            endpos[u] += endpos[v];
        }
    }

    //注意！在使用该方法前，endpos[] 代表每个点作为“终结点”的次数
	//使用该方法后，endpos[] 指在串中出现总次数，即原数组的子树求和
    void get_endpos() {
        for (int i = 1; i <= tot; ++i) { p[i].clear(); }
        for (int i = 2; i <= tot; ++i) {
            p[link[i]].push_back(i);
        }
        dfs(1);
        for (int i = 1; i <= tot; ++i) { p[i].clear(); }
    }

    // 在您不确定是否有抄写错误时再使用该方法 
    // 必须在输入任何数据前自检，此前的数据会被清空 
    static const int STC = 998244353;
    void self_test() {
        clear();
        for (int i = 1; i <= 1000; ++i) { extend(i * i % 26 + 1); }
        ll tmp = 107 * last + 301 * tot;
        for (int i = 1; i <= tot; ++i) {
            tmp = (tmp * 33 + link[i] * 101 + len[i] * 97) % STC;
            for (int k = 1; k < S; ++k) { tmp = (tmp + k * ch[i][k]) % STC; }
        }
        assert("stage 1" && tmp == 393281314);   // stage1 : 检查建树是否正确
        tmp = 0;
        get_endpos();
        for (int i = 1; i <= tot; ++i) { tmp = (tmp * 33 + endpos[i]) % STC; }
        assert("stage 2" && tmp == 178417668);   // stage2 : 检查endpos计算是否正确，如果您修改了endpos[]的含义则会报错
        cout << "Self Test Passed. Remember to delete this function's use. \n";
        clear(); 
    }

	// 调试时可调用 
    void debug_print() {
        for (int i = 1; i <= tot; ++i) {
            cerr << "node : " << i << " father : " << link[i] << " endpos : " << endpos[i] << " len : " << len[i] << "\n"; 
        }
    }

    ll solve() {
		// 在这里输入你自己的解题逻辑
        ll ans = 0;
        get_endpos();
        for (int i = 1; i <= tot; ++i) {
            if (endpos[i] >= 2) { ans = max(ans, (ll)endpos[i]*len[i]); }
        }
        return ans;
    }
} sam;

void solve()
{
    // sam.self_test();
    string s; cin >> s;
    for (auto c: s) { sam.extend(c-'a'+1); }
    cout << sam.solve() << "\n";
}
```
