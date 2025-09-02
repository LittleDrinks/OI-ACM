```cpp
const int V = 5000;
int g[V + 5][V + 5];
int gcd(int x, int y) {
    x = abs(x);
    y = abs(y);
    if (x <= V && y <= V && g[x][y] != 0) {
        return g[x][y];
    }
    ll res = x == 0 ? y : gcd(y % x, x);
    if (x <= V && y <= V) {
        g[x][y] = res;
    }
    return res;
}
int lcm(int x, int y) {
    int d = gcd(x, y);
    return d == 0 ? 0 : x / d * y;
}
```
