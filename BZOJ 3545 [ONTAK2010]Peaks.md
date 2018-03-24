# code
科学必须一丝不苟的严谨, 探索真理必须谦虚和自信。
```c++
#include <cstdio>
#include <string>
#include <algorithm>

const int N = 100005;

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

int n, m, q, h[N], b[N], fa[N], cnt, ans[N*5];
int sum[N<<5], lson[N<<5], rson[N<<5], root[N];

struct edge {
	int a, b, c, id, flag;
	bool operator < (const edge &cmp) const {
		if (c == cmp.c) return flag < cmp.flag;
		return c < cmp.c;
	}
} e[N*10];

int find(int x) {
	if (x != fa[x]) fa[x] = find(fa[x]);
	return fa[x];
}

void insert(int &cur, int l, int r, int x) {
	if (!cur) cur = ++cnt;
	sum[cur] = 1;
	if (l == r) return;
	int mid = l + ((r - l) >> 1);
	if (x <= mid) insert(lson[cur], l, mid, x);
	else insert(rson[cur], mid + 1, r, x);
}

int merge(int x, int y) {
	if (!x) return y;
	if (!y) return x;
	sum[x] += sum[y];
	lson[x] = merge(lson[x], lson[y]);
	rson[x] = merge(rson[x], rson[y]);
	return x;
}

int query(int cur, int l, int r, int k) {
	if (l == r) return l;
	int mid = l + ((r - l) >> 1);
	if (sum[lson[cur]] >= k) return query(lson[cur], l, mid, k);
	else return query(rson[cur], mid + 1, r, k - sum[lson[cur]]);
}

void solve() {
	for (int i = 1; i <= n; ++ i) insert(root[i], 1, n, h[i]);
	for (int i = 1; i <= m + q; ++ i) {
		if (e[i].flag) {
			int x = find(e[i].a);
			if (sum[root[x]] < e[i].b) ans[e[i].id] = -1;
			else ans[e[i].id] = b[query(root[x], 1, n, sum[root[x]] - e[i].b + 1)];
		} else {
			int x = find(e[i].a), y = find(e[i].b);
			if (x != y) {
				fa[y] = x;
				root[x] = merge(root[x], root[y]);
			}
		}
	}
}

int main() {
	n = read(), m = read(), q = read();
	for (int i = 1; i <= n; ++ i) h[i] = b[i] = read();
	std::sort(b + 1, b + n + 1);
	for (int i = 1; i <= n; ++ i)
		h[i] = std::lower_bound(b + 1, b + n + 1, h[i]) - b;
	for (int i = 1; i <= n; ++ i) fa[i] = i;
	for (int i = 1; i <= m; ++ i)
		e[i].a = read(), e[i].b = read(), e[i].c = read();
	for (int i = m + 1; i <= m + q; ++ i) 
		e[i].flag = 1, e[i].id = i - m, e[i].a = read(), e[i].c = read(), e[i].b = read();
	std::sort(e + 1, e + m + q + 1);
	solve();
	for (int i = 1; i <= q; ++ i) printf("%d\n", ans[i]);
	return 0;
}
```
