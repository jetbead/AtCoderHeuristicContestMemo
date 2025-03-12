# ビームサーチ用テンプレート

## 単純なビームサーチ

- 実装
  - Stateクラス
    - 状態を定義
    - 評価関数を定義
    - ハッシュ関数を定義
      - 重複排除や状態のループがなければ省略
    - デバッグ用printを実装
  - generate_next_states()関数
    - 状態から行動・次の状態を生成
  - 重複排除
- 改善
  - 行動の改善
  - 評価関数の改善
  - 出力を永続stackにする
  - 状態のコピーコスト削減(状態のサイズを小さくする)
  - priority_queue部分の改善(逆にしてBEAM_WIDTH個以上ならpopする、など)
  - 重複排除
  - 多様性確保
  - 評価値、ハッシュ値の差分計算
  - ビーム幅の調整、自動調整
  - 差分更新可能な場合は、オイラーツアービームサーチなどに変更
  - など

```cpp
struct BeamConfig {
    int MAX_TURN;
    int BEAM_WIDTH;
};

struct State {
    // ...

    double eval;          // 評価値
    uint64_t hash;        // ハッシュ値
    vector<int> actions;  // 行動列

    State() : eval(compute_eval()), hash(compute_hash()) {
    }

    // 現在の状態から愚直に評価値を計算
    double compute_eval() {
        // ...
        return 0;
    }

    // 現在の状態から愚直にハッシュ値を計算
    uint64_t compute_hash() {
        // ...
        return 0;
    }

    void print() {
        // ...
    }

    inline bool operator<(const State& other) const {
        return eval < other.eval;
    }

    inline bool operator>(const State& other) const {
        return eval > other.eval;
    }
};

using Heap = priority_queue<State>;  // 最大化タスク
// using Heap = priority_queue<State, vector<State>, greater<State>>; // 最小化タスク

void generate_next_states(Heap& next_states, const State& state) {
    State next_state = state;
    // ...
    next_state.eval = 0;
    next_state.hash = 0;
    next_state.actions.push_back(0);
    next_states.push(next_state);
}

void solve_beam(const BeamConfig& config) {
    State init_state;

    Heap states;
    states.push(init_state);
    rep(t, config.MAX_TURN) {
        Heap next_states;
        rep(b, config.BEAM_WIDTH) {
            if (states.empty()) break;

            generate_next_states(next_states, states.top());
            states.pop();
        }
        swap(states, next_states);
    }
    assert(!states.empty());
    State best_state = states.top();

    // ...
}
```

