# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <queue>
#include <vector>
#include <algorithm>

const int N = 5005, E = 110000, M = 100000, INF = 0x3f3f3f3f;

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
std::vector<int> G[E];
int n, S, T, p[E], d[E];
int lson[M], rson[M], root[N], rt, cnt, tot;
int a[N], b[N], w[N], l[N], r[N], q[N], c[N*3];

void addedge(int from, int to, int cap) {
	edges.push_back((Edge){from, to, 0, cap});
	edges.push_back((Edge){to, from, 0, 0});
	int size = edges.size();
	G[from].push_back(size - 2);
	G[to].push_back(size - 1);
}

void bfs() {
	std::queue<int> Q; Q.push(T);
	bool vis[E] = {}; vis[T] = 1;
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
	int num[E] = {}, cur[E] = {}, x = S, flow = 0;
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
			if (--num[d[x]] == 0) break;
			++num[d[x] = m+1], cur[x] = 0;
			if (x != S) x = edges[p[x]].from;
		}
	}
	return flow;
}

void point(int pre, int cur, int l, int r, int pos, int node) {
	if (l == r) {
		addedge(node, cur, INF);
	} else {
		lson[cur] = lson[pre], rson[cur] = rson[pre];
		int mid = l + ((r - l) >> 1);
		if (pos <= mid) {
			lson[cur] = ++cnt;
			point(lson[pre], lson[cur], l, mid, pos, node);
			addedge(lson[cur], cur, INF);
			if (rson[cur]) addedge(rson[cur], cur, INF);
			if (lson[pre]) addedge(lson[pre], lson[cur], INF);
		} else {
			rson[cur] = ++cnt;
			point(rson[pre], rson[cur], mid + 1, r, pos, node);
			addedge(rson[cur], cur, INF);
			if (lson[cur]) addedge(lson[cur], cur, INF);
			if (rson[pre]) addedge(rson[pre], rson[cur], INF);
		}
	}
}

void pointed(int cur, int l, int r, int L, int R, int node) {
	if (!cur) return;
	if (L <= l && r <= R) addedge(cur, node, INF);
	else {
		int mid = l + ((r - l) >> 1);
		if (L <= mid) pointed(lson[cur], l, mid, L, R, node);
		if (mid < R) pointed(rson[cur], mid + 1, r, L, R, node);
	}
}

int find(int l, int r, int x) {
	while (l <= r) {
		int mid = l + ((r - l) >> 1);
		if (c[mid] < x) l = mid + 1;
		else if (c[mid] == x) return mid;
		else r = mid - 1;
	}
}

int main() {
	n = read(); S = 0, T = 2 * n + 1; int sum = 0;
	for (int i = 1; i <= n; ++ i) {
		a[i] = read(), b[i] = read(), w[i] = read();
		l[i] = read(), r[i] = read(), q[i] = read();
		addedge(S, i, w[i]), addedge(i, T, b[i]);
		sum += b[i] + w[i], c[++tot] = a[i], c[++tot] = l[i], c[++tot] = r[i];
	}
	std::sort(c + 1, c + tot + 1);
	cnt = T;
	for (int i = 1; i <= n; ++ i) {
		int ll = find(1, tot, l[i]), rr = find(1, tot, r[i]),
			aa = find(1, tot, a[i]);
		pointed(root[rt], 1, tot, ll, rr, i + n);
		root[++rt] = ++cnt;
		point(root[rt-1], root[rt], 1, tot, aa, i);
		addedge(i + n, i, q[i]);
		addedge(root[rt-1], root[rt], INF);
	}
	n = cnt + 1;
	printf("%d\n", sum - maxflow());
	return 0;
}
```
