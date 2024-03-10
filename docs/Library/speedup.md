# 高速化(C++)

- 基本的に、本質的な改善に時間を使うほうがよくて、定数倍高速化などは最後の最後に行うほうが良い
  - 高速化が本質じゃないならば

## Links

- https://xuzijian629.hatenablog.com/entry/2019/03/31/130708
- https://qiita.com/ageprocpp/items/7bda728d109c953ece3c
- https://yukicoder.me/wiki/auto_vectorization
- https://kmyk.github.io/blog/blog/2017/06/07/bitset-vector-bool/
- https://www.slideshare.net/KMC_JP/ss-45855264
- https://zenn.dev/irugo/articles/4bd113195228c3
- https://topcoder-tomerun.hatenablog.jp/entry/2017/12/16/000000
- https://en.algorithmica.org/hpc/
- https://www.cc.u-tokyo.ac.jp/events/lectures/X01/shiryou-1.pdf

## SIMD

- https://qiita.com/logicmachine/items/3531ddb0b8acc04cb613
- https://qiita.com/saka1_p/items/72c7755086ec985cade6
- https://runom.hatenablog.com/entry/2018/12/18/235641
- https://speakerdeck.com/sato_t/cpuwoatatakakusurujing-puroshi-li-ji
- https://www.cc.u-tokyo.ac.jp/kashiwa_open/files/25simd.pdf
- https://kaityo256.github.io/sevendayshpc/day7/index.html
- https://speakerdeck.com/herumi/simdnoji-chu-toguan-shu-noshi-zhuang-li

## AoS/SoA

- Array of Structure(AoS)、Structure of Arrays(SoA)
- キャッシュ効率やSIMDなどの最適のされやすさなど

## キャッシュブロッキング

- https://www.isus.jp/hpc/cache-blocking/
  - https://www.intel.com/content/www/us/en/developer/articles/technical/cache-blocking-techniques.html

## Cache-Oblivious

- https://www.slideshare.net/joisino/ss-258176417
- https://www.slideshare.net/iwiwi/cacheoblivious-dsirnlp5

## 細かいテク

### bit演算、bitset高速化

- 複数のbool値などを保持するのに`uint64_t`を64個のbit集合として圧縮したりすると、データコピーのコストが減って高速化につながる
- また、簡単なbit-level parallelで複数要素をまとめて処理することで高速化を狙う
- popcountなど
- https://naoyat.hatenablog.jp/entry/2014/05/12/143650
- https://www.slideshare.net/KMC_JP/slide-www
- https://rsk0315.hatenablog.com/entry/2021/06/08/091128
- https://rsk0315.hatenablog.com/entry/2022/09/25/214915
- https://graphics.stanford.edu/~seander/bithacks.html

### SIMD化されやすい書き方、キャッシュに乗りやすい書き方

- if文やmax/minの組み合わせではなく、clamp()を使う
- 参照の局所性
  - https://twitter.com/komori3_/status/1734784399504548339
  - 2次元対称行列のアクセスで添字の順番を揃える

### vectorの途中の要素をO(1)で消すテク

- 消したい`v[i]`を末尾の要素とswapし、pop_back()する

### [0,N)の整数の集合を管理する定数倍が軽いデータ構造(IndexSet)

- https://topcoder-tomerun.hatenablog.jp/entry/2021/06/12/134643
- [IndexSet](./index_set.md)
- 要素と位置の2つのvectorを持っておいて更新する
  - (validな候補集合の)indexを保持する、など
  - ランダムに選ぶ関数を追加する、など
- choise-dictionary?
  - https://arxiv.org/abs/1711.00808

### 計算量オーダーより計算回数

- ヒューリスティック系だと、Nが大きくなかったりするため、計算量オーダー的には`O(N)`でも`O(logN)`より高速に動作する場合がある
- 連結成分を見つけるのにUnionFind(dsu)を使うより、単純にBFSで列挙した方が実行時間が速かったりする

### キャッシュミスを減らす

- 2次元配列より1次元配列を使う、2次元配列でできるだけ連続した参照順序にする
- https://ja.wikipedia.org/wiki/%E5%8F%82%E7%85%A7%E3%81%AE%E5%B1%80%E6%89%80%E6%80%A7


### 動的メモリ確保を減らす

- global変数やstatic変数にして使い回す
- カウンタ変数を用意してその値かで判定することでbool配列の初期化を省略(Fast-Clearing Array)
  - https://topcoder-tomerun.hatenablog.jp/entry/2022/11/06/145156
  - https://twitter.com/koyumeishi_/status/1589142265209188352
  - https://twitter.com/FakePsyho/status/1605963724715479042
  - https://twitter.com/FakePsyho/status/1605965054779674624 (拡張)
  - https://qiita.com/tanaka-a/items/f06543c1f4750c465fc2 (世代管理)
- 初期化配列
  - https://qiita.com/kgoto/items/0251e442292d8ebc1f3d
- vectorなど、十分な量を最初にreserveしておく
- queueやpriority_queueなど、最後にemptyになるまでpopすれば空にできる

### 逆操作で戻す

- 同じ処理を2回 or 変化したやつだけ保持しておいてそれだけ戻す or undo操作で初期状態に戻す
  - Nが大きいのに変化する要素が少ない場合に有効
- DFS的にノードを辿ったりする場合は、ノードごとに独立に状態を作るのではなく、undo操作＆advance操作でノード間を移動するようにすると状態のコピーがなくなる
  - https://qiita.com/rhoo/items/f2be256cde5ad2e62dde

### 分岐を減らす

- https://en.algorithmica.org/hpc/pipelining/branchless/
- https://probablydance.com/2023/04/27/beautiful-branchless-binary-search/

