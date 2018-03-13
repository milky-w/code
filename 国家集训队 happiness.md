# code
```c++
#include <cstdio>
#include <string>
#include <vector>
#include <queue>
#include <cstring>

typedef long long ll;
const int N = 10005;
const ll INF = 1e18;

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

ll min(ll x, ll y) {
	if (x <= y) return x; return y;
}

struct Edge {
	int from, to; ll flow, cap;
};
std::vector<Edge> edges;
std::vector<int> G[N];

int n, S, T, p[N], d[N];

void addEdge(int from, int to, ll cap) {
	edges.push_back((Edge){from, to, 0, cap});
	edges.push_back((Edge){to, from, 0, 0});
	int size = edges.size();
	G[from].push_back(size - 2);
	G[to].push_back(size - 1);
}

void bfs() {
	bool vis[N] = {}; vis[T] = 1;
	std::queue<int> Q; Q.push(T);
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

ll augment() {
	int x = T; ll a = INF;
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

ll maxflow() {
	int num[N] = {}, cur[N] = {}, x = S; ll flow = 0;
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

int sci[102][102], lib[102][102];

int main() {
	int R = read(), C = read(); ll sum = 0; S = 0, T = R * C + 1;
	for (int i = 1; i <= R; ++ i)
		for (int j = 1; j <= C; ++ j)
			lib[i][j] = read(), sum += lib[i][j], lib[i][j] <<= 1;
	for (int i = 1; i <= R; ++ i)
		for (int j = 1; j <= C; ++ j)
			sci[i][j] = read(), sum += sci[i][j], sci[i][j] <<= 1;
	for (int i = 1; i < R; ++ i)
		for (int j = 1; j <= C; ++ j) {
			int x = read(); sum += x;
			lib[i][j] += x, lib[i+1][j] += x;
			addEdge((i-1)*C+j, i*C+j, x),
			addEdge(i*C+j, (i-1)*C+j, x);
		}
	for (int i = 1; i < R; ++ i)
		for (int j = 1; j <= C; ++ j) {
			int x = read(); sum += x;
			sci[i][j] += x, sci[i+1][j] += x;
			addEdge((i-1)*C+j, i*C+j, x),
			addEdge(i*C+j, (i-1)*C+j, x);
		}
	for (int i = 1; i <= R; ++ i)
		for (int j = 1; j < C; ++ j) {
			int x = read(); sum += x;
			lib[i][j] += x, lib[i][j+1] += x;
			addEdge((i-1)*C+j, (i-1)*C+j+1, x),
			addEdge((i-1)*C+j+1, (i-1)*C+j, x);
		}
	for (int i = 1; i <= R; ++ i)
		for (int j = 1; j < C; ++ j) {
			int x = read(); sum += x;
			sci[i][j] += x, sci[i][j+1] += x;
			addEdge((i-1)*C+j, (i-1)*C+j+1, x),
			addEdge((i-1)*C+j+1, (i-1)*C+j, x);
		}
	for (int i = 1; i <= R; ++ i)
		for (int j = 1; j <= C; ++ j)
			addEdge(S, (i-1)*C+j, lib[i][j]),
			addEdge((i-1)*C+j, T, sci[i][j]);
	n = R * C + 2;
	printf("%lld\n", sum - (maxflow() >> 1));
	return 0;
}
```
