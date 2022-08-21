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

### xoshiro/xoroshiro

- https://prng.di.unimi.it/

```cpp
// Xoroshiro128++
// https://prng.di.unimi.it/xoroshiro128plusplus.c
class Xoroshiro128pp {
  uint64_t s[2];
  static inline uint64_t rotl(const uint64_t x, int k){
    return (x << k) | (x >> (64-k));
  }
  uint64_t splitmix64() {
    static uint64_t x = 1234567;
    uint64_t z = (x += 0x9e3779b97f4a7c15);
    z = (z ^ (z >> 30)) * 0xbf58476d1ce4e5b9;
    z = (z ^ (z >> 27)) * 0x94d049bb133111eb;
    return z ^ (z >> 31);
  }
public:
  Xoroshiro128pp(){
    s[0] = splitmix64();
    s[1] = splitmix64();
  }
  uint64_t next(){
    const uint64_t s0 = s[0];
    uint64_t s1 = s[1];
    const uint64_t ret = rotl(s0 + s1, 17) + s0;
    s1 ^= s0;
    s[0] = rotl(s0, 49) ^ s1 ^ (s1 << 21);
    s[1] = rotl(s1, 28);
    return ret;
  }
  uint64_t operator()() { return next(); }
};

Xoroshiro128pp x128pp;
x128pp();
```

### PCG系

- https://www.pcg-random.org/index.html
- https://ja.wikipedia.org/wiki/Permuted_congruential_generator


### hack(シード値特定、hash衝突)

- https://twitter.com/yosupot/status/1236215631034253312
  - https://atcoder.jp/contests/rcl-contest-2020-final
- https://codeforces.com/blog/entry/62393
  - https://qiita.com/recuraki/items/652f97f5330fde231ddb
- https://noshi91.hatenablog.com/entry/2022/04/18/000958
- https://kmyk.github.io/blog/blog/2017/03/08/unordered-map-hash-collision/

