# code
```c++
#include <cstdio>
#include <cstring>
#include <string>
#include <queue>
#include <vector>

const int N = 5005, INF = 0x3f3f3f3f;

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
std::vector<int> G[N];
int p[N], a[N], d[N], S, T;

void addEdge(int from, int to, int cap, int cost) {
	edges.push_back((Edge){from, to, 0, cap, cost});
	edges.push_back((Edge){to, from, 0, 0, -cost});
	int size = edges.size();
	G[from].push_back(size - 2);
	G[to].push_back(size - 1);
}

bool SPFA(int &flow, int &cost) {
	std::queue<int> Q; Q.push(S);
	bool inq[N] = {}; inq[S] = 1;
	memset(d, 0x3f, sizeof d);
	d[S] = p[S] = 0, a[S] = INF;
	while (!Q.empty()) {
		int u = Q.front(); Q.pop(), inq[u] = 0;
		int size = G[u].size();
		for (int i = 0; i < size; ++ i) {
			Edge e = edges[G[u][i]];
			if (e.cap > e.flow && d[e.to] > d[u] + e.cost) {
				d[e.to] = d[u] + e.cost, p[e.to] = G[u][i];
				a[e.to] = min(a[u], e.cap - e.flow);
				if (!inq[e.to]) inq[e.to] = 1, Q.push(e.to);
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

int main() {
	int n = read(), k = read(), cnt = 0;
	for (int i = 1; i <= n; ++ i)
		for (int j = 1; j <= n; ++ j) {
			int val = read(); cnt += 2;
			addEdge(cnt - 1, cnt, 1, -val);
			addEdge(cnt - 1, cnt, INF, 0);
			if (i > 1) addEdge(cnt - n * 2, cnt - 1, INF, 0);
			if (j > 1) addEdge(cnt - 2, cnt - 1, INF, 0);
		}
	S = 0, T = cnt + 1;
	addEdge(S, 1, k, 0), addEdge(cnt, T, k, 0);
	printf("%d\n", -mincost());
	return 0;
}
```
