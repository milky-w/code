# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <cstring>

const int N = 2000010;
char s[1000010];
int last = 1, sz = 1, a[N], c[N], dis[N], val[N], ch[N][26], fa[N];

long long max(long long x, long long y) {
	if (x >= y) return x; return y;
}
void insert(int c) {
	int pre = last, now = ++sz;
	dis[now] = dis[pre] + 1, val[now] = 1, last = now;
	for (; pre && !ch[pre][c]; pre = fa[pre]) ch[pre][c] = now;
	if (!pre) fa[now] = 1;
	else {
		int old = ch[pre][c];
		if (dis[old] == dis[pre] + 1) fa[now] = old;
		else {
			int news = ++sz; dis[news] = dis[pre] + 1;
			memcpy(ch[news], ch[old], sizeof ch[old]);
			fa[news] = fa[old], fa[now] = fa[old] = news;
			for (; pre && ch[pre][c] == old; pre = fa[pre]) ch[pre][c] = news;
		}
	}
}
int main() {
	scanf("%s", s);
	int n = strlen(s);
	for (int i = 0; i < n; ++i) insert(s[i] - 'a');
	for (int i = 1; i <= sz; ++i) ++c[dis[i]];
	for (int i = 2; i <= sz; ++i) c[i] += c[i-1];
	for (int i = 1; i <= sz; ++i) a[c[dis[i]]--] = i;
	long long ans = 0;
	for (int i = sz; i; --i) {
		int x = a[i];
		val[fa[x]] += val[x];
		if (val[x] > 1) ans = max(ans, (1LL) * val[x] * dis[x]);
	}
	printf("%lld\n", ans);
	return 0;
}
```
