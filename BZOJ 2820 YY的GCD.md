# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>

const int N = 10000010;
int mu[N], prime[N], tot, sum[N];
bool notPrime[N];

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
void swap(int &x, int &y) {
	x ^= y, y ^= x, x ^= y;
}
void get_mu(int n) {
	notPrime[1] = mu[1] = 1;
	for (int i = 2; i <= n; ++ i) {
		if (!notPrime[i]) prime[++tot] = i, mu[i] = -1;
		for (int j = 1; prime[j] * i <= n; ++ j) {
			notPrime[prime[j]*i] = 1;
			if (i % prime[j] == 0) {
				mu[prime[j]*i] = 0;
				break;
			}
			mu[prime[j]*i] = -mu[i];
		}
	}
}
void get_sum(int n) {
	for (int i = 1; i <= tot; ++ i)
		for (int j = prime[i]; j <= n; j += prime[i])
			sum[j] += mu[j / prime[i]];
	for (int i = 2; i <= n; ++ i) sum[i] += sum[i-1];
}

int main() {
	int T = read();
	get_mu(N - 5), get_sum(N - 5);
	while (T--) {
		int n = read(), m = read();
		if (n > m) swap(n, m);
		long long ans = 0;
		for (int i = 1, last; i <= n; i = last + 1) {
			last = min(n / (n / i), m / (m / i));
			ans += (1LL) * (sum[last] - sum[i-1]) * (n / i) * (m / i);
		}
		printf("%lld\n", ans);
	}
	return 0;
}
```
