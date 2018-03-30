# code
科学必须一丝不苟的严谨。
```c++
/*--------------------------------------
	将字符串复制一倍，计算sa数组
	最后答案为s[sa[i]+n-1](sa[i] < n) 
	！！！注意数组大小乘2 
--------------------------------------*/

#include <cstdio>
#include <string>
#include <cstring>

const int N = 200010;
int n, m = 130, x[N], y[N], sa[N], c[N];
char s[N];

void get_sa() {
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

int main() {
	scanf("%s", s), n = strlen(s);
	for (int i = 0; i < n; ++ i) s[i+n] = s[i];
	int t = n; n *= 2, get_sa();
	for (int i = 0; i < n; ++ i)
		if (sa[i] < t) printf("%c", s[sa[i]+t-1]);
	puts(""); return 0;
}
```
