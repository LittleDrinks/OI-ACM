```cpp
namespace NTT {
const int g = 3;
vector<int> Omega(int L) {
    int wn = qpow(g, MOD / L);
    vector<int> w(L);
    w[L >> 1] = 1;
    for (int i = L / 2 + 1; i <= L - 1; ++i) {
        w[i] = mul(w[i - 1], wn);
    }
    for (int i = L / 2 - 1; i; --i) {
        w[i] = w[i << 1];
    }
    return w;
}
auto W = Omega(1 << 21);
void DIF(int *a, int n) {
    for (int k = n >> 1; k; k >>= 1) {
        for (int i = 0, y; i < n; i += k << 1) {
            for (int j = 0; j < k; ++j) {
                y = a[i + j + k];
                a[i + j + k] = mul(del(a[i + j], y), W[k + j]);
                a[i + j] = add(a[i + j], y);
            }
        }
    }
}
void IDIT(int *a, int n) {
    for (int k = 1; k < n; k <<= 1) {
        for (int i = 0, x, y; i < n; i += k << 1) {
            for (int j = 0; j < k; ++j) {
                x = a[i + j];
                y = mul(a[i + j + k], W[k + j]);
                a[i + j + k] = del(x, y);
                a[i + j] = add(a[i + j], y);
            }
        }
    }
    int Inv = MOD - (MOD - 1) / n;
    for (int i = 0; i < n; ++i) {
        a[i] = mul(a[i], Inv);
    }
    reverse(a + 1, a + n);
}
} // namespace NTT
namespace Polynomial {
using Poly = std::vector<int>;
Poly &operator*=(Poly &a, int b) {
    for (auto &x : a) {
        x = mul(x, b);
    }
    return a;
}
Poly operator*(Poly a, int b) {
    return a *= b;
}
Poly operator*(int a, Poly b) {
    return b * a;
}
void DFT(Poly &a) {
    NTT::DIF(a.data(), a.size());
}
void IDFT(Poly &a) {
    NTT::IDIT(a.data(), a.size());
}
int norm(int n) {
    return 1 << (32 - __builtin_clz(n - 1));
}
void norm(Poly &a) {
    if (!a.empty()) {
        a.resize(norm(a.size()), 0);
    }
}
Poly &dot(Poly &a, Poly &b) {
    for (int i = 0; i < int(a.size()); ++i) {
        a[i] = mul(a[i], b[i]);
    }
    return a;
}
Poly operator*(Poly a, Poly b) {
    int n = a.size() + b.size() - 1, L = norm(n);
    if (a.size() <= 8 || b.size() <= 8) {
        Poly c(n);
        for (int i = 0; i < int(a.size()); ++i) {
            for (int j = 0; j < int(b.size()); ++j) {
                c[i + j] = add(c[i + j], mul(a[i], b[j]));
            }
        }
        return c;
    }
    a.resize(L);
    b.resize(L);
    DFT(a);
    DFT(b);
    dot(a, b);
    IDFT(a);
    a.resize(n);
    return a;
}
} // namespace Polynomial
using namespace Polynomial;
```
