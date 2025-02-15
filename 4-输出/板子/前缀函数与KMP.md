---
tags:
---
```cpp
vector<int> prefixFunction(string s)
{
    vector<int> pi(s.length());
    for (int i = 1; i < s.length(); ++i) {
        int j = pi[i-1];
        while (j && s[j]!=s[i]) { j = pi[j-1]; }
        if (s[j] == s[i]) { ++j; }
        pi[i] = j;
    }
    return pi;
}
void kmp()
{
	string s, t;
    cin >> s >> t;
    int n = s.length(), m = t.length();
    vector<int> pi = prefixFunction(t+"#"+s);
    for (int i = m+1; i <= n+m; ++i) {
        if (pi[i] == m) { cout << i-2*m+1 << "\n"; }
    }
    for (int i = 0; i < m; ++i) { cout << pi[i] << " "; }
}
```