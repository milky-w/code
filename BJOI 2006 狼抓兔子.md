# code
```C++
#include <cstdio>
#include <string>
#include <vector>
#include <queue>
#include <cstring>

const int N = 2000005;

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

struct Pair {
	int val, id;
	bool operator > (const Pair &cmp) const {
		return val > cmp.val;
	}
};

struct Edge {
	int to, dis;
};
std::vector<Edge> edges;
std::vector<int> G[N];
int dis[N];

void add(int from, int to, int dis) {
	edges.push_back((Edge){to, dis});
	edges.push_back((Edge){from, dis});
	int size = edges.size();
	G[from].push_back(size - 2);
	G[to].push_back(size - 1);
}

void Dij(int tot) {
	std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair> > Q;
	memset(dis, 0x3f, sizeof dis); dis[0] = 0;
	bool vis[N] = {}; Q.push((Pair){0, 0}); int cur = 0;
	while (!Q.empty()) {
		int u = Q.top().id; Q.pop();
		if (vis[u]) continue;
		vis[u] = 1; if (++cur == tot) break;
		int size = G[u].size();
		for (int i = 0; i < size; ++ i) {
			Edge e = edges[G[u][i]];
			if (!vis[e.to] && dis[u] + e.dis < dis[e.to])
				dis[e.to] = dis[u] + e.dis, Q.push((Pair){dis[e.to], e.to});
		}
	}
}

int main() {
	int n = read(), m = read(), tot = (n - 1) * (m - 1) * 2 + 1;
	if (n == 1 || m == 1) {
		if (n == 1) n = m; int ans = 0x3f3f3f3f;
		for (int i = 1; i < n; ++ i) ans = std::min(ans, read());
		printf("%d\n", ans); return 0;
	}
	for (int i = 1; i <= n; ++ i)
		for (int j = 1; j < m; ++ j) {
			int x = read();
			if (i == 1) add(0, j, x);
			else if (i == n) add(((i-1<<1)-1)*(m-1)+j, tot, x);
			else add(((i-1<<1)-1)*(m-1)+j, (i-1<<1)*(m-1)+j, x);
		}
	for (int i = 1; i < n; ++ i)
		for (int j = 1; j <= m; ++ j) {
			int x = read();
			if (j == 1) add(((i<<1)-1)*(m-1)+j, tot, x);
			else if (j == m) add(0, (i-1<<1)*(m-1)+j-1, x);
			else add((i-1<<1)*(m-1)+j-1, ((i<<1)-1)*(m-1)+j, x);
		}
	for (int i = 1; i < n; ++ i)
		for (int j = 1; j < m; ++ j) {
			int x = read();
			add((i-1<<1)*(m-1)+j, ((i<<1)-1)*(m-1)+j, x);
		}
	Dij(tot + 1);
	printf("%d\n", dis[tot]);
	return 0;
}
```
