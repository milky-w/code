## Description

定义无向图中的一条边的值为：这条边连接的两个点的值的异或值。
定义一个无向图的值为：这个无向图所有边的值的和。
给你一个有n个结点m条边的无向图。其中的一些点的值是给定的，而其余的点的值由你决定（但要求均为非负数），使得这个无向图的值最小。在无向图的值最小的前提下，使得无向图中所有点的值的和最小。
 
## Input

第一行，两个数n，m，表示图的点数和边数。
接下来n行，每行一个数，按编号给出每个点的值（若为负数则表示这个点的值由你决定，值的绝对值大小不超过10^9）。
接下来m行，每行二个数a，b，表示编号为a与b的两点间连一条边。（保证无重边与自环。）
 
## Output

第一行，一个数，表示无向图的值。
第二行，一个数，表示无向图中所有点的值的和。
 
## Sample Input

    3 2

    2

    -1

    0

    1 2

    2 3

 

## Sample Output

    2

    2

 

## HINT

数据约定

    n<=500，m<=2000

 

样例解释

    2结点的值定为0即可。
# code
```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <vector>
#include <queue>

typedef long long ll;
const int N = 505;
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

ll val[N];
int n, m, S, T, d[N], p[N];

struct edge {
	int from, to;
} E[2005];

struct Edge {
	int from, to; ll flow, cap;
};
std::vector<Edge> edges;
std::vector<int> G[505];

void addEdge(int from, int to, ll cap) {
	edges.push_back((Edge){from, to, 0, cap});
	edges.push_back((Edge){to, from, 0, 0});
	int size = edges.size();
	G[from].push_back(size - 2);
	G[to].push_back(size - 1);
}

void bfs() {
	std::queue<int> Q; Q.push(T);
	int vis[N] = {}; vis[T] = 1;
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
	int num[N] = {}, cur[N] = {}, x = S;
	ll flow = 0; bfs();
	for (int i = 0; i < n; ++ i) ++num[d[i]];
	while (d[S] < n) {
		if (x == T) flow += augment(), x = S;
		int size = G[x].size(); bool ok = false;
		for (int i = cur[x]; i < size; ++ i) {
			Edge e = edges[G[x][i]];
			if (e.cap > e.flow && d[x] == d[e.to] + 1) {
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

void build(int k) {
	edges.clear();
	for (int i = 0; i < n; ++ i) G[i].clear();
	for (int i = 1; i <= n - 2; ++ i)
		if (val[i] >= 0) {
			if (val[i] & k) addEdge(S, i, INF);
			else addEdge(i, T, INF);
		}
	for (int i = 1; i <= m; ++ i) {
		addEdge(E[i].from, E[i].to, 1);
		addEdge(E[i].to, E[i].from, 1);
	}
}

ll getans2() {
	int vis[N] = {}; vis[S] = 1; ll res = 0;
	std::queue<int> Q; Q.push(S);
	while (!Q.empty()) {
		int u = Q.front(); Q.pop();
		int size = G[u].size();
		for (int i = 0; i < size; ++ i) {
			Edge e = edges[G[u][i]];
			if (!vis[e.to] && e.cap > e.flow)
				++res, vis[e.to] = 1, Q.push(e.to);
		}
	}
	return res;
}

int main() {
	n = read(), m = read(), S = 0, T = n + 1;
	for (int i = 1; i <= n; ++ i) val[i] = read();
	for (int i = 1; i <= m; ++ i)
		E[i].from = read(), E[i].to = read();
	ll ans1 = 0, ans2 = 0; n += 2;
	for (int i = 0; i <= 30; ++ i) {
		build(1 << i);
		ans1 += (1 << i) * maxflow();
		ans2 += (1 << i) * getans2();
	}
	printf("%lld\n%lld\n", ans1, ans2);
	return 0;
}
```
