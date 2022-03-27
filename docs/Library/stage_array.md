# Stage 機能付き Value/Array

## 概要

- 焼きなましなどで、変数や配列に対して 1 回だけ undo 操作を実装したい場合がある
- これを簡単に実装するために、操作に対して、commit で反映、reset で 1 つ前の commit 状態に戻す機能を持たせたクラス
  - `StageValue`: stage 機能付き変数
  - `StageArray`: stage 機能付き配列

## 計算量

- `set()`: $O(1)$
  - 値をセット
- `add()`: $O(1)$
  - 値に追加
- `get()` or `operator[]`: $O(1)$
  - 値を取得
- `commit()`: $O(m)$ ($m$: 操作回数)
  - 操作内容を反映
- `reset()`: $O(1)$
  - 操作内容を破棄し、1 つ前の commit 状態に戻す

## コード

```cpp
// stage機能付き変数
//   commitで保存、resetで一つ前のcommit時点まで戻す
template <class T>
class StageValue {
    T val;      // 最終状態の変数
    T stage;    // staging変数
    int now;    // 現在のcommit時間
    int times;  // stageの変更commit時間

   public:
    StageValue() : val(), stage(), now(0), times(-1) {
    }

    StageValue(const T& val) : val(val), stage(), now(0), times(-1) {
    }

    void set(const T& val) {
        stage = val;
        times = now;
    }

    void add(const T& val) {
        set(get() + val);
    }

    const T& get() const {
        if (times == now) {
            return stage;
        } else {
            return val;
        }
    }

    void commit() {
        if (now == times) val = stage;
        now++;
    }

    void reset() {
        now++;
    }
};

// stage機能付き配列
//   commitで保存、resetで一つ前のcommit時点まで戻す
template <class T>
class StageArray {
    int N;
    std::vector<T> arr;      // 最終状態の配列
    std::vector<T> stage;    // staging配列
    int now;                 // 現在のcommit時間
    std::vector<int> times;  // stageの変更commit時間
    std::vector<int> ops;    // 操作したindex

   public:
    StageArray() = delete;
    explicit StageArray(int N) : N(N), arr(N), stage(N), now(0), times(N, -1) {
    }

    int size() const {
        return N;
    }

    T& set(int i, const T& val) {
        stage[i] = val;
        times[i] = now;
        ops.push_back(i);
        return stage[i];
    }

    void add(int i, const T& val) {
        set(get() + val);
    }

    const T& get(int i) const {
        if (times[i] == now) {
            return stage[i];
        } else {
            return arr[i];
        }
    }

    void commit() {
        for (int i : ops) arr[i] = stage[i];
        ops.clear();
        now++;
    }

    void reset() {
        ops.clear();
        now++;
    }

    struct Deref {
        StageArray& sa;
        int index;
        Deref(StageArray& sa, int index) : sa(sa), index(index) {
        }
        operator T() {
            return sa.get(index);
        }
        T& operator=(const T& other) {
            return sa.set(index, other);
        }
    };
    Deref operator[](int index) {
        return Deref(*this, index);
    }
};
```

## Usage

```cpp
int main() {
    StageValue<int> x;
    x.set(5);
    cout << x.get() << endl; // 5
    x.reset();
    cout << x.get() << endl; // 0
    x.set(4);
    x.commit();
    cout << x.get() << endl; // 4
    x.set(3);
    x.reset();
    cout << x.get() << endl; // 4

    StageArray<int> v(10);
    // v.set(0, 1);
    v[0] = 1;
    v.commit();
    // cout << v.get(0) << endl; // 1
    cout << v[0] << endl;
    // v.set(0, 2);
    v[0] = 2;
    // cout << v.get(0) << endl; // 2
    cout << v[0] << endl;
    v.reset();
    // cout << v.get(0) << endl; // 1
    cout << v[0] << endl;

    return 0;
}
```

## Verified

- https://atcoder.jp/contests/ahc006/submissions/27339909
  - `add()`以外
