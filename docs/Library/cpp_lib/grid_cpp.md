# グリッド操作

```cpp
struct P {
    int y, x;
    P() : y(0), x(0) {
    }
    P(int y, int x) : y(y), x(x) {
    }
    bool operator<(const P& other) const {
        if (y == other.y) return x < other.x;
        return y < other.y;
    }
    bool operator>(const P& other) const {
        return other < *this;
    }
    bool operator==(const P& other) const {
        return y == other.y && x == other.x;
    }
};
ostream& operator<<(ostream& os, const P& p) {
    os << "(" << p.y << "," << p.x << ")";
    return os;
}

template <class T>
struct Array2D {
    static_assert(!is_same<T, bool>::value, "use int8_t instead of bool");
    const int H, W;
    vector<T> v;
    Array2D(int H, int W) : H(H), W(W), v(H * W, T()) {
    }
    void clear() {
        v.assign(H * W, T());
    }
    const T& operator[](const P& p) const {
        return v[p.y * W + p.x];
    }
    T& operator[](const P& p) {
        return v[p.y * W + p.x];
    }
};
using Grid = Array2D<int>;

template <class T>
class FastClearingArray2D {
    T base;
    T mx;
    Array2D<T> v;

   public:
    FastClearingArray2D(int H, int W) : base(1), mx(0), v(H, W) {
    }
    const T operator[](const P& p) const {
        return get(p);
    }
    const T get(const P& p) const {
        if (v[p] >= base) return v[p] - base;
        return -1;
    }
    void set(const P& p, const T& x) {
        assert(x >= 0);
        v[p] = x + base;
        mx = max(mx, v[p]);
    }
    void clear() {
        base = mx + 1;
        if (base > 1e9) {
            base = 1;
            mx = 0;
            v.clear();
        }
    }
};
```

```cpp
// 方向操作
enum Angle { U = 0, L = 1, D = 2, R = 3 };
const string angle = "ULDR";
const string rev_angle = "DRUL";
static constexpr int vy[4] = {-1, 0, 1, 0};
static constexpr int vx[4] = {0, -1, 0, 1};
int rev(int k) {
    return (k + 2) % 4;
}
using Dir = array<bool, 4>;
P get_dir_pos(const P& p, int k) {
    return P(p.y + vy[k], p.x + vx[k]);
}
```
