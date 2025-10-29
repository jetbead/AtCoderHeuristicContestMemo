# オブジェクトプール

## 概要

- 簡易オブジェクトプール
- 大量のオブジェクトを管理したいとき、要素を再利用して、メモリアロケートをしないようにする

## コード

```cpp
template <class T>
class ObjectPool {
    vector<T> v;
    vector<int> deleted;

   public:
    void reserve(int n) {
        v.reserve(n);
    }
    T& operator[](int i) {
        return v[i];
    }
    int push(const T& a) {
        if (deleted.size() > 0) {
            int i = deleted.back();
            deleted.pop_back();
            v[i] = a;
            return i;
        } else {
            v.push_back(a);
            return v.size() - 1;
        }
    }
    void pop(int i) {
        deleted.push_back(i);
    }
    void size() const {
        return v.size() - deleted.size();
    }
};
```

## Verified

- なし
