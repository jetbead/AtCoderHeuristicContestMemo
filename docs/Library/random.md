# 乱数/乱択

## Links

- https://www.slideshare.net/iwiwi/ss-13293754
- https://speakerdeck.com/square1001/algorithm-with-randomness
- https://blog.hamayanhamayan.com/entry/2017/08/25/110421

## 乱択アルゴリズム

- アルゴリズムの一部に乱数/ランダムな選択を取り入れているアルゴリズム
- 最適解を目指すコンテストの場合、いろんな状態や解を生成することになどよく利用される
  - DFSや貪欲するときにランダムに辺を選ぶ乱択DFS/乱択貪欲で、少し異なる解をたくさん生成して一番良いものを使う、など

## Zobrist Hash

- 集合(要素の集合や盤面の状態)にハッシュ値を割り振る方法
  - 重複排除などに利用
- xorを使っていて、要素を部分的に変更したときの差分が高速に計算できる
- https://en.wikipedia.org/wiki/Zobrist_hashing
- https://trap.jp/post/1594/


## 乱数

- 質の良い疑似乱数を使いたいが、一方で乱数生成が重いと探索回数が減ってしまったりする
  - 高速な乱数生成手法を使ったほうがよい場合が多いので、xorshiftなどよく使ってる
- https://twitter.com/reputeless/status/1505092507201605632

### xorshift

- https://en.wikipedia.org/wiki/Xorshift

```cpp
uint32_t xor128() {
    static uint32_t x = 123456789, y = 362436069, z = 521288629, w = 88675123;
    uint32_t t;
    t = (x ^ (x << 11));
    x = y;
    y = z;
    z = w;
    return w = (w ^ (w >> 19)) ^ (t ^ (t >> 8));
}
```

```cpp
uint64_t xor128p() {
    static uint64_t x0 = 123456789, x1 = x0 ^ 0x9E3779B97F4A7C15ULL;
    uint64_t s1 = x0, s0 = x1;
    x0 = s0;
    s1 ^= s1 << 23;
    s1 ^= s1 >> 18;
    s1 ^= s0 ^ (s0 >> 5);
    x1 = s1;
    return s0 + s1;
}
```

#### シャッフル

- 単純にランダムに2点をswapするのを繰り返す方法とかだと、偏りがでる
- Fisher–Yates_shuffle
  - O(N)

```cpp
template <class RandomAccessIterator>
void xor128_shuffle(RandomAccessIterator first, RandomAccessIterator last) {
    typename iterator_traits<RandomAccessIterator>::difference_type i, n;
    n = (last - first);
    for (i = n - 1; i > 0; --i) swap(first[i], first[xor128() % (i + 1)]);
}
```

### xoshiro/xoroshiro

- https://prng.di.unimi.it/

```cpp
// Xoroshiro128++
// https://prng.di.unimi.it/xoroshiro128plusplus.c
class Xoroshiro128pp {
    uint64_t s[2];
    static inline uint64_t rotl(const uint64_t x, int k) {
        return (x << k) | (x >> (64 - k));
    }
    uint64_t splitmix64() {
        static uint64_t x = 1234567;
        uint64_t z = (x += 0x9e3779b97f4a7c15);
        z = (z ^ (z >> 30)) * 0xbf58476d1ce4e5b9;
        z = (z ^ (z >> 27)) * 0x94d049bb133111eb;
        return z ^ (z >> 31);
    }

   public:
    Xoroshiro128pp() {
        s[0] = splitmix64();
        s[1] = splitmix64();
    }
    uint64_t next() {
        const uint64_t s0 = s[0];
        uint64_t s1 = s[1];
        const uint64_t ret = rotl(s0 + s1, 17) + s0;
        s1 ^= s0;
        s[0] = rotl(s0, 49) ^ s1 ^ (s1 << 21);
        s[1] = rotl(s1, 28);
        return ret;
    }
    uint64_t operator()() {
        return next();
    }
};

Xoroshiro128pp x128pp;
x128pp();
```

### PCG系

- https://www.pcg-random.org/index.html
- https://ja.wikipedia.org/wiki/Permuted_congruential_generator
- https://twitter.com/rho__o/status/1734784410808160661

```cpp
class Pcg32fast {
    static const uint64_t multiplier = 6364136223846793005ULL;
    uint64_t mcg_state;

   public:
    Pcg32fast() {
        mcg_state = 0xcafef00dd15ea5e5ULL;
        next();
    }
    Pcg32fast(uint64_t seed) {
        mcg_state = 2 * seed + 1;
        next();
    }
    uint32_t next() {
        uint64_t x = mcg_state;
        uint32_t count = (uint32_t)(x >> 61);
        mcg_state = x * multiplier;
        x ^= x >> 22;
        return (uint32_t)(x >> (22 + count));
    }
    uint32_t operator()() {
        return next();
    }
};
```

### 乱数生成の高速化

- `rand() % m`のかわりに`(ll)rand() * m >> 32`を使う
  - https://www.pcg-random.org/posts/bounded-rands.html
  - [AHC027](../ContestMemo/ahc027.md)
  - https://twitter.com/koyumeishi_/status/1363367043861389316
  - お気持ちとしては、0〜(2^32) -1の乱数を 2^32 で割ると0以上1未満になるので、それをm倍するイメージ
- https://twitter.com/rho__o/status/1734784563665424875
- 配列に入れておいてbit maskしたものでアクセス
  - https://twitter.com/terry_u16/status/1734776648627806391
- 高速な乱数生成アルゴリズム
  - https://twitter.com/ethylene_66/status/1734773049860501848
  - https://twitter.com/rho__o/status/1734786100873564174 (LCGs)

### hack(シード値特定、hash衝突)

#### 乱数予測

- https://twitter.com/yosupot/status/1236215631034253312
  - https://atcoder.jp/contests/rcl-contest-2020-final
- https://github.com/kmyk/mersenne-twister-predictor
  - http://herumi.in.coocan.jp/diary/1505.html#18
  - メルセンヌツイスターは、624個程度観察できれば次の乱数を予測できる

#### hash衝突

- https://codeforces.com/blog/entry/62393
  - https://qiita.com/recuraki/items/652f97f5330fde231ddb
- https://noshi91.hatenablog.com/entry/2022/04/18/000958
- https://kmyk.github.io/blog/blog/2017/03/08/unordered-map-hash-collision/

### Ziggurat Algorithm

- https://en.wikipedia.org/wiki/Ziggurat_algorithm
  - https://cpprefjp.github.io/reference/random/normal_distribution.html

## 重み付きサンプリング(Weighted random sampling)

- 復元/非復元(with/without replacement)抽出
  - 一度サンプリングしたものを、次のサンプリングの前に戻すかどうか
  - 同じ要素が再び複数回選ばれるかどうか

### Roulette Wheel Selection

- 要素iの重みw_iとして、要素iの選択される確率p_i = w_i / Σ_j w_jでサンプリングすること
- 計算テクとしては、w_iの累積和(or FenwickTree/BIT)を持っておいて、[0,合計)の一様乱数で該当する要素を決める
  - 愚直に線形に見るとO(N)だが、二分探索すればO(logN)でできる
  - さらに、前計算を工夫すると、O(1)で選択も可能(Walker's Alias Method)

### Walker's Alias Method

- N要素を重みに従ってサンプリングしたい場合、O(1)で行える
- 等分のbinを用意し、各binに高々2種類になるように割り当てる

```cpp
// 重み分布weight(和が1.0でなくてもよい)に従ってサンプリング
class WalkersAliasMethod {
    int N;
    std::vector<double> weight;
    std::vector<double> prob;
    std::vector<int> index;

    void make_table() {
        double average = 0.0;
        for (double w : weight) average += w;
        average /= weight.size();

        std::queue<pair<int, double>> que_over, que_under;
        for (size_t i = 0; i < weight.size(); i++) {
            if (weight[i] > average)
                que_over.push(std::make_pair(i, weight[i]));
            else
                que_under.push(std::make_pair(i, weight[i]));
        }
        while (!que_under.empty()) {
            std::pair<int, double> p = que_under.front();
            que_under.pop();

            double diff = average - p.second;

            if (!que_over.empty()) {
                std::pair<int, double> q = que_over.front();
                que_over.pop();

                prob[p.first] = diff / average;
                index[p.first] = q.first;

                q.second -= diff;
                if (q.second > average)
                    que_over.push(q);
                else
                    que_under.push(q);
            } else {
                prob[p.first] = 0.0;
                index[p.first] = p.first;
            }
        }
        while (!que_over.empty()) {
            std::pair<int, double> q = que_over.front();
            que_over.pop();
            prob[q.first] = 0.0;
            index[q.first] = q.first;
        }
    }

   public:
    WalkersAliasMethod(const std::vector<double>& weight) : weight(weight) {
        N = weight.size();
        prob.assign(N, 0.0);
        index.assign(N, 0);
        make_table();
    }

    void dump_table() {
        for (size_t i = 0; i < N; i++) {
            std::cerr << i << "\t";
            std::cerr << "[" << i << ": " << 1.0 - prob[i] << "]";
            std::cerr << "[" << index[i] << ": " << prob[i] << "]";
            std::cerr << std::endl;
        }
    }

    size_t size() const {
        return weight.size();
    }

    int sampling() {
        int idx = xor128() % N;
        return frand() < prob[idx] ? index[idx] : idx; // frand()は0.0から1.0の一様乱数
    }
};
```

### one-pass sampling

- 構築なしで、すべての要素を1回なめるだけでサンプリングするテク
  - w_iの合計値の情報なしでサンプリングできる
- `-ln(frand())/w_i`の値が一番小さいものを選ぶ
  - Reservoir sampling + exponential distribution + numeric stability
  - https://en.wikipedia.org/wiki/Reservoir_sampling
  - https://stackoverflow.com/questions/2140787/select-k-random-elements-from-a-list-whose-elements-have-weights
  - https://twitter.com/t33f/status/1576435864439488515
- k個選ぶなら、上記を降順ソートして前からk個選ぶ

## 乱択要素選択

- 候補集合からランダムに選ぶ
  - ランダムに集合のindexを選ぶ
  - 「[0,N)の整数の集合を管理する定数倍が軽いデータ構造」([高速化](./speedup.md),[IndexSet](./index_set.md))を使う
- 要素に重みをつけて、重み付きサンプリングする

```c++
class RandomSelector {
    vector<int> table;

   public:
    RandomSelector() {
    }
    RandomSelector(const vector<int>& weights) {
        for (int i = 0; i < weights.size(); i++) add(i, weights[i]);
    }
    void add(int val, int weight) {
        for (int i = 0; i < weight; i++) {
            table.push_back(val);
        }
    }
    inline int select() {
        if (table.size() == 0) return -1;
        return table[xor128() % table.size()];
    }
};
```

- 要素に重み(乱数)をつけて、ソート or priority_queueして前から選ぶ
- ランダムにk要素選んで、評価値を計算して、一番良いものを選ぶ
  - 評価値を求めるのが重い場合、選択時に計算を持ってこれるテク
  - https://www.youtube.com/watch?v=eddDPITjzDc (35分ごろ)
  - https://twitter.com/terry_u16/status/1576231985596764160
- 4近傍選択時の優先度シャッフル
  - https://twitter.com/chokudai/status/1628614093329559552
  - `int nx = x + vx[arr[k]]`みたいにarrを入れることで、方向の優先度をシャッフル
  - これがない場合は上下左右が固定なので、一方方向に偏りやすいのを回避できる
