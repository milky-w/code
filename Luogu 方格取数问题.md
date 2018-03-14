# code
```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <queue>
#include <vector>

const int N = 10005, INF = 0x3f3f3f3f;

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
	int from, to, flow, cap;
};
std::vector<Edge> edges;
std::vector<int> G[N];
int S, T, n, p[N], d[N];

void addEdge(int from, int to, int cap) {
	edges.push_back((Edge){from, to, 0, cap});
	edges.push_back((Edge){to, from, 0, 0});
	int size = edges.size();
	G[from].push_back(size - 2);
	G[to].push_back(size - 1);
}

void bfs() {
	std::queue<int> Q; Q.push(T);
	int vis[N] = {}; vis[T] = 1;
	memset(d, 0x3f, sizeof d); d[T] = 0;
	while (!Q.empty()) {
		int u = Q.front(); Q.pop();
		int size = G[u].size();
		for (int i = 0; i < size; ++ i) {
			Edge e = edges[G[u][i]], g = edges[G[u][i]^1];
			if (g.cap > g.flow && !vis[e.to])
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
	bfs();
	for (int i = 0; i < n; ++ i) ++num[d[i]];
	while (d[S] < n) {
		if (x == T) flow += augment(), x = S;
		int size = G[x].size(); bool ok = false;
		for (int i = cur[x]; i < size; ++ i) {
			Edge e = edges[G[x][i]];
			if (e.cap > e.flow && d[e.to] + 1 == d[x]) {
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
			if (--num[d[x]] == 0) break;
			++num[d[x] = m + 1], cur[x] = 0;
			if (x != S) x = edges[p[x]].from;
		}
	}
	return flow;
}

int val[105][105];

int main() {
	int r = read(), c = read(), sum = 0;
	S = 0, T = r * c + 1, n = T + 1;
	for (int i = 1; i <= r; ++ i)
		for (int j = 1; j <= c; ++ j) {
			val[i][j] = read(), sum += val[i][j];
			if ((i & 1) ^ (j & 1)) addEdge(S, (i-1)*c+j, val[i][j]);
			else addEdge((i-1)*c+j, T, val[i][j]);
			if (i > 1) {
				if ((i & 1) ^ (j & 1))
					addEdge((i-1)*c+j, (i-2)*c+j, INF);
				else
					addEdge((i-2)*c+j, (i-1)*c+j, INF);
			}
			if (j > 1) {
				if ((i & 1) ^ (j & 1))
					addEdge((i-1)*c+j, (i-1)*c+j-1, INF);
				else 
					addEdge((i-1)*c+j-1, (i-1)*c+j, INF);
			}
		}
	int tmp = maxflow();
	printf("%d\n", sum - tmp);
	return 0;
}
```
