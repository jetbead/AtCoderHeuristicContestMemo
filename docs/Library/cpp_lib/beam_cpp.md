# ビームサーチ用テンプレート

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
            // c++20以降はshared_ptrのoperator<は削除されているので、compを指定する必要がある
            nth_element(next_states.begin(), next_states.begin() + beam_width, next_states.end(),
                        [](const auto& lhs, const auto& rhs) { return *lhs > *rhs; });
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

