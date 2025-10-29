# 二重確率行列

## 概要

- 各行の和、各列の和がそれぞれ1になる非負の実正方行列
  - [AHC022](../ContestMemo/ahc022.md)
- 以下の実装では、normalize()を呼び出したら正規化する

## コード

```cpp
class DoublyStochasticMatrix {
    int N;
    vector<vector<double>> probs;

   public:
    DoublyStochasticMatrix(int N) : N(N), probs(N, vector<double>(N, 1.0 / N)) {
    }
    void set(int y, int x, double p) {
        probs[y][x] = p;
    }
    double get(int y, int x) {
        return probs[y][x];
    }
    void normalize(int T) {
        for (int t = 0; t < T; t++) {
            for (int i = 0; i < N; i++) {
                double sum = 0;
                for (int j = 0; j < N; j++) {
                    sum += probs[i][j];
                }
                for (int j = 0; j < N; j++) {
                    probs[i][j] /= sum;
                }
            }
            for (int j = 0; j < N; j++) {
                double sum = 0;
                for (int i = 0; i < N; i++) {
                    sum += probs[i][j];
                }
                for (int i = 0; i < N; i++) {
                    probs[i][j] /= sum;
                }
            }
        }
    }
};
```

## Verified

- なし
