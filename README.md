# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>

const int N = 500005, INF = 0x3f3f3f3f;
int f[N], ch[N][2], a[N], root, siz[N], bin[N], back;
int sum[N], val[N], tag1[N], tag2[N], pre[N], sub[N], suf[N];

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
int max(int x, int y) {
	if (x >= y) return x; return y;
}
void swap(int &x, int &y) {
	x ^= y, y ^= x, x ^= y;
}
int newNode() {
	static int cnt = 0;
	if (back) return bin[--back];
	return ++cnt;
}
int get(int x) {
	return ch[f[x]][1] == x;
}
void maintain(int x) {
	siz[x] = siz[ch[x][0]] + siz[ch[x][1]] + 1;
	sum[x] = sum[ch[x][0]] + sum[ch[x][1]] + val[x];
	pre[x] = max(pre[ch[x][0]], sum[ch[x][0]] + val[x] + max(0, pre[ch[x][1]]));
	suf[x] = max(suf[ch[x][1]], sum[ch[x][1]] + val[x] + max(0, suf[ch[x][0]]));
	sub[x] = max(max(sub[ch[x][0]], sub[ch[x][1]]), max(0, suf[ch[x][0]]) + val[x] + max(0, pre[ch[x][1]]));
}
void update1(int x) {
	tag1[x] ^= 1, swap(ch[x][0], ch[x][1]), swap(pre[x], suf[x]);
}
void update2(int x, int c) {
	tag1[x] = 0, tag2[x] = 1;
	val[x] = c, sum[x] = siz[x] * c;
	pre[x] = suf[x] = sub[x] = max(c, sum[x]);
}
void pushdown(int x) {
	if (tag1[x]) {
		if (ch[x][0]) update1(ch[x][0]);
		if (ch[x][1]) update1(ch[x][1]);
		tag1[x] = 0;
	}
	if (tag2[x]) {
		if (ch[x][0]) update2(ch[x][0], val[x]);
		if (ch[x][1]) update2(ch[x][1], val[x]);
		tag2[x] = 0;
	}
}
int find(int x, int k) {
	while (1) {
		pushdown(x);
		if (siz[ch[x][0]] >= k) x = ch[x][0];
		else {
			k -= siz[ch[x][0]] + 1;
			if (!k) return x;
			x = ch[x][1];
		}
	}
}
void build(int &cur, int fa, int l, int r) {
	if (l > r) return;
	cur = newNode();
	int mid = l + ((r - l) >> 1);
	siz[cur] = 1, f[cur] = fa, tag1[cur] = tag2[cur] = 0;
	sum[cur] = val[cur] = pre[cur] = suf[cur] = sub[cur] = a[mid];
	build(ch[cur][0], cur, l, mid - 1);
	build(ch[cur][1], cur, mid + 1, r);
	maintain(cur);
}
void rotate(int x) {
	int fa = f[x], fafa = f[fa], d = get(x);
	pushdown(fa), pushdown(x);
	ch[fa][d] = ch[x][d^1], f[ch[fa][d]] = fa;
	ch[x][d^1] = fa, f[fa] = x, f[x] = fafa;
	if (fafa) ch[fafa][ch[fafa][1] == fa] = x;
	maintain(fa), maintain(x);
}
void splay(int x, int tar) {
	for (int fa; (fa = f[x]) != tar; rotate(x))
		if (f[fa] != tar) rotate(get(fa) == get(x) ? fa : x);
	if (!tar) root = x;
}
void insert() {
	int p = read(), n = read();
	for (int i = 1; i <= n; ++ i) a[i] = read();
	int x = find(root, p + 1); splay(x, 0);
	int y = find(ch[x][1], 1); splay(y, x);
	build(ch[y][0], y, 1, n);
	maintain(y), maintain(x);
}
void free(int &x) {
	bin[back++] = x;
	if (ch[x][0]) free(ch[x][0]);
	if (ch[x][1]) free(ch[x][1]);
	x = 0;
}
void del() {
	int p = read(), n = read();
	int x = find(root, p); splay(x, 0);
	int y = find(ch[x][1], n + 1); splay(y, x);
	if (ch[y][0]) free(ch[y][0]);
	maintain(y), maintain(x);
}
void reverse() {
	int p = read(), n = read();
	int x = find(root, p); splay(x, 0);
	int y = find(ch[x][1], n + 1); splay(y, x);
	if (ch[y][0]) update1(ch[y][0]);
	maintain(y), maintain(x);
}
void make_same() {
	int p = read(), n = read(), c = read();
	int x = find(root, p); splay(x, 0);
	int y = find(ch[x][1], n + 1); splay(y, x);
	if (ch[y][0]) update2(ch[y][0], c);
	maintain(y), maintain(x);
}
int get_sum() {
	int p = read(), n = read();
	int x = find(root, p); splay(x, 0);
	int y = find(ch[x][1], n + 1); splay(y, x);
	return sum[ch[y][0]];
}
int max_sum() {
	return sub[root];
}

int main() {
	int n = read(), m = read();
	for (int i = 1; i <= n; ++ i) a[i+1] = read();
	a[1] = a[n+2] = pre[0] = suf[0] = sub[0] = -INF;
	build(root, 0, 1, n + 2);
	char opt[15] = {};
	while (m--) {
		scanf("%s", opt);
		if (opt[2] == 'S') insert();
		else if (opt[2] == 'L') del();
		else if (opt[2] == 'K') make_same();
		else if (opt[2] == 'V') reverse();
		else if (opt[2] == 'T') printf("%d\n", get_sum());
		else printf("%d\n", max_sum());
	}
	return 0;
}
```
