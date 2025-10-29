# 足切りpriority_queue

## 概要

- ビームサーチの上位N件を高速に取る話
  - https://zenn.dev/siman/articles/e94f63246f6cb3
- ビームサーチ時に、priority_queue代替として使う

## コード(足切りsort)

- push()で要素を入れて、最後にall()でまとめて取得する
- 実装的には、2 \* max_size個溜まったらソートしてmax_size個に減らすのを繰り返す

```cpp
template <class T>
class CutOffPriorityQueue {
    int max_size;
    vector<T> v;
    bool update;

    void sort_and_cutoff() {
        sort(v.rbegin(), v.rend());
        if (v.size() > max_size) v.resize(max_size);
        update = false;
    }

   public:
    CutOffPriorityQueue(int max_size) : max_size(max_size), update(false) {
    }

    void clear() {
        v.clear();
        update = false;
    }

    void push(const T& x) {
        v.emplace_back(x);
        update = true;
        if (v.size() >= 2 * max_size) sort_and_cutoff();
    }

    vector<T>& all() {
        if (update) sort_and_cutoff();
        return v;
    }
};
```

## コード(足切りpriority_queue)

- (関数名が被るのでインタフェースを変えている)

```cpp
template <class T>
struct CutOffPriorityQueue : public priority_queue<T, vector<T>, greater<T>> {
    int max_size;
    CutOffPriorityQueue(int max_size) : max_size(max_size) {
    }
    bool add(const T& x) {
        if (this->size() >= max_size) {
            if (this->top() >= x) return false;
            this->pop();
        }
        this->push(x);
        return true;
    }
    vector<T> to_vector() {
        vector<T> ret(this->c);
        sort(ret.rbegin(), ret.rend());
        return ret;
    }
};
```

## Verified

- なし
