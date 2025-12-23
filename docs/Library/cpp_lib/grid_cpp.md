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
    struct Row {
        T* row_data;
        int w;
        Row(T* row_data, int w) : row_data(row_data), w(w) {
        }
        T& operator[](int x) {
            return row_data[x];
        }
        const T& operator[](int x) const {
            return row_data[x];
        }
    };
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
    Row operator[](int y) {
        return Row(&v[y * W], W);
    }
    const Row operator[](int y) const {
        return Row(const_cast<T*>(&v[y * W]), W);
    }
};
using Grid = Array2D<int>;

template <class T>
class FastClearingArray2D {
    T base;
    T mx;
    Array2D<T> v;

    struct CellProxy {
        FastClearingArray2D* self;
        P p;
        operator T() const {
            return self->get(p);
        }
        CellProxy& operator=(const T& x) {
            self->set(p, x);
            return *this;
        }
    };

    struct RowProxy {
        FastClearingArray2D* self;
        int y;
        CellProxy operator[](int x) {
            return CellProxy{self, P(y, x)};
        }
    };

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
    RowProxy operator[](int y) {
        return RowProxy{this, y};
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
static constexpr int vy[4] = {-1, 0, 1, 0};
static constexpr int vx[4] = {0, -1, 0, 1};

bool in_range(const P& p) {
    return 0 <= p.y && p.y < H && 0 <= p.x && p.x < W;
}

enum Angle { U = 0, L = 1, D = 2, R = 3 };
const string angle = "ULDR";
int rev(int k) {
    return (k + 2) % 4;
}

using Dir = array<bool, 4>;
P get_dir_pos(const P& p, int k) {
    return P(p.y + vy[k], p.x + vx[k]);
}
```
