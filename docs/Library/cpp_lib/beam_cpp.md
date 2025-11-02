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
  - 状態のコピーコスト削減(状態のサイズを小さくする、スコアだけを先に計算して上位のみ状態を生成する)
  - priority_queue部分の改善(逆にしてBEAM_WIDTH個以上ならpopする、など)
    - [cutoff priority_queue](../cutoff_priority_queue.md)
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

## Candidate生成によるビームサーチ

- Stateが重い場合、上記のように遷移先をStateで生成すると、処理時間的にもメモリ使用量的にも厳しい
- Stateから、評価値や操作差分だけなど必要最低限の情報だけをCandidateとして生成して、それで評価値上位を求めるようにすると軽くできる

### 前方更新型ビームサーチ

- 各ターンごと、State集合からCandidate集合を生成し、その評価値上位のもののみCandidateからStateを生成する
- 注意
  - 毎ターン、最大でビーム幅個のState生成(Stateのコピー+変更操作)が入るので重い
  - Stateの差分更新ができる場合は、差分更新型ビームサーチを検討する

```cpp
namespace ForwardBeamSearch {

namespace Utils {

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

};  // namespace Utils

////////// 以下を問題ごとに書き換える //////////

// TODO
using Score = double;  // 注: 高いほどよい
using Hash = uint32_t;

enum class OpType {
    // TODO
    NONE,
    OP1,
    OP2
};

struct Op {
    // 状態に対する変更操作の種類
    OpType type;
    // 状態に対する変更操作の追加情報
    // TODO

    Op() {
    }
};

struct State {
    Score score;
    Hash hash;
    // 状態に関するもの
    // TODO
    // 出力に関するもの
    // TODO

    State() {
    }

    inline bool operator<(const State& other) const {
        return score < other.score;
    }
    inline bool operator>(const State& other) const {
        return score > other.score;
    }

    // 状態に対する操作をサポートする関数
    // TODO
};
ostream& operator<<(ostream& os, const State& state) {
    // TODO
    os << "stateの解";
    return os;
}

// 注: できるだけ軽くする
struct Candidate {
    Score score;     // 変更後のスコア
    int parent_idx;  // 元となった親stateのindex
    Hash hash;       // 重複排除、多様性確保用
    Op op;           // 変更操作列
    bool finished;   // 終了したか

    inline bool operator<(const Candidate& other) const {
        return score < other.score;
    }
    inline bool operator>(const Candidate& other) const {
        return score > other.score;
    }
};

using Heap = Utils::CutOffPriorityQueue<Candidate>;

// 指定したStateからCandidateを作成
void expand(State& state, int parent_idx, Heap& cands) {
    Candidate cand;
    // TODO
    cands.push(cand);
}

// CandidateからStateを生成
State make_state_from_candidate(const State& base_state, const Candidate& cand) {
    State state = base_state;  // !!!Copy!!!
    state.score = cand.score;
    state.hash = cand.hash;
    // TODO
    return state;
}

////////// ここまで //////////

struct Config {
    int max_turn;
    int beam_width;

    bool with_timeout;     // 各ターンでのタイムアウトありで動かすかどうか
    double time_limit;     // 処理全体のtime limit
    double expand_weight;  // expandフェーズに使う時間割合(1.0未満)
};

double get_timeout_sec(double remain_time, int remain_turn, double expand_weight) {
    // 残り時間を等分割したものをこのターンで使える時間制限にする
    return (remain_time / remain_turn) * expand_weight;
}

void solve(const State& init_state, const Config& config) {
    Timer beam_timer;
    beam_timer.start();

    vector<State> states;
    states.emplace_back(init_state);
    Heap cands(config.beam_width);
    for (int turn = 0; turn < config.max_turn; turn++) {
        const double start_time = beam_timer.getSec();
        const double timeout_sec = get_timeout_sec(config.time_limit - start_time,
                                                   config.max_turn - turn, config.expand_weight);

        // expand
        cands.clear();
        for (size_t b = 0; b < states.size(); b++) {
            if (b > config.beam_width) break;
            if (b > 0 && config.with_timeout && beam_timer.getSec() - start_time > timeout_sec)
                break;
            expand(states[b], b, cands);
        }

        // cand2state
        bool finished = false;
        vector<State> next_states;
        next_states.reserve(config.beam_width);
        for (const Candidate& cand : cands.all()) {
            next_states.emplace_back(make_state_from_candidate(states[cand.parent_idx], cand));
            finished |= cand.finished;
        }

        swap(states, next_states);
        if (finished) break;
    }

    assert(!states.empty());
    State& best = states[0];
    cout << best << endl;
}

};  // namespace ForwardBeamSearch
```

### 差分更新型ビームサーチ

- Stateに対して、変更操作とその逆操作の処理が軽い場合、Stateのコピー無しで状態生成することで高速化できる可能性がある
  - move_forward(op), move_backward(op)
- EulerTourで操作を辺として持つ実装
  - 参考: https://eijirou-kyopro.hatenablog.com/entry/2024/02/01/115639
- 注意
  - Opだけの情報ではStateを戻せないような場合、Opを拡張するか、Candidate/Edgeに情報を持たせて、move_forward()/move_back_ward()時に更新する

```cpp
namespace EulerTourBeamSearch {

namespace Utils {

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

};  // namespace Utils

////////// 以下を問題ごとに書き換える //////////

// TODO
using Score = double;  // 注: 高いほどよい
using Hash = uint32_t;

enum class OpType {
    // TODO
    NONE,
    OP1,
    OP2
};

struct Op {
    // 状態に対する変更操作の種類
    OpType type;
    // 状態に対する変更操作の追加情報
    // TODO

    Op() {
    }

    bool operator==(const Op& op) const {
        // TODO
        return true;
    }
};

struct State {
    Score score;
    Hash hash;
    // 状態に関するもの
    // TODO

    State() {
    }

    inline bool operator<(const State& other) const {
        return score < other.score;
    }
    inline bool operator>(const State& other) const {
        return score > other.score;
    }

    // 現在の状態からopを適用した状態に遷移する
    // 注: Opだけで状態が復元できない場合は、Candidate/Edgeに持たせて更新する
    void move_forward(const Op& op) {
        // TODO
    }

    // 現在の状態にopをて供する前の状態に遷移する
    void move_backward(const Op& op) {
        // TODO
    }
};
ostream& operator<<(ostream& os, const State& state) {
    // TODO
    os << "stateの解";
    return os;
}

// 注: できるだけ軽くする
struct Candidate {
    Score score;     // 変更後のスコア
    int parent_idx;  // 元となった親stateのindex
    Hash hash;       // 重複排除、多様性確保用
    Op op;           // 変更操作列
    bool finished;   // 終了したか

    inline bool operator<(const Candidate& other) const {
        return score < other.score;
    }
    inline bool operator>(const Candidate& other) const {
        return score > other.score;
    }
};

using Heap = Utils::CutOffPriorityQueue<Candidate>;

// 指定したStateからCandidateを作成
void expand(State& state, int parent_idx, Heap& cands) {
    Candidate cand;
    // TODO
    cands.push(cand);
}

////////// ここまで //////////

struct Config {
    int max_turn;
    int beam_width;
};

class EulerTour {
    struct Edge {
        bool is_forward;  // 前進辺か
        int leaf_idx;     // 遷移後が葉ノードなら0以上の番号
        Op op;            // 操作列

        Edge() {
        }
        Edge(bool is_forward, int leaf_idx, const Op& op)
            : is_forward(is_forward), leaf_idx(leaf_idx), op(op) {
        }
    };

    State state;
    vector<Op> direct_path;

    vector<Edge> current_tour;
    vector<Edge> next_tour;
    vector<vector<pair<int, Candidate>>> buckets;

   public:
    EulerTour(const Config& config, const State& init_state)
        : state(init_state), buckets(config.beam_width) {
    }

    void make_cands(Heap& cands) {
        cands.clear();

        if (current_tour.empty()) {
            expand(state, 0, cands);
            return;
        }

        for (const Edge& e : current_tour) {
            if (e.is_forward) {
                state.move_forward(e.op);
            } else {
                state.move_backward(e.op);
            }

            if (e.leaf_idx >= 0) {
                expand(state, e.leaf_idx, cands);
            }
        }
    }

    bool update(Heap& cands) {
        const vector<Candidate>& leaves = cands.all();
        bool finished = false;

        if (current_tour.empty()) {
            for (size_t i = 0; i < leaves.size(); i++) {
                const Candidate& c = leaves[i];
                current_tour.emplace_back(true, i, c.op);
                current_tour.emplace_back(false, -1, c.op);
                finished |= c.finished;
            }
            return finished;
        }

        for (size_t i = 0; i < leaves.size(); i++) {
            const Candidate& c = leaves[i];
            buckets[c.parent_idx].emplace_back(i, c);
            finished |= c.finished;
        }

        auto it = current_tour.begin();

        while (it->is_forward && it->leaf_idx < 0 && it->op == current_tour.back().op) {
            direct_path.emplace_back(it->op);
            state.move_forward(it->op);
            current_tour.pop_back();
            ++it;
            if (it == current_tour.end()) break;
        }

        while (it != current_tour.end()) {
            if (it->is_forward) {
                next_tour.emplace_back(true, -1, it->op);
            } else {
                if (next_tour.back().is_forward) {
                    next_tour.pop_back();
                } else {
                    next_tour.emplace_back(false, -1, it->op);
                }
            }

            if (it->leaf_idx >= 0 && !buckets[it->leaf_idx].empty()) {
                for (const auto& pr : buckets[it->leaf_idx]) {
                    const int next_leaf_idx = pr.first;
                    const Candidate& c = pr.second;
                    next_tour.emplace_back(true, next_leaf_idx, c.op);
                    next_tour.emplace_back(false, -1, c.op);
                }
                buckets[it->leaf_idx].clear();
            }
            ++it;
        }

        swap(current_tour, next_tour);
        next_tour.clear();

        return finished;
    }

    vector<Op> get_path(int leaf_idx) const {
        assert(leaf_idx >= 0);

        vector<Op> ret = direct_path;
        for (const Edge& e : current_tour) {
            if (e.is_forward) {
                ret.emplace_back(e.op);
            } else {
                ret.pop_back();
            }

            if (e.leaf_idx == leaf_idx) {
                return ret;
            }
        }

        return vector<Op>();
    }
};

void solve(const State& init_state, const Config& config) {
    EulerTour et(config, init_state);
    Heap cands(config.beam_width);
    for (int turn = 0; turn < config.max_turn; turn++) {
        // expand
        cands.clear();
        et.make_cands(cands);

        // cand2state
        if (et.update(cands)) break;
    }

    Score best_score = cands.all()[0].score;
    vector<Op> best_ops = et.get_path(0);
    assert(!best_ops.empty());
    // output best_ops
}

};  // namespace EulerTourBeamSearch
```
