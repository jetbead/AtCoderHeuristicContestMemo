# TSP

```cpp
template <class T>
class TSP {
    int N;
    vector<vector<T>> G;

    struct State {
        T dist_sum;
        vector<int> order;
    };

    class Transition {
       public:
        Transition() = default;
        virtual ~Transition() = default;
        virtual T calc() = 0;
        virtual void apply() = 0;
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
        inline T calc() {
            return transitions[select_index]->calc();
        }
        inline void apply() {
            transitions[select_index]->apply();
        }
    };

    class Transition2opt : public Transition {
       private:
        State& state;
        const vector<vector<T>>& G;
        T new_dist_sum;
        int l, r;

       public:
        Transition2opt(State& state, const vector<vector<T>>& G) : state(state), G(G) {
        }
        virtual T calc() override {
            new_dist_sum = state.dist_sum;

            const int N = state.order.size();
            l = 1 + xor128() % (N - 1);
            r = l;
            while (l == r) r = 1 + xor128() % (N - 1);
            if (l > r) swap(l, r);

            int pl = (l - 1 + N) % N;
            int nr = (r + 1) % N;

            new_dist_sum -= G[state.order[pl]][state.order[l]];
            new_dist_sum -= G[state.order[r]][state.order[nr]];

            new_dist_sum += G[state.order[pl]][state.order[r]];
            new_dist_sum += G[state.order[l]][state.order[nr]];

            return new_dist_sum;
        }
        virtual void apply() override {
            state.dist_sum = new_dist_sum;
            while (l < r) {
                swap(state.order[l], state.order[r]);
                l++;
                r--;
            }
        }
    };

    class Transition3opt : public Transition {
       private:
        State& state;
        const vector<vector<T>>& G;
        T new_dist_sum;
        vector<int> pos;
        int recon_type;
        vector<int> new_order;

        void init_pos() {
            const int N = state.order.size();
            pos[0] = -1;
            while (true) {
                for (int i = 0; i < 3; i++) pos[i] = xor128() % N;
                sort(pos.begin(), pos.end());
                // if (pos[0] + 1 < pos[1] && pos[1] + 1 < pos[2]) break;
                if (pos[0] < pos[1] && pos[1] < pos[2]) break;
            }
        }

       public:
        Transition3opt(State& state, const vector<vector<T>>& G) : state(state), G(G), pos(3, -1) {
            new_order.reserve(state.order.size());
        }
        virtual T calc() override {
            new_dist_sum = state.dist_sum;
            init_pos();
            if (pos.size() == 0) return new_dist_sum;
            recon_type = xor128() % 7;
            const int N = state.order.size();

            if (recon_type == 0) {
                new_dist_sum -= G[state.order[pos[0]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum -= G[state.order[pos[1]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum += G[state.order[pos[0]]][state.order[pos[1]]];
                new_dist_sum += G[state.order[(pos[1] + 1) % N]][state.order[(pos[0] + 1) % N]];
            } else if (recon_type == 1) {
                new_dist_sum -= G[state.order[pos[1]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum -= G[state.order[pos[2]]][state.order[(pos[2] + 1) % N]];
                new_dist_sum += G[state.order[pos[1]]][state.order[pos[2]]];
                new_dist_sum += G[state.order[(pos[1] + 1) % N]][state.order[(pos[2] + 1) % N]];
            } else if (recon_type == 2) {
                new_dist_sum -= G[state.order[pos[0]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum -= G[state.order[pos[2]]][state.order[(pos[2] + 1) % N]];
                new_dist_sum += G[state.order[pos[0]]][state.order[pos[2]]];
                new_dist_sum += G[state.order[(pos[0] + 1) % N]][state.order[(pos[2] + 1) % N]];
            } else if (recon_type == 3) {
                new_dist_sum -= G[state.order[pos[0]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum -= G[state.order[pos[1]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum -= G[state.order[pos[2]]][state.order[(pos[2] + 1) % N]];
                new_dist_sum += G[state.order[pos[0]]][state.order[pos[1]]];
                new_dist_sum += G[state.order[(pos[0] + 1) % N]][state.order[pos[2]]];
                new_dist_sum += G[state.order[(pos[1] + 1) % N]][state.order[(pos[2] + 1) % N]];
            } else if (recon_type == 4) {
                new_dist_sum -= G[state.order[pos[0]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum -= G[state.order[pos[1]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum -= G[state.order[pos[2]]][state.order[(pos[2] + 1) % N]];
                new_dist_sum += G[state.order[pos[0]]][state.order[pos[2]]];
                new_dist_sum += G[state.order[(pos[1] + 1) % N]][state.order[(pos[0] + 1) % N]];
                new_dist_sum += G[state.order[pos[1]]][state.order[(pos[2] + 1) % N]];
            } else if (recon_type == 5) {
                new_dist_sum -= G[state.order[pos[0]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum -= G[state.order[pos[1]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum -= G[state.order[pos[2]]][state.order[(pos[2] + 1) % N]];
                new_dist_sum += G[state.order[pos[0]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum += G[state.order[pos[2]]][state.order[pos[1]]];
                new_dist_sum += G[state.order[(pos[0] + 1) % N]][state.order[(pos[2] + 1) % N]];
            } else if (recon_type == 6) {
                new_dist_sum -= G[state.order[pos[0]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum -= G[state.order[pos[1]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum -= G[state.order[pos[2]]][state.order[(pos[2] + 1) % N]];
                new_dist_sum += G[state.order[pos[0]]][state.order[(pos[1] + 1) % N]];
                new_dist_sum += G[state.order[pos[2]]][state.order[(pos[0] + 1) % N]];
                new_dist_sum += G[state.order[pos[1]]][state.order[(pos[2] + 1) % N]];
            }

            return new_dist_sum;
        }
        virtual void apply() override {
            if (pos[0] == -1) return;
            state.dist_sum = new_dist_sum;
            const int N = state.order.size();

            if (recon_type == 0) {
                int l = pos[0] + 1, r = pos[1];
                while (l < r) {
                    swap(state.order[l], state.order[r]);
                    l++;
                    r--;
                }
            } else if (recon_type == 1) {
                int l = pos[1] + 1, r = pos[2];
                while (l < r) {
                    swap(state.order[l], state.order[r]);
                    l++;
                    r--;
                }
            } else if (recon_type == 2) {
                int l = pos[0] + 1, r = pos[2];
                while (l < r) {
                    swap(state.order[l], state.order[r]);
                    l++;
                    r--;
                }
            } else if (recon_type == 3) {
                new_order.clear();
                for (int i = pos[1]; i >= pos[0] + 1; i--) new_order.push_back(state.order[i]);
                for (int i = pos[2]; i >= pos[1] + 1; i--) new_order.push_back(state.order[i]);
                for (int i = 0; i < new_order.size(); i++)
                    state.order[pos[0] + 1 + i] = new_order[i];
            } else if (recon_type == 4) {
                new_order.clear();
                for (int i = pos[2]; i >= pos[1] + 1; i--) new_order.push_back(state.order[i]);
                for (int i = pos[0] + 1; i <= pos[1]; i++) new_order.push_back(state.order[i]);
                for (int i = 0; i < new_order.size(); i++)
                    state.order[pos[0] + 1 + i] = new_order[i];
            } else if (recon_type == 5) {
                new_order.clear();
                for (int i = pos[1] + 1; i <= pos[2]; i++) new_order.push_back(state.order[i]);
                for (int i = pos[1]; i >= pos[0] + 1; i--) new_order.push_back(state.order[i]);
                for (int i = 0; i < new_order.size(); i++)
                    state.order[pos[0] + 1 + i] = new_order[i];
            } else if (recon_type == 6) {
                new_order.clear();
                for (int i = pos[1] + 1; i <= pos[2]; i++) new_order.push_back(state.order[i]);
                for (int i = pos[0] + 1; i <= pos[1]; i++) new_order.push_back(state.order[i]);
                for (int i = 0; i < new_order.size(); i++)
                    state.order[pos[0] + 1 + i] = new_order[i];
            }

            assert(state.order.size() == N);

            /*
            {
                double dsum = 0;
                for (int i = 0; i < N; i++) {
                    dsum += G[state.order[i]][state.order[(i + 1) % N]];
                }
                if (abs(dsum - state.dist_sum) > 1e-5) {
                    cerr << "! " << recon_type << " " << dsum << " " << state.dist_sum << endl;
                }
                assert(abs(dsum - state.dist_sum) < 1e-5);
            }
            //*/
        }
    };

    double calc_temp(double sec, double time_limit) {
        // 初期温度は近傍によって最適値が違いそうなので都度調整
        static const double START_TEMP = 1e-4;
        static const double END_TEMP = 1e-9;
        return START_TEMP + (END_TEMP - START_TEMP) * sec / time_limit;
    }

   public:
    TSP(int N) : N(N), G(N, vector<T>(N, 1e9)) {
    }
    void add_dist(int i, int j, const T& a) {
        G[i][j] = a;
        G[j][i] = a;
    }
    vector<int> calc(const double time_limit) {
        State state;
        for (int i = 0; i < N; i++) {
            state.order.push_back(i);
        }
        state.dist_sum = 0;
        for (int i = 0; i < N; i++) {
            state.dist_sum += G[state.order[i]][state.order[(i + 1) % N]];
        }

        State best_state = state;

        TransitionSelector selector;
        selector.add(make_unique<Transition2opt>(state, G), 50);
        selector.add(make_unique<Transition3opt>(state, G), 50);

        Timer timer;
        timer.start();
        double sec;
        int step = 0;
        while (true) {
            step++;
            sec = timer.getSec();
            if (sec > time_limit) break;
            double temp = calc_temp(sec, time_limit);

            selector.select_transition();

            T prev_score = state.dist_sum;
            T new_score = selector.calc();

            T delta = prev_score - new_score;
            bool undo = false;
            if (delta < 0) {
                if (exp(delta / temp) >= frand()) {
                    ;
                } else {
                    undo = true;
                }
            }
            if (undo) {
            } else {
                selector.apply();
                if (best_state.dist_sum > state.dist_sum) {
                    best_state = state;
                }
            }
        }
        cerr << "Step: " << step << endl;
        cerr << best_state.dist_sum << endl;

        return state.order;
    }
};
```
