# TSP(巡回セールスマン問題)

## Links

- http://www.math.uwaterloo.ca/tsp/index.html
- https://future-architect.github.io/articles/20211201a/
- https://twitter.com/shunji_umetani/status/977545293049688064

## 厳密解

- すべての並びを列挙 `O(N!)`
- bitDP `O(N^2 2^N)`
  - 蟻本pp.173-175

## 近似解法

### 2-opt/3-opt/k-opt/Lin-Kernighan heuristic

- Chained Lin-Lernighan
  - Applegate et al., Chained Lin-Kernighan for large traveling salesman problems, INFORMS Journal on Computing, 2003


### Lin-Kernighan-Helsgaun TSP Solver(LKH)

- http://webhotel4.ruc.dk/~keld/research/LKH/
- http://webhotel4.ruc.dk/~keld/research/LKH-3/
- LK法に効率的な枝刈りを導入した方法、ツール
- 詳細な解説や使い方、パラメータなどは同梱のpdfに書かれている
- LKH-3は、様々なTSP亜種などの問題に適用できるよう、penalty(違反)を定義して、penaltyが小さいtourを見つけるように拡張したもの

#### 利用例

- https://www.kaggle.com/code/ppershing/lkh-bootstrap-rust-fine-tuning/notebook?scriptVersionId=9206685

#### チューニング

- ほとんどのケースでデフォルトで十分な模様
- ただ、SEED、PATCHING_A/C、KICKS/KICK_TYPEあたりをいじると改善するかもしれない
  - (PATCHING_A/Cは変えられる値が制限されている？)
- TIME_LIMITもparファイルに指定しておくのがよさそう
- https://www.kaggle.com/c/traveling-santa-2018-prime-paths/discussion/73694

#### Penaltyの改造(LKH-3)

- https://www.kaggle.com/code/xuxu1234/santa-ctsp-2
- https://www.kaggle.com/code/starohub/st-21-a-minmax-ctsp/notebook


### Concorde TSP Solver(Concorde)

- https://www.math.uwaterloo.ca/tsp/concorde/index.html
- https://twitter.com/shunji_umetani/status/933354622005141504

### GA-EAX

- Edge Assembly Crossover(EAX)を使った遺伝的アルゴリズム(GA)
  - https://pubsonline.informs.org/doi/10.1287/ijoc.1120.0506
  - https://www.jstage.jst.go.jp/article/tjsai/22/5/22_5_542/_article/
- https://github.com/sugia/GA-for-TSP
- https://github.com/senshineL/GA-EAX-restart
- (LKHなどより良い解が得られる可能性が高い。kaggleで上位陣が利用)

### 自前の雑なTSPソルバー

```cpp
// 適当なTSPソルバー
#include <algorithm>
#include <fstream>
#include <iostream>
#include <random>
#include <set>
#include <sstream>
#include <vector>

struct P {
    double x, y;
};
double distance(const P& a, const P& b) {
    return sqrt((a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y));
}

class TSP {
    void vis(int tm, const std::vector<P>& v, const std::vector<int>& tour) {
        static int mabiki = 10;
        if (tm >= 0 && mabiki > 0) {
            mabiki--;
            return;
        }
        mabiki = 5;
        std::stringstream ss;
        ss << "out" << tm;
        std::ofstream ofs("plt/" + ss.str() + ".plt");
        ofs << "set terminal png size 500,500" << std::endl;
        ofs << "set output \"png/" << ss.str() + ".png\"" << std::endl;
        ofs << "set size ratio 1" << std::endl;
        ofs << "set nokey" << std::endl;
        ofs << "set grid" << std::endl;
        ofs << "set xrange [0:10000]" << std::endl;
        ofs << "set yrange [0:10000]" << std::endl;
        ofs << "set format x \"\"" << std::endl;
        ofs << "set format y \"\"" << std::endl;
        ofs << "plot \"-\" using 1:2 with lp pointtype 7" << std::endl;
        for (int i = 0; i <= tour.size(); i++) {
            ofs << v[tour[i % tour.size()]].x << "\t" << v[tour[i % tour.size()]].y << std::endl;
        }
        ofs << "e" << std::endl;
    }

    std::vector<int> small_case(const std::vector<P>& v) {
        int N = v.size();

        double tour_distance = 0;
        std::vector<int> tour(N);
        for (int i = 0; i < N; i++) {
            tour[i] = i;
            tour_distance += distance(v[tour[i]], v[tour[(i + 1) % N]]);
        }

        double best_distance = tour_distance;
        std::vector<int> best_tour = tour;

        do {
            tour_distance = 0;
            for (int i = 0; i < N; i++) {
                tour_distance += distance(v[tour[i]], v[tour[(i + 1) % N]]);
            }
            if (best_distance > tour_distance) {
                best_distance = tour_distance;
                best_tour = tour;
            }
        } while (std::next_permutation(tour.begin(), tour.end()));

        return best_tour;
    }

    void kick(const std::vector<P>& v, std::vector<int>& tour, double& tour_distance) {
        int N = v.size();
        // make k segments
        std::set<int> kpos;
        kpos.insert(0);
        while (kpos.size() < k_swapkick) {
            int r = 1 + rng() % (N - 1);
            kpos.insert(r);
        }
        // for (auto x : kpos) std::cout << x << " ";
        // std::cout << std::endl;

        // k-swap-kick
        std::vector<int> seg(kpos.begin(), kpos.end());
        int pos = 0;
        std::vector<int> new_tour(N);
        for (int i = seg[0]; i < seg[1]; i++) {
            new_tour[pos] = tour[i];
            pos++;
        }
        for (int i = seg.size() - 1; i > 0; i--) {
            int st = seg[i];
            int ed = (i == seg.size() - 1) ? N - 1 : seg[i + 1] - 1;
            for (int j = st; j <= ed; j++) {
                new_tour[pos] = tour[j];
                pos++;
            }
        }

        std::swap(tour, new_tour);
        tour_distance = 0;
        for (int i = 0; i < N; i++) {
            tour_distance += distance(v[tour[i]], v[tour[(i + 1) % N]]);
        }
        tmcnt++;
    }

    void local_search_2opt(const std::vector<P>& v, std::vector<int>& tour, double& tour_distance) {
        int N = v.size();
        bool ischange = true;
        while (ischange) {
            ischange = false;
            for (int pos0 = 0; pos0 < N - 2; pos0++) {
                for (int pos1 = pos0 + 2; pos1 < N; pos1++) {
                    tmcnt++;

                    int a = pos0;
                    int b = (a + 1) % N;
                    int c = pos1;
                    int d = (c + 1) % N;

                    if (a != 0 || d != 0) {
                        double base_distance = 0;
                        base_distance += distance(v[tour[a]], v[tour[b]]);
                        base_distance += distance(v[tour[c]], v[tour[d]]);

                        double new_distance = 0;
                        new_distance += distance(v[tour[a]], v[tour[c]]);
                        new_distance += distance(v[tour[b]], v[tour[d]]);

                        if (base_distance > new_distance) {
                            ischange = true;
                            tour_distance -= base_distance;
                            tour_distance += new_distance;

                            for (int i = 0; i <= (c - b) / 2; i++) {
                                std::swap(tour[b + i], tour[c - i]);
                            }
                            vis(tmcnt, v, tour);
                        }
                    }
                }
            }
        }
    }

    std::vector<int> large_case(const std::vector<P>& v) {
        int N = v.size();

        double tour_distance = 0;
        std::vector<int> tour(N);
        for (int i = 0; i < N; i++) {
            tour[i] = i;
            tour_distance += distance(v[tour[i]], v[tour[(i + 1) % N]]);
        }

        double best_distance = tour_distance;
        std::vector<int> best_tour = tour;

        for (int t = 0; t < retry; t++) {
            kick(v, tour, tour_distance);
            local_search_2opt(v, tour, tour_distance);

            if (best_distance > tour_distance) {
                best_distance = tour_distance;
                best_tour = tour;
            }
        }

        std::cout << best_distance << std::endl;
        vis(-1, v, best_tour);

        return best_tour;
    }

   public:
    TSP() : rng(std::random_device()()), tmcnt(0) {
    }

    std::vector<int> solve(const std::vector<P>& v) {
        if (v.size() <= 6)
            return small_case(v);
        else
            return large_case(v);
    }

   private:
    static const int k_swapkick = 6;
    static const int retry = 50;

    std::mt19937 rng;
    int tmcnt;
};

int main() {
    int N = 100;
    std::vector<P> v;
    for (int i = 0; i < N; i++) {
        P p;
        p.x = (rand() % 10000000) / 1000.0;
        p.y = (rand() % 10000000) / 1000.0;
        v.push_back(p);
    }

    TSP tsp;
    tsp.solve(v);

    return 0;
}
```



## 未分類

- https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8520904/
- https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8075568/
- Data Structures for Traveling Salesman
  - https://dl.acm.org/doi/pdf/10.5555/313559.313668
- https://github.com/GAStudyGroup/GPX2
- http://tsp-basics.blogspot.com/
