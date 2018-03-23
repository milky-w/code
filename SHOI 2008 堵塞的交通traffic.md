# code
科学必须一丝不苟的严谨。
```c++
/*
线段树结点记录[l,r]的矩形, mid=(l+r)/2 
ok[0] -> 第一行mid与mid+1是否连通 
ok[1] -> 第二行mid与mid+1是否连通 
对于矩形内部连通性 
1----2 -> (1&2)->ok[2], (1&3)->ok[3]
|    | -> (1&4)->ok[4], (2&3)->ok[5]
3----4 -> (2&4)->ok[6], (3&4)->ok[7]
另外, 线段树的叶子结点只保存一纵列
*/

#include <cstdio>
#include <string>
#include <cstring>

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

void swap(int &x, int &y) { x ^= y, y ^= x, x ^= y; }

struct Node { int ok[8]; } node[N<<2];

void maintain(Node &cur, Node lc, Node rc) {
	cur.ok[3] = lc.ok[3] | (lc.ok[2] & cur.ok[0] & rc.ok[3] & cur.ok[1] & lc.ok[7]);
	cur.ok[6] = rc.ok[6] | (rc.ok[2] & cur.ok[0] & lc.ok[6] & cur.ok[1] & rc.ok[7]);
	
	cur.ok[2] = (lc.ok[2] & cur.ok[0] & rc.ok[2]) | (lc.ok[4] & cur.ok[1] & rc.ok[5]);
	cur.ok[7] = (lc.ok[7] & cur.ok[1] & rc.ok[7]) | (lc.ok[5] & cur.ok[0] & rc.ok[4]);
	
	cur.ok[4] = (lc.ok[4] & cur.ok[1] & rc.ok[7]) | (lc.ok[2] & cur.ok[0] & rc.ok[4]);
	cur.ok[5] = (lc.ok[5] & cur.ok[0] & rc.ok[2]) | (lc.ok[7] & cur.ok[1] & rc.ok[5]);
}

void build(int cur, int l, int r) {
	if (l == r) {
		node[cur].ok[0] = node[cur].ok[1] = 1;
		node[cur].ok[2] = node[cur].ok[7] = 1;
	} else {
		int mid = l + ((r - l) >> 1);
		build(cur << 1, l, mid);
		build(cur << 1 | 1, mid + 1, r);
	}
}

void update1(int cur, int l, int r, int row, int col, int flag) { //for same row
	int mid = l + ((r - l) >> 1);
	if (mid == col) {
		node[cur].ok[row] = flag;
	} else {
		if (col < mid) update1(cur << 1, l, mid, row, col, flag);
		else update1(cur << 1 | 1, mid + 1, r, row, col ,flag);
	}
	if (l < r) maintain(node[cur], node[cur<<1], node[cur<<1|1]);
}

void update2(int cur, int l, int r, int col, int flag) { //for same column
	if (l == r) {
		node[cur].ok[3] = node[cur].ok[6] = flag;
		node[cur].ok[4] = node[cur].ok[5] = flag;
	} else {
		int mid = l + ((r - l) >> 1);
		if (col <= mid) update2(cur << 1, l, mid, col, flag);
		else update2(cur << 1 | 1, mid + 1, r, col ,flag);
		maintain(node[cur], node[cur<<1], node[cur<<1|1]);
	}
}

Node query(int cur, int l, int r, int L, int R) {
	if (L <= l && r <= R) return node[cur];
	int mid = l + ((r - l) >> 1);
	if (R <= mid) return query(cur << 1, l, mid, L, R);
	if (L > mid) return query(cur << 1 | 1, mid + 1, r, L, R);
	else {
		Node res = node[cur];
		maintain(res, query(cur << 1, l, mid, L, R), query(cur << 1 | 1, mid + 1, r, L, R));
		return res;
	}
}

int main() {
	int n = read(); char opt[10] = {};
	build(1, 1, n);
	while (1) {
		scanf("%s", opt);
		if (opt[0] == 'E') break;
		int x1 = read(), y1 = read(), x2 = read(), y2 = read();
		if (y1 > y2) swap(x1, x2), swap(y1, y2);
		if (opt[0] == 'C') {
			if (x1 == x2) update1(1, 1, n, x1-1, y1, 0);
			else update2(1, 1, n, y1, 0);
		} else if (opt[0] == 'O') {
			if (x1 == x2) update1(1, 1, n, x1-1, y1, 1);
			else update2(1, 1, n, y1, 1);
		} else {
			int ans = 0;
			Node a = query(1, 1, n, 1, y1),
				 b = query(1, 1, n, y1, y2),
				 c = query(1, 1, n, y2, n);
			if (x1 == 1 && x2 == 1)
				ans = b.ok[2] | (a.ok[6] & b.ok[5]) | (c.ok[3] & b.ok[4]) | (a.ok[6] & b.ok[7] & c.ok[3]);
			else if (x1 == 2 && x2 == 2) 
				ans = b.ok[7] | (a.ok[6] & b.ok[4]) | (c.ok[3] & b.ok[5]) | (a.ok[6] & b.ok[2] & c.ok[3]);
			else if (x1 == 1 && x2 == 2)
				ans = b.ok[4] | (a.ok[6] & b.ok[7]) | (c.ok[3] & b.ok[2]) | (a.ok[6] & b.ok[5] & c.ok[3]);
			else
				ans = b.ok[5] | (a.ok[6] & b.ok[2]) | (c.ok[3] & b.ok[7]) | (a.ok[6] & b.ok[4] & c.ok[3]);
			puts(ans ? "Y" : "N");
		}
	}
	return 0;
}
```
