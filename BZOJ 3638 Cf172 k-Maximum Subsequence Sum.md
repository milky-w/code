# code
科学必须一丝不苟的严谨。

## Description

给一列数，要求支持操作： 1.修改某个数的值 2.读入l,r,k，询问在[l,r]内选不相交的不超过k个子段，最大的和是多少。

## Input

The first line contains integer n (1 ≤ n ≤ 105), showing how many numbers the sequence has. The next line contains n integers a1, a2, ..., an (|ai| ≤ 500).

The third line contains integer m (1 ≤ m ≤ 105) — the number of queries. The next m lines contain the queries in the format, given in the statement.

All changing queries fit into limits: 1 ≤ i ≤ n, |val| ≤ 500.

All queries to count the maximum sum of at most k non-intersecting subsegments fit into limits: 1 ≤ l ≤ r ≤ n, 1 ≤ k ≤ 20. It is guaranteed that the number of the queries to count the maximum sum of at most k non-intersecting subsegments doesn't exceed 10000.

## Output

For each query to count the maximum sum of at most k non-intersecting subsegments print the reply — the maximum sum. Print the answers to the queries in the order, in which the queries follow in the input.

## Sample Input

9

9 -8 9 -1 -1 -1 9 -8 9

3

1 1 9 1

1 1 9 2

1 4 6 3
## Sample Output

17

25

0
## HINT

In the first query of the first example you can select a single pair (1, 9). So the described sum will be 17.


Look at the second query of the first example. How to choose two subsegments? (1, 3) and (7, 9)? Definitely not, the sum we could get from (1, 3) and (7, 9) is 20, against the optimal configuration (1, 7) and (9, 9) with 25.


The answer to the third query is 0, we prefer select nothing if all of the numbers in the given interval are negative.

```c++
#include <cstdio>
#include <string>
#include <queue>

const int N = 1e5 + 5;

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
int v[N];

struct Pair {int l, r; };
struct data {int sum, pre, sub, suf, l, r, p, s; };
struct Segt {int tag; data mx, mn; } node[N<<2];

std::queue<Pair> Q;

void in(data &a, int p, int x) {
	a.l = a.r = a.p = a.s = p;
	a.sum = a.pre = a.sub = a.suf = x;
}

void maintain1(data &cur, data &lc, data &rc) {
	cur.sum = lc.sum + rc.sum;
	
	cur.p = lc.p, cur.s = rc.s;
	cur.pre = lc.pre, cur.suf = rc.suf;
	if (lc.sum + rc.pre > cur.pre)
		cur.pre = lc.sum + rc.pre, cur.p = rc.p;
	if (rc.sum + lc.suf > cur.suf)
		cur.suf = rc.sum + lc.suf, cur.s = lc.s;
		
	cur.l = lc.s, cur.r = rc.p;
	cur.sub = lc.suf + rc.pre;
	if (lc.sub > cur.sub)
		cur.sub = lc.sub, cur.l = lc.l, cur.r = lc.r;
	if (rc.sub > cur.sub)
		cur.sub = rc.sub, cur.l = rc.l, cur.r = rc.r;
}

void maintain2(data &cur, data &lc, data &rc) {
	cur.sum = lc.sum + rc.sum;
	
	cur.p = lc.p, cur.s = rc.s;
	cur.pre = lc.pre, cur.suf = rc.suf;
	if (lc.sum + rc.pre < cur.pre)
		cur.pre = lc.sum + rc.pre, cur.p = rc.p;
	if (rc.sum + lc.suf < cur.suf)
		cur.suf = rc.sum + lc.suf, cur.s = lc.s;
		
	cur.l = lc.s, cur.r = rc.p;
	cur.sub = lc.suf + rc.pre;
	if (lc.sub < cur.sub)
		cur.sub = lc.sub, cur.l = lc.l, cur.r = lc.r;
	if (rc.sub < cur.sub)
		cur.sub = rc.sub, cur.l = rc.l, cur.r = rc.r;
}

void build(int cur, int l, int r) {
	if (l == r) in(node[cur].mx, l, v[l]), in(node[cur].mn, l, v[l]);
	else {
		int mid = l + ((r - l) >> 1);
		build(cur << 1, l, mid);
		build(cur << 1 | 1, mid + 1, r);
		maintain1(node[cur].mx, node[cur<<1].mx, node[cur<<1|1].mx);
		maintain2(node[cur].mn, node[cur<<1].mn, node[cur<<1|1].mn);
	}
}

void swap(data &a, data &b) {
	data t = a; a = b; b = t;
	a.sum = -a.sum, a.pre = -a.pre, a.sub = -a.sub, a.suf = -a.suf;
	b.sum = -b.sum, b.pre = -b.pre, b.sub = -b.sub, b.suf = -b.suf;
}

void pushdown(int cur, int l, int r) {
	if (l < r) {
		node[cur<<1].tag ^= 1, swap(node[cur<<1].mx, node[cur<<1].mn);
		node[cur<<1|1].tag ^= 1, swap(node[cur<<1|1].mx, node[cur<<1|1].mn);
	}
	node[cur].tag = 0;
}

data query(int cur, int l, int r, int L, int R) {
	if (L <= l && r <= R) return node[cur].mx;
	if (node[cur].tag) pushdown(cur, l, r);
	int mid = l + ((r - l) >> 1);
	data res, ls, rs;
	if (L <= mid) ls = query(cur << 1, l, mid, L, R);
	if (mid < R) rs = query(cur << 1 | 1, mid + 1, r, L, R);
	maintain1(node[cur].mx, node[cur<<1].mx, node[cur<<1|1].mx);
	maintain2(node[cur].mn, node[cur<<1].mn, node[cur<<1|1].mn);
	if (L <= mid && mid < R) { maintain1(res, ls, rs); return res; }
	if (L <= mid) return ls; return rs;
}

void reverse(int cur, int l, int r, int L, int R) {
	if (L <= l && r <= R) {
		node[cur].tag ^= 1;
		swap(node[cur].mx, node[cur].mn);
	} else {
		if (node[cur].tag) pushdown(cur, l, r);
		int mid = l + ((r - l) >> 1);
		if (L <= mid) reverse(cur << 1, l, mid, L, R);
		if (mid < R) reverse(cur << 1 | 1, mid + 1, r, L, R);
		maintain1(node[cur].mx, node[cur<<1].mx, node[cur<<1|1].mx);
		maintain2(node[cur].mn, node[cur<<1].mn, node[cur<<1|1].mn);
	}
}

void update(int cur, int l, int r, int p, int x) {
	if (l == r) in(node[cur].mx, l, x), in(node[cur].mn, l, x);
	else {
		if (node[cur].tag) pushdown(cur, l, r);
		int mid = l + ((r - l) >> 1);
		if (p <= mid) update(cur << 1, l, mid, p, x);
		else update(cur << 1 | 1, mid + 1, r, p, x);
		maintain1(node[cur].mx, node[cur<<1].mx, node[cur<<1|1].mx);
		maintain2(node[cur].mn, node[cur<<1].mn, node[cur<<1|1].mn);
	}
}

int main() {
	int n = read();
	for (int i = 1; i <= n; ++ i) v[i] = read();
	build(1, 1, n);
	int m = read();
	while (m--) {
		int opt = read(), l = read(), r = read();
		if (opt == 1) {
			int k = read(), ans = 0;
			while (k--) {
				data tmp = query(1, 1, n, l, r);
				if (tmp.sub <= 0) break;
				ans += tmp.sub;
				reverse(1, 1, n, tmp.l, tmp.r);
				Q.push((Pair){tmp.l, tmp.r});
			}
			while (!Q.empty()) {
				reverse(1, 1, n, Q.front().l, Q.front().r);
				Q.pop();
			}
			printf("%d\n", ans);
		} else update(1, 1, n, l, r);
	}
	return 0;
}
```
