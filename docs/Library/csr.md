# CSR(compressed sparse row)

## 概要

- 静的なグラフの隣接リスト`vector<vector<T>>`を1次元vector2つで管理する
  - 隣接リストでの各行について、1次元に要素を並べたcolumnsでのオフセットをcompressed_rowsとして持っておく
- 1次元配列で管理すると、キャッシュやメモリアクセスでの高速化などが見込める可能性がある
- 注意
  - 常に速くなるとは限らない(むしろ遅くなる可能性もある)
  - 最適化オプションやcache効率、処理の内容で大きく変わる可能性がありそう

## コード

```cpp
template <class T>
class CSR {
    vector<size_t> compressed_rows;
    vector<T> columns;

   public:
    struct CSRSpan {
        using Iterator = typename vector<T>::const_iterator;
        Iterator b, e;
        CSRSpan(Iterator b, Iterator e) : b(b), e(e) {
        }
        Iterator begin() const {
            return b;
        }
        Iterator end() const {
            return e;
        }
    };
   public:
    CSR() {
    }
    CSR(const vector<vector<T>>& v) {
        build(v);
    }

    void build(const vector<vector<T>>& v) {
        compressed_rows.clear();
        columns.clear();

        size_t base = 0;
        for (size_t i = 0; i < v.size(); i++) {
            compressed_rows.emplace_back(base);
            base += v[i].size();
            for (size_t j = 0; j < v[i].size(); j++) {
                columns.emplace_back(v[i][j]);
            }
        }
        compressed_rows.emplace_back(base);
    }

    size_t rows_size() const {
        if (compressed_rows.size() == 0) return 0;
        return compressed_rows.size() - 1;
    }
    size_t columns_size() const {
        return columns.size();
    }
    size_t size(size_t i) const {
        return compressed_rows[i + 1] - compressed_rows[i];
    }
    CSRSpan get_span(size_t i) const {
        return CSRSpan{columns.begin() + compressed_rows[i],
                       columns.begin() + compressed_rows[i + 1]};
    }
    T& operator()(size_t i, size_t j) {
        return columns[compressed_rows[i] + j];
    }
    const T& get(size_t i, size_t j) const {
        return columns[compressed_rows[i] + j];
    }
    void set(size_t i, size_t j, const T& x) {
        columns[compressed_rows[i] + j] = x;
    }
    const vector<T>& get_columns() const {
        return columns;
    }
    const size_t get_columns_offset(size_t i) const {
        return compressed_rows[i];
    }
};
```

## Usage

```cpp
vector<vector<int>> v(5);
v[0].push_back(1);
v[0].push_back(2);
v[2].push_back(0);
v[3].push_back(4);
v[4].push_back(0);
v[4].push_back(2);

CSR<int> csr(v);

rep(i, csr.rows_size()) {
    cerr << i << ": ";
    for (int x : csr.get_span(i)) {
        cerr << x << " ";
    }
    cerr << endl;
}
```

## Verified

- なし
