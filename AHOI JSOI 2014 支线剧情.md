# code
```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <queue>
#include <vector>

const int N = 310, INF = 0x3f3f3f3f;

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

int min(int x, int y) {
    if (x <= y) return x; return y;
}

struct Edge {
    int from, to, flow, cap, cost;
};
std::vector<Edge> edges;
std::vector<int> G[310];
int S, T, n, p[N], d[N], a[N];

void addEdge(int from, int to, int cap, int cost) {
    edges.push_back((Edge){from, to, 0, cap, cost});
    edges.push_back((Edge){to, from, 0, 0, -cost});
    int size = edges.size();
    G[from].push_back(size - 2);
    G[to].push_back(size - 1);
}

bool SPFA(int &flow, int &cost) {
    int inq[N] = {}; inq[S] = 1;
    std::queue<int> Q; Q.push(S);
    memset(d, 0x3f, sizeof d);
    d[S] = p[S] = 0, a[S] = INF;
    while (!Q.empty()) {
        int u = Q.front(); Q.pop(), inq[u] = 0;
        int size = G[u].size();
        for (int i = 0; i < size; ++ i) {
            Edge e = edges[G[u][i]];
            if (e.cap > e.flow && d[e.to] > d[u] + e.cost) {
                d[e.to] = d[u] + e.cost;
                p[e.to] = G[u][i];
                a[e.to] = min(a[u], e.cap - e.flow);
                if (!inq[e.to]) Q.push(e.to), inq[e.to] = 1;
            }
        }
    }
    if (d[T] == INF) return false;
    flow += a[T], cost += a[T] * d[T];
    int x = T;
    while (x != S) {
        edges[p[x]].flow += a[T], edges[p[x]^1].flow -= a[T];
        x = edges[p[x]].from;
    }
    return true;
}

int mincost() {
    int flow = 0, cost = 0;
    while (SPFA(flow, cost)) {}
    return cost;
}

int dgr[N];

int main() {
    n = read(), S = 0, T = n + 1; int sum = 0;
    for (int i = 1; i <= n; ++ i) {
        int k = read(); dgr[i] -= k;
        while (k--) {
            int j = read(), t = read(); sum += t;
            addEdge(i, j, INF, t); ++ dgr[j];
        }
    } 
    for (int i = 1; i <= n; ++ i) {
        if (i > 1) addEdge(i, 1, INF, 0);
        if (dgr[i] > 0) addEdge(S, i, dgr[i], 0);
        if (dgr[i] < 0) addEdge(i, T, -dgr[i], 0);
    }
    n += 2;
    printf("%d\n", sum + mincost());
    return 0;
}
```
