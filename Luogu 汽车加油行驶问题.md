# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <queue>
#include <vector>

const int N = 200000, INF = 0x3f3f3f3f;

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

struct Pair {
	int val, id;
	bool operator > (const Pair &cmp) const {
		return val > cmp.val;
	}
};
struct Edge {
	int to, cost;
};
std::vector<Edge> edges;
std::vector<int> G[N];
int id[105][105][15], mat[105][105], dis[N], dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1}, cnt;
bool vis[N];
std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair> > Q;

void addedge(int from, int to, int cost) {
	G[from].push_back(edges.size());
	edges.push_back((Edge){to, cost});
}

void Dij(int S) {
	memset(dis, 0x3f, sizeof dis); dis[S] = 0;
	int cur = 0; Q.push((Pair){0, S});
	while (!Q.empty()) {
		int u = Q.top().id; Q.pop();
		if (vis[u]) continue;
		vis[u] = 1; if (++cur == cnt) break;
		int size = G[u].size();
		for (int i = 0; i < size; ++ i) {
			Edge e = edges[G[u][i]];
			if (!vis[e.to] && dis[e.to] > dis[u] + e.cost) {
				dis[e.to] = dis[u] + e.cost;
				Q.push((Pair){dis[e.to], e.to});
			}
		}
	}
}

int main() {
	int n = read(), K = read(), A = read(), B = read(), C = read();
	for (int i = 1; i <= n; ++ i)
		for (int j = 1; j <= n; ++ j)
			for (int k = 0; k <= K; ++ k) id[i][j][k] = ++cnt;
	for (int i = 1; i <= n; ++ i)
		for (int j = 1; j <= n; ++ j) mat[i][j] = read();
	for (int i = 1; i <= n; ++ i)
		for (int j = 1; j <= n; ++ j)
			for (int r = 0; r < 4; ++ r) {
				int x = i + dx[r], y = j + dy[r];
				if (x < 1 || x > n || y < 1 || y > n) continue;
				int b = (x < i || y < j) ? B : 0;
				if (mat[i][j]) {
					if (mat[x][y]) addedge(id[i][j][K], id[x][y][K], A + b);
					else addedge(id[i][j][K], id[x][y][K-1], b);
				} else if (mat[x][y]) {
					for (int k = 1; k <= K; ++ k)
						addedge(id[i][j][k], id[x][y][K], A + b);
					addedge(id[i][j][0], id[x][y][K], A + b + A + C);
				} else {
					for (int k = 1; k <= K; ++ k)
						addedge(id[i][j][k], id[x][y][k-1], b);
					addedge(id[i][j][0], id[x][y][K-1], b + A + C);
				}
			}
	Dij(K + 1);
	int ans = INF;
	for (int i = 0; i <= K; ++ i) ans = min(ans, dis[id[n][n][i]]);
	printf("%d\n", ans);
	return 0;
}
```
