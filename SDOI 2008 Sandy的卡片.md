# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>
#include <cstring>

const int N = 102005, INF = 0x3f3f3f3f;

int n, m, t;
int a[1005][1005], sa[N], c[N], x[N], y[N], s[N];
int rank[N], height[N], vis[N], sta[N], top, id[N];

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

int min(int x, int y) { if (x <= y) return x; return y; }
int max(int x, int y) { if (x >= y) return x; return y; }

void getSa() {
	for (int i = 0; i < m; ++ i) c[i] = 0;
	for (int i = 0; i < n; ++ i) ++c[x[i] = s[i]];
	for (int i = 1; i < m; ++ i) c[i] += c[i-1];
	for (int i = n - 1; i >= 0; -- i) sa[--c[x[i]]] = i;
	
	for (int k = 1; k <= n; k <<= 1) {
		int p = 0;
		for (int i = n - k; i < n; ++ i) y[p++] = i;
		for (int i = 0; i < n; ++ i) if (sa[i] >= k) y[p++] = sa[i] - k;
		
		for (int i = 0; i < m; ++ i) c[i] = 0;
		for (int i = 0; i < n; ++ i) ++c[x[y[i]]];
		for (int i = 1; i < m; ++ i) c[i] += c[i-1];
		for (int i = n - 1; i >= 0; -- i) sa[--c[x[y[i]]]] = y[i];
		
		for (int i = 0; i < n; ++ i) x[i] ^= y[i], y[i] ^= x[i], x[i] ^= y[i];
		p = 1, x[sa[0]] = 0;
		for (int i = 1; i < n; ++ i)
			x[sa[i]] = y[sa[i]] == y[sa[i-1]] && y[sa[i]+k] == y[sa[i-1]+k] ? p-1 : p++;
		if (p >= n) break;
		m = p;
	}
}

void getHeight() {
	int k = 0;
	for (int i = 0; i < n; ++ i) rank[sa[i]] = i;
	for (int i = 0; i < n; ++ i) {
		if (!rank[i]) continue;
		if (k) --k;
		int j = sa[rank[i]-1];
		while (i+k < n && j+k < n && s[i+k] == s[j+k]) ++k;
		height[rank[i]] = k;
	}
}

bool check(int x) {
	while (top) vis[sta[top--]] = 0;
	for (int i = 0; i < n; ++ i) {
		if (height[i] < x) while(top) vis[sta[top--]] = 0;
		if (!vis[id[sa[i]]]) {
			vis[id[sa[i]]] = 1;
			sta[++top] = id[sa[i]];
			if (top == t) return true;
		}
	}
	return false;
}

int main() {
	freopen("card.in", "r", stdin);
	freopen("card.out", "w", stdout);
	
	t = read(); int l = 0, r = INF, minn = INF, maxn = 0;
	for (int i = 1; i <= t; ++ i) {
		a[i][0] = read(), a[i][1] = read();
		r = min(r, a[i][0] - 1);
		for (int j = 2; j <= a[i][0]; ++ j) {
			a[i][j] = read();
			minn = min(minn, a[i][j] - a[i][j-1]);
			maxn = max(maxn, a[i][j] - a[i][j-1]);
		}
	}
	for (int i = 1; i <= t; ++ i) {
		for (int j = 2; j <= a[i][0]; ++ j)
			id[n] = i, s[n++] = a[i][j] - a[i][j-1] - minn;
		s[n++] = ++maxn - minn;
	}
	for (int i = 0; i < n; ++ i) m = max(m, s[i] + 1);
	getSa(), getHeight();
	while (l < r) {
		int mid = l + ((r - l + 1) >> 1);
		if (check(mid)) l = mid; else r = mid - 1;
	}
	printf("%d\n", l + 1); return 0;
}
```
