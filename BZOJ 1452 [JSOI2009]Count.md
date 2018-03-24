# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>

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

int n, m, f[105][305][305], mat[305][305];

int lowbit(int x) { return x & (-x); }

void update(int x, int y, int c, int val) {
	for (int i = x; i <= n; i += lowbit(i))
		for (int j = y; j <= m; j += lowbit(j))
			f[c][i][j] += val;
}

int query(int x, int y, int c) {
	int res = 0;
	for (int i = x; i; i -= lowbit(i))
		for (int j = y; j; j -= lowbit(j))
			res += f[c][i][j];
	return res;
}

int main() {
	n = read(), m = read();
	for (int i = 1; i <= n; ++ i)
		for (int j = 1; j <= m; ++ j) {
			mat[i][j] = read();
			update(i, j, mat[i][j], 1);
		}
	int q = read();
	while (q--) {
		int opt = read();
		if (opt == 1) {
			int x = read(), y = read(), c = read();
			update(x, y, mat[x][y], -1);
			mat[x][y] = c;
			update(x, y, c, 1);
		} else {
			int x1 = read(), x2 = read(), y1 = read(), y2 = read(), c = read();
			int ans = query(x2, y2, c) + query(x1 - 1, y1 - 1, c);
			ans -= query(x1 - 1, y2, c) + query(x2, y1 - 1, c);
			printf("%d\n", ans);
		}
	}
	return 0;
}
```
