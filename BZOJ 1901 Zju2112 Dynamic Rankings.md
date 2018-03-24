# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>
#include <algorithm>

const int N = 10005;

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

struct data {
	int x, y, k;
} b[N];

int a[N], sta[N<<1], top, hash[N<<1], tot, L[30], R[30], ln, rn;
int root[N<<1], sum[N<<8], lson[N<<8], rson[N<<8], cnt;

int lowbit(int x) {
	return x & (-x);
}

int find(int x) {
	int l = 1, r = tot;
	while (l <= r) {
		int mid = l + ((r - l) >> 1);
		if (hash[mid] < x) l = mid + 1;
		else if (hash[mid] == x) return mid;
		else r = mid - 1;
	}
}

void update(int pre, int &cur, int l, int r, int pos, int x) {
	cur = ++cnt, sum[cur] = sum[pre] + x;
	lson[cur] = lson[pre], rson[cur] = rson[pre];
	if (l == r) return;
	int mid = l + ((r - l) >> 1);
	if (pos <= mid) update(lson[pre], lson[cur], l, mid, pos, x);
	else update(rson[pre], rson[cur], mid + 1, r, pos, x);
}

int query(int l, int r, int k) {
	if (l == r) return l;
	int suml = 0, sumr = 0;
	for (int i = 1; i <= ln; ++ i) suml += sum[lson[L[i]]];
	for (int i = 1; i <= rn; ++ i) sumr += sum[lson[R[i]]];
	int mid = l + ((r - l) >> 1);
	if (sumr - suml >= k) {
		for (int i = 1; i <= ln; ++ i) L[i] = lson[L[i]];
		for (int i = 1; i <= rn; ++ i) R[i] = lson[R[i]];
		return query(l, mid, k);
	} else {
		for (int i = 1; i <= ln; ++ i) L[i] = rson[L[i]];
		for (int i = 1; i <= rn; ++ i) R[i] = rson[R[i]];
		return query(mid + 1, r, k - (sumr - suml));
	}
}

int main() {
	int n = read(), m = read();
	for (int i = 1; i <= n; ++ i)
		sta[++top] = a[i] = read();
	for (int i = 1; i <= m; ++ i) {
		char opt = getchar();
		while (opt != 'Q' && opt != 'C') opt = getchar();
		b[i].x = read(), b[i].y = read();
		if (opt == 'Q') b[i].k = read();
		else sta[++top] = b[i].y;
	}
	std::sort(sta + 1, sta + top + 1);
	hash[++tot] = sta[1];
	for (int i = 2; i <= top; ++ i)
		if (sta[i] != sta[i-1]) hash[++tot] = sta[i];
	for (int i = 1; i <= n; ++ i) {
		int t = find(a[i]);
		for (int j = i; j <= n; j += lowbit(j))	
			update(root[j], root[j], 1, tot, t, 1);
	}
	for (int i = 1; i <= m; ++ i) {
		if (b[i].k) {
			ln = 0, rn = 0, --b[i].x;
			for (int j = b[i].x; j; j -= lowbit(j))
				L[++ln] = root[j];
			for (int j = b[i].y; j; j -= lowbit(j))
				R[++rn] = root[j];
			printf("%d\n", hash[query(1, tot, b[i].k)]);
		} else {
			int t = find(a[b[i].x]);
			for (int j = b[i].x; j <= n; j += lowbit(j))
				update(root[j], root[j], 1, tot, t, -1);
			a[b[i].x] = b[i].y, t = find(b[i].y);
			for (int j = b[i].x; j <= n; j += lowbit(j))
				update(root[j], root[j], 1, tot, t, 1);
		}
	}
	return 0;
}
```
