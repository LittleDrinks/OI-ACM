---
tags:
---
```cpp
vector<int> prefixFunction(string s)
{   // fail : 1-index
    int n = s.length();
    vector<int> fail(n + 1);
    for (int i = 1, j = 0; i < n; ++i) {
        while (j && s[i] != s[j]) { j = fail[j]; }
        j += (s[i] == s[j]);
        fail[i + 1] = j;
    }
    return fail;
}
void kmp()
{
	string s, t;
	cin >> s >> t;
	int n = s.length();
	int m = t.length();
	auto fail = prefixFunction(t + "#" + s);
	for (int i = m + 2; i <= n + m + 1; ++i) {
	    if (fail[i] == m) {
	        cout << i - 2*m << "\n";
	    }
	}
	for (int i = 1; i <= m; ++i) { cout << fail[i] << " \n"[i == m]; }
}
```