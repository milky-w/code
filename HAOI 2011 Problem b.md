# code
科学必须一丝不苟的严谨。
```c++
#include <cstdio>
#include <string>

const int N = 50010;
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
	for (int i = 1; i <= n; ++ i) sum[i] = sum[i-1] + mu[i];
}

long long calc(int n, int m, int k) {
	long long res = 0;
	n /= k, m /= k;
	if (n > m) swap(n, m);
	int last;
	for (int i = 1; i <= n; i = last + 1) {
		last = min(n / (n / i), m / (m / i));
		res += (1LL) * (sum[last] - sum[i-1]) * (n / i) * (m / i);
	}
	return res;
}

int main() {
	int T = read();
	get_mu(N - 5);
	while (T--) {
		int a = read(), b = read(), c = read(), d = read(), k = read();
		long long ans = calc(b, d, k) - calc(a - 1, d, k) - calc(b, c - 1, k) + calc(a - 1, c - 1, k);
		printf("%lld\n", ans);
	}
	return 0;
}
```
