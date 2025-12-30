# 焼きなましテンプレート

## 基本

### variant版

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
    std::chrono::steady_clock::time_point start_time;
    std::chrono::steady_clock::time_point getNow() {
        return std::chrono::steady_clock::now();
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
inline double frand() {
    return xor128() * (1.0 / 4294967296.0);
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

class Transition1 {
    const string name;
    State& state;
    int score;

   public:
    Transition1(const string& name, State& state) : name(name), state(state) {
    }
    void apply() {
        score = state.score;
        state.score = 1;
    }
    void rollback() {
        state.score = score;
    }
    string get_name() {
        return name;
    }
};

class Transition2 {
    const string name;
    State& state;
    int score;

   public:
    Transition2(const string& name, State& state) : name(name), state(state) {
    }
    void apply() {
        score = state.score;
        state.score = 1;
    }
    void rollback() {
        state.score = score;
    }
    string get_name() {
        return name;
    }
};

using Transitions = variant<Transition1, Transition2>;

template <class T>
concept TransitionConcept = requires(T t) {
    t.apply();
    t.rollback();
    { t.get_name() } -> convertible_to<string>;
};

class TransitionSelector {
    vector<int> table;
    vector<Transitions> transitions;
    int select_index;

   public:
    template <TransitionConcept T>
    void add(T transition, int weight) {
        int idx = transitions.size();
        for (int i = 0; i < weight; i++) {
            table.push_back(idx);
        }
        transitions.emplace_back(transition);
    }
    inline void select_transition() {
        select_index = table[xor128() % table.size()];
    }
    inline void apply() {
        visit([](auto& x) { x.apply(); }, transitions[select_index]);
    }
    inline void rollback() {
        visit([](auto& x) { x.rollback(); }, transitions[select_index]);
    }
    inline void dump() {
        visit(
            [](auto& x) {
                cerr << "select: " << x.get_name() << endl;
                ;
            },
            transitions[select_index]);
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
    // return START_TEMP * pow(END_TEMP / START_TEMP, sec / time_limit);
}

void solve() {
    State state;
    init(state);

    State best_state = state;

    // 近傍
    TransitionSelector selector;
    selector.add(Transition1("transition1", state), 1);
    selector.add(Transition2("transition2", state), 2);

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

### 継承版

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
    std::chrono::steady_clock::time_point start_time;
    std::chrono::steady_clock::time_point getNow() {
        return std::chrono::steady_clock::now();
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
inline double frand() {
    return xor128() * (1.0 / 4294967296.0);
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
    // return START_TEMP * pow(END_TEMP / START_TEMP, sec / time_limit);
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
