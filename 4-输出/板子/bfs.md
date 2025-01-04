---
tags:
---
```cpp
const int dx={-1, 1, 0, 0}, dy={0, 0, -1, 1}  // d={上, 下, 左, 右}
int f[N][N];
bool vis[N][N];
bool valid(int x, int y)
{
    return (
        (1 <= x && x <= h) &&
        (1 <= y && y <= w)
    );
}
int sx, sy, tx, ty;
int bfs()
{
    queue <array<int,3>> q;
    q.push( { sx, sy, 0 } );
    memset(f, 0x3f, sizeof(f));
    while (!q.empty()) {
        auto [x, y, cnt] = q.front(); q.pop();
        if (vis[x][y]) { continue; }
        vis[x][y] = true;
        f[x][y] = cnt;
        for (int dd = 0; dd < 4; ++dd) {
            int nx = x + dx[dd];
            int ny = y + dy[dd];
            if (valid(nx, ny)) { q.push( { nx, ny, cnt+1 } ); }
        }
    }
    if (vis[tx][ty]) { return f[tx][ty]; }
    else { return -1; }
}
```