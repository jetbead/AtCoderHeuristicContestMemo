# Fast-clearing Array

- 初期化処理をO(1)で行う
- 値は0以上しか入れられない(未初期化は-1として扱う)

```cpp
template <class T>
class FastClearingArray {
    int N;
    T base;
    T mx;
    vector<T> v;

   public:
    FastClearingArray(int N) : N(N), base(1), mx(0), v(N, T()) {
    }
    const T operator[](const int i) const {
        return get(i);
    }
    const T get(const int i) const {
        if (v[i] >= base) return v[i] - base;
        return -1;
    }
    void set(const int i, const T& x) {
        assert(x >= 0);
        v[i] = x + base;
        mx = max(mx, v[i]);
    }
    void clear() {
        base = mx + 1;
        if (base > 1e9) {
            base = 1;
            mx = 0;
            v.assign(N, T());
        }
    }
};
```
