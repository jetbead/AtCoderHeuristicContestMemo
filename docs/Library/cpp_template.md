# C++テンプレート

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
    int score;
};

class Neighborhood {
   protected:
    State& state;

   public:
    Neighborhood(State& state) : state(state) {
    }
    virtual void apply() = 0;
    virtual void rollback() = 0;
};

class Neighborhood1 : public Neighborhood {
   private:
   public:
    Neighborhood1(State& state) : Neighborhood(state) {
    }
    void apply() {
        state.score++;
    }
    void rollback() {
        state.score--;
    }
};

void input() {
}

double calc_temp(double sec, double time_limit) {
    static const double START_TEMP = 10.0;
    static const double END_TEMP = 1e-9;
    return START_TEMP + (END_TEMP - START_TEMP) * sec / time_limit;
}

void solve() {
    State state;
    state.score = 0;

    // 近傍
    Neighborhood1 neighborhood1(state);

    Neighborhood* neighborhood;
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

        // 近傍選択
        neighborhood = &neighborhood1;

        int prev_score = state.score;
        neighborhood->apply();
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
            neighborhood->rollback();
        }

        // cerr << state.score << endl;
    }

    cerr << "steps = " << step << endl;
    cerr << "Score = " << state.score << endl;
    cerr << "Processed Time = " << global_timer.getSec() << endl;
}

int main() {
    global_timer.start();
    input();
    solve();
    return 0;
}
```
