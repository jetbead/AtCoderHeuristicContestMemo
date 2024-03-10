# C++テンプレート

- 注意: ころころ変わる可能性あり

## 焼きなましテンプレート

```cpp
#include <bits/stdc++.h>

#include <atcoder/all>

using namespace std;
using namespace atcoder;
#define REP(i, a, n) for (int i = (a); i < (int)(n); i++)
#define rep(i, n) REP(i, 0, n)
#define FOR(it, c) for (__typeof((c).begin()) it = (c).begin(); it != (c).end(); ++it)
#define ALLOF(c) (c).begin(), (c).end()
typedef long long ll;
typedef unsigned long long ull;

class Timer {
    std::chrono::system_clock::time_point start_time;
    std::chrono::system_clock::time_point getNow() {
        return std::chrono::system_clock::now();
    }

   public:
    void start() {
        start_time = getNow();
    }
    float getSec() {
        float count =
            std::chrono::duration_cast<std::chrono::microseconds>(getNow() - start_time).count();
        return count / 1e6;
    }
};

uint32_t xor128() {
    static uint32_t x = 123456789, y = 362436069, z = 521288629, w = 88675123;
    uint32_t t;
    t = (x ^ (x << 11));
    x = y;
    y = z;
    z = w;
    return w = (w ^ (w >> 19)) ^ (t ^ (t >> 8));
}
inline float frand() {
    return xor128() % UINT32_MAX / static_cast<float>(UINT32_MAX);
}
inline int exprand(int x) {
    return (int)pow(x, frand());
}
template <class RandomAccessIterator>
void xor128_shuffle(RandomAccessIterator first, RandomAccessIterator last) {
    typename iterator_traits<RandomAccessIterator>::difference_type i, n;
    n = (last - first);
    for (i = n - 1; i > 0; --i) swap(first[i], first[xor128() % (i + 1)]);
}

static constexpr double GLOBAL_TIME_LIMIT = 1.97;
Timer global_timer;

struct State {
    // ...
    int score;

    State() {
        reset();
    }
    void reset() {
        // ...
        score = compute_score();
    }
    int compute_score() {
        // ...
        return 0;
    }
    void check_score() {
        assert(compute_score() == score);
    }
    void print() {
        // ...
    }
    inline bool operator<(const State& other) const {
        return score < other.score;
    }
    inline bool operator>(const State& other) const {
        return score > other.score;
    }
};

class Transition {
   protected:
    const string name;
    State& state;

   public:
    Transition(const string& name, State& state) : name(name), state(state) {
    }
    virtual ~Transition() = default;
    virtual void apply() = 0;
    virtual void rollback() = 0;
    virtual string get_name() {
        return name;
    }
};

class TransitionSelector {
    vector<int> table;
    vector<unique_ptr<Transition>> transitions;
    int select_index;

   public:
    void add(unique_ptr<Transition>&& transition, int weight) {
        int idx = transitions.size();
        for (int i = 0; i < weight; i++) {
            table.push_back(idx);
        }
        transitions.emplace_back(move(transition));
    }
    inline void select_transition() {
        select_index = table[xor128() % table.size()];
    }
    inline void apply() {
        transitions[select_index]->apply();
    }
    inline void rollback() {
        transitions[select_index]->rollback();
    }
    inline void dump() {
        cerr << "select: " << transitions[select_index]->get_name() << endl;
    }
};

class Transition1 : public Transition {
   private:
   public:
    Transition1(const string& name, State& state) : Transition(name, state) {
    }
    virtual void apply() override {
        state.score++;
    }
    virtual void rollback() override {
        state.score--;
    }
};

void input() {
}

void init(State& state) {
}

double calc_temp(double sec, double time_limit) {
    static const double START_TEMP = 10.0;
    static const double END_TEMP = 1e-9;
    return START_TEMP + (END_TEMP - START_TEMP) * sec / time_limit;
}

void solve() {
    State state;
    init(state);

    State best_state = state;

    // 近傍
    TransitionSelector selector;
    selector.add(make_unique<Transition1>("transition1", state), 100);

    // 探索
    const double TIME_LIMIT = GLOBAL_TIME_LIMIT - global_timer.getSec();
    Timer timer;
    timer.start();
    double sec;
    int step = 0;
    while (true) {
        step++;
        sec = timer.getSec();
        if (sec > TIME_LIMIT) break;
        double T = calc_temp(sec, TIME_LIMIT);

        selector.select_transition();

        int prev_score = state.score;
        selector.apply();
        int new_score = state.score;

        int delta = new_score - prev_score;
        bool undo = false;
        if (delta < 0) {
            if (exp(delta / T) >= frand()) {
                ;
            } else {
                undo = true;
            }
        }
        if (undo) {
            selector.rollback();
        } else {
            if (best_state < state) {
                best_state = state;
            }
        }

        // cerr << state.score << endl;
    }

    cerr << "steps = " << step << endl;
    cerr << "Score = " << best_state.score << endl;
    cerr << "Processed Time = " << global_timer.getSec() << endl;
}

int main() {
    global_timer.start();
    input();
    solve();
    return 0;
}
```


## ビームサーチテンプレート

```cpp
#include <bits/stdc++.h>

#include <atcoder/all>

using namespace std;
using namespace atcoder;
#define REP(i, a, n) for (int i = (a); i < (int)(n); i++)
#define rep(i, n) REP(i, 0, n)
#define FOR(it, c) for (__typeof((c).begin()) it = (c).begin(); it != (c).end(); ++it)
#define ALLOF(c) (c).begin(), (c).end()
typedef long long ll;
typedef unsigned long long ull;

class Timer {
    std::chrono::system_clock::time_point start_time;
    std::chrono::system_clock::time_point getNow() {
        return std::chrono::system_clock::now();
    }

   public:
    void start() {
        start_time = getNow();
    }
    float getSec() {
        float count =
            std::chrono::duration_cast<std::chrono::microseconds>(getNow() - start_time).count();
        return count / 1e6;
    }
};

uint32_t xor128() {
    static uint32_t x = 123456789, y = 362436069, z = 521288629, w = 88675123;
    uint32_t t;
    t = (x ^ (x << 11));
    x = y;
    y = z;
    z = w;
    return w = (w ^ (w >> 19)) ^ (t ^ (t >> 8));
}
inline float frand() {
    return xor128() % UINT32_MAX / static_cast<float>(UINT32_MAX);
}
inline int exprand(int x) {
    return (int)pow(x, frand());
}
template <class RandomAccessIterator>
void xor128_shuffle(RandomAccessIterator first, RandomAccessIterator last) {
    typename iterator_traits<RandomAccessIterator>::difference_type i, n;
    n = (last - first);
    for (i = n - 1; i > 0; --i) swap(first[i], first[xor128() % (i + 1)]);
}

static constexpr double GLOBAL_TIME_LIMIT = 1.97;
Timer global_timer;

using Action = int;
using Actions = vector<Action>;

struct State {
    int score;  // 評価値
    Action first_action;
    State() {
        reset();
    }
    void reset() {
        // ...
        score = compute_score();
    }
    int compute_score() {
        // ...
        return 0;
    }
    void check_score() {
        assert(compute_score() == score);
    }
    // ターン型の場合は状態に遷移操作を持たせる
    void advance(const Action& action) {
        // ...
    }
    Actions legal_actions() const {
        Actions actions;
        // ...
        return actions;
    }
    void print() {
        // ...
    }
    inline bool operator<(const State& other) const {
        return score < other.score;
    }
    inline bool operator>(const State& other) const {
        return score > other.score;
    }
};

Action random_action(const State& state) {
    auto legal_actions = state.legal_actions();
    assert(legal_actions.size() > 0);
    return legal_actions[xor128() % legal_actions.size()];
}

Action greedy_action(const State& state) {
    auto legal_actions = state.legal_actions();
    assert(legal_actions.size() > 0);

    int best_score = -1;
    Action best_action;
    for (const auto& action : legal_actions) {
        State now = state;
        now.advance(action);
        if (best_score < now.score) {
            best_score = now.score;
            best_action = action;
        }
    }
    return best_action;
}

Action beam_search_action(const State& state, const int beam_width, const int beam_depth) {
    vector<shared_ptr<State>> states;
    states.emplace_back(make_shared<State>(state));
    for (int turn = 0; turn < beam_depth; turn++) {
        vector<shared_ptr<State>> next_states;
        for (const shared_ptr<State>& now_state : states) {
            auto legal_actions = now_state->legal_actions();
            for (const auto& action : legal_actions) {
                shared_ptr<State> next_state = make_shared<State>(*now_state);
                next_state->advance(action);
                if (turn == 0) {
                    next_state->first_action = action;
                }
                next_states.emplace_back(next_state);
            }
        }
        if (next_states.size() > beam_width) {
            nth_element(next_states.begin(), next_states.begin() + beam_width, next_states.end(),
                        greater<>());
            next_states.resize(beam_width);
        }
        swap(states, next_states);
    }
    shared_ptr<State> best_state = make_shared<State>();
    for (const shared_ptr<State>& now_state : states) {
        if (*best_state < *now_state) {
            best_state = now_state;
        }
    }
    return best_state->first_action;
}

void input() {
}

void init(State& state) {
}

void solve_look_ahead() {
    State state;
    init(state);

    const double TIME_LIMIT = GLOBAL_TIME_LIMIT - global_timer.getSec();
    Timer timer;
    timer.start();
    double sec;
    while (true) {
        sec = timer.getSec();
        if (sec > TIME_LIMIT) break;

        Action action = random_action(state);
        state.advance(action);
    }
}

void solve_all(const int beam_width, const int max_turn) {
    State state;
    init(state);

    vector<State> states;
    states.emplace_back(state);
    for (int turn = 0; turn < max_turn; turn++) {
        vector<State> next_states;
        for (const State& now_state : states) {
            auto legal_actions = now_state.legal_actions();
            for (const auto& action : legal_actions) {
                State next_state = now_state;
                next_state.advance(action);
                if (turn == 0) {
                    next_state.first_action = action;
                }
                next_states.emplace_back(next_state);
            }
        }
        if (next_states.size() > beam_width) {
            nth_element(next_states.begin(), next_states.begin() + beam_width, next_states.end(),
                        greater<>());
            next_states.resize(beam_width);
        }
        swap(states, next_states);
    }
}

int main() {
    global_timer.start();
    input();
    solve_look_ahead();
    solve_all(10, 100);
    return 0;
}
```


## グリッド操作

```cpp
struct P {
    int y, x;
    P() : y(0), x(0) {
    }
    P(int y, int x) : y(y), x(x) {
    }
    bool operator<(const P& other) const {
        if (y == other.y) return x < other.x;
        return y < other.y;
    }
    bool operator>(const P& other) const {
        return other < *this;
    }
    bool operator==(const P& other) const {
        return y == other.y && x == other.x;
    }
};
ostream& operator<<(ostream& os, const P& p) {
    os << "(" << p.y << "," << p.x << ")";
    return os;
}

template <class T>
struct Array2D {
    static_assert(!is_same<T, bool>::value, "use int8_t instead of bool");
    const int H, W;
    vector<T> v;
    Array2D(int H, int W) : H(H), W(W), v(H * W, T()) {
    }
    void clear() {
        v.assign(H * W, T());
    }
    const T& operator[](const P& p) const {
        return v[p.y * W + p.x];
    }
    T& operator[](const P& p) {
        return v[p.y * W + p.x];
    }
};
using Grid = Array2D<int>;

template <class T>
class FastClearingArray2D {
    T base;
    T mx;
    Array2D<T> v;

   public:
    FastClearingArray2D(int H, int W) : base(1), mx(0), v(H, W) {
    }
    const T operator[](const P& p) const {
        return get(p);
    }
    const T get(const P& p) const {
        if (v[p] >= base) return v[p] - base;
        return -1;
    }
    void set(const P& p, const T& x) {
        assert(x >= 0);
        v[p] = x + base;
        mx = max(mx, v[p]);
    }
    void clear() {
        base = mx + 1;
        if (base > 1e9) {
            base = 1;
            mx = 0;
            v.clear();
        }
    }
};
```

## 方向操作

```cpp
struct Dir {
    enum Angle { U = 0, L = 1, D = 2, R = 3 };
    const string angle = "ULDR";
    const string rev_angle = "DRUL";
    const int vy[4] = {-1, 0, 1, 0};
    const int vx[4] = {0, -1, 0, 1};
    int rev(int k) const {
        return (k + 2) % 4;
    }

    array<bool, 4> dir;
};
```
