# IndexSet

## 概要

- https://topcoder-tomerun.hatenablog.jp/entry/2021/06/12/134643
- 0〜N-1までの整数の集合を管理するクラス

## 計算量

- `insert()`: $O(1)$
  - 値を追加
- `erase()`: $O(1)$
  - 値を削除
- `count()`: $O(1)$
  - 指定した要素の個数を取得(0か1)
- `clear()`: $O(1)$
  - 値をすべて削除
- `size()`: $O(1)$
  - 要素数を取得
- `items()`: $O(1)$
  - すべての要素をvectorで取得

## コード

```cpp
class IndexSet {
    int N;
    vector<int> v, pos;

   public:
    IndexSet(int N) : N(N), pos(N, -1) {
    }
    void insert(int x) {
        assert(0 <= x && x < N);
        if (pos[x] != -1) return;
        pos[x] = v.size();
        v.push_back(x);
    }
    void erase(int x) {
        assert(0 <= x && x < N);
        if (pos[x] == -1) return;
        int p = pos[x];
        int b = v.back();
        v[p] = b;
        v.pop_back();
        pos[b] = p;
        pos[x] = -1;
    }
    int count(int x) {
        assert(0 <= x && x < N);
        if (pos[x] == -1) return 0;
        return 1;
    }
    void clear() {
        v.clear();
        pos.assign(N, -1);
    }
    size_t size() const {
        return v.size();
    }
    const vector<int>& items() const {
        return v;
    }
};
```
