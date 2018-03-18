# code
科学需要一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <queue>
#include <vector>

const int N = 1200, INF = 0x3f3f3f3f;

int read() {
    int x = 0, f = 1;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = -1;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 3) + (x << 1) + (c ^ 48);
        c = getchar();
    }
    return x * f;
}

int max(int x, int y) {
    if (x >= y) return x; return y;
}

int min(int x, int y) {
    if (x <= y) return x; return y;
}

struct Edge {
    int from, to, flow, cap;
};
std::vector<Edge> edges;
std::vector<int> G[N];
int n, S, T, p[N], d[N];

void addedge(int from, int to, int cap) {
    edges.push_back((Edge){from, to, 0, cap});
    edges.push_back((Edge){to, from, 0, 0});
    int size = edges.size();
    G[from].push_back(size - 2);
    G[to].push_back(size - 1);
}

void bfs() {
    std::queue<int> Q; Q.push(T);
    bool vis[N] = {}; vis[T] = 1;
    for (int i = 0; i < n; ++ i) d[i] = n;
    d[T] = 0;
    while (!Q.empty()) {
        int u = Q.front(); Q.pop();
        int size = G[u].size();
        for (int i = 0; i < size; ++ i) {
            Edge e = edges[G[u][i]], g = edges[G[u][i]^1];
            if (!vis[e.to] && g.cap > g.flow)
                d[e.to] = d[u] + 1, vis[e.to] = 1, Q.push(e.to);
        }
    }
}

int augment() {
    int x = T, a = INF;
    while (x != S) {
        a = min(a, edges[p[x]].cap - edges[p[x]].flow);
        x = edges[p[x]].from;
    }
    x = T;
    while (x != S) {
        edges[p[x]].flow += a, edges[p[x]^1].flow -= a;
        x = edges[p[x]].from;
    }
    return a;
}

int maxflow() {
    int num[N] = {}, cur[N] = {}, x = S, flow = 0;
    bfs(); for (int i = 0; i < n; ++ i) ++num[d[i]];
    while (d[S] < n) {
        if (x == T) flow += augment(), x = S;
        int size = G[x].size(); bool ok = false;
        for (int i = cur[x]; i < size; ++ i) {
            Edge e = edges[G[x][i]];
            if (e.cap > e.flow && d[e.to]+1 == d[x]) {
                p[e.to] = G[x][i], cur[x] = i, ok = true, x = e.to;
                break;
            }
        }
        if (!ok) {
            int size = G[x].size(), m = n - 1;
            for (int i = 0; i < size; ++ i) {
                Edge e = edges[G[x][i]];
                if (e.cap > e.flow) m = min(m, d[e.to]);
            }
            if (--num[d[x]] <= 0) break;
            ++num[d[x] = m+1], cur[x] = 0;
            if (x != S) x = edges[p[x]].from;
        }
    }
    return flow;
}

int a[N], f[N];

int main() {
    int m = read(), s = 1;
    for (int i = 1; i <= m; ++ i)
        a[i] = read(), f[i] = 1;
    for (int i = m; i >= 1; -- i)
        for (int j = i + 1; j <= m; ++ j)
            if (a[j] >= a[i]) f[i] = max(f[i], f[j] + 1), s = max(s, f[i]);
    printf("%d\n", s);
    S = 0, T = 2 * m + 1;
    for (int i = 1; i <= m; ++ i) {
        addedge(i, i + m, 1);
        if (f[i] == s) addedge(S, i, 1);
        if (f[i] == 1) addedge(i + m, T, 1);
        for (int j = i + 1; j <= m; ++ j)
            if (a[j] >= a[i] && f[j] == f[i]-1)
                addedge(i + m, j, 1);
    }
    n = T + 1;
    int ans = maxflow();
    printf("%d\n", ans);
    if (f[1] == s) addedge(S, 1, INF), addedge(1, 1 + m, INF);
    addedge(m, m + m, INF), addedge(m + m, T, INF);
    ans += maxflow();
    printf("%d\n", ans);
    return 0;
}
```
