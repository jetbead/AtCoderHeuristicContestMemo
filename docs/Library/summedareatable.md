# 2次元累積和

## 概要

- 2次元平面上の頂点集合が与えられるとき、任意の長方形領域にある頂点数を高速に計算
- 座標が負になる場合、座標が大きいが頂点数が少ない場合：座標圧縮(座圧)と組み合わせて使う

## 計算量

- `add()`: $O(1)$
  - 頂点を追加
- `init()`: $O(N^2)$
  - 累積和の構築
- `get()`: $O(1)$
  - 領域内の頂点数を返す
  - 注意: 使用する場合は必ず先にinit()を呼び出して構築する

## コード

```cpp
// NxNの領域の2次元累積和
template <class T>
struct SummedAreaTable {
    int N;
    vector<vector<T>> table;
 
    SummedAreaTable(int N) : N(N), table(N, vector<T>(N, 0)) {
    }
    void add(int y, int x) {
        table[y][x]++;
    }
    void init() {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                table[i][j] += get(i, j - 1) + get(i - 1, j) - get(i - 1, j - 1);
            }
        }
    }
    // [0,y]と[0,x]の領域の個数を返す
    T get(int y, int x) {
        if (y < 0 || x < 0) return 0;
        if (y >= N) y = N - 1;
        if (x >= N) x = N - 1;
        return table[y][x];
    }
    // [y1,y2)と[x1,x2)の領域の個数を返す
    T get(int y1, int x1, int y2, int x2) {
        return get(y2 - 1, x2 - 1) - get(y1 - 1, x2 - 1) - get(y2 - 1, x1 - 1) +
               get(y1 - 1, x1 - 1);
    }
};
```

## Usage

```cpp
int main() {
    SummedAreaTable<int> sat(10);

    // 頂点を追加
    sat.add(0, 0);
    sat.add(3, 0);
    sat.add(0, 5);

    // 累積和を構築
    sat.init();

    // 領域内の個数を返却
    cout << sat.get(0, 0, 1, 1) << endl;    // 1
    cout << sat.get(0, 0, 4, 1) << endl;    // 2
    cout << sat.get(0, 0, 10, 10) << endl;  // 3
    cout << sat.get(0, 5, 10, 10) << endl;  // 1

    return 0;
}
```

## Verified

- https://atcoder.jp/contests/ahc012/submissions/33008172
