# Queue

## 概要

- シンプルなQueueの実装

## コード

```cpp
template <class T>
struct Queue {
    vector<T> v;
    size_t p;

    Queue(size_t reserve_size = 1024 * 1024) : p(0) {
        v.reserve(reserve_size);
    }
    void push(const T& x) {
        v.emplace_back(x);
    }
    template <class... Args>
    void emplace(Args&&... args) {
        v.emplace_back(forward<Args>(args)...);
    }
    T& front() {
        return v[p];
    }
    T& back() {
        return v.back();
    }
    void pop() {
        p++;
    }
    bool empty() const {
        return v.size() == p;
    }
    size_t size() const {
        return v.size() - p;
    }
    void clear() {
        v.clear();
        p = 0;
    }
};
```

## Verified

- なし
