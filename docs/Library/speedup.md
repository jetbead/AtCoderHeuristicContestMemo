# 高速化(C++)

- 基本的に、本質的な改善に時間を使うほうがよくて、定数倍高速化などは最後の最後に行うほうが良い
  - 高速化が本質じゃないならば

## SIMD

- https://qiita.com/logicmachine/items/3531ddb0b8acc04cb613
- https://qiita.com/saka1_p/items/72c7755086ec985cade6
- https://runom.hatenablog.com/entry/2018/12/18/235641
- https://speakerdeck.com/sato_t/cpuwoatatakakusurujing-puroshi-li-ji

## Links

- https://xuzijian629.hatenablog.com/entry/2019/03/31/130708
- https://qiita.com/ageprocpp/items/7bda728d109c953ece3c
- https://yukicoder.me/wiki/auto_vectorization
- https://kmyk.github.io/blog/blog/2017/06/07/bitset-vector-bool/
- https://www.slideshare.net/KMC_JP/ss-45855264
- https://zenn.dev/irugo/articles/4bd113195228c3


## 細かいテク

### bit演算、bitset高速化

- 複数のbool値などを保持するのに`uint64_t`を64個のbit集合として圧縮したりすると、データコピーのコストが減って高速化につながる
- また、簡単なbit-level parallelで複数要素をまとめて処理することで高速化を狙う
- popcountなど
- https://naoyat.hatenablog.jp/entry/2014/05/12/143650
- https://www.slideshare.net/KMC_JP/slide-www
- https://rsk0315.hatenablog.com/entry/2021/06/08/091128
- https://rsk0315.hatenablog.com/entry/2022/09/25/214915


### vectorの途中の要素をO(1)で消すテク

- 消したい`v[i]`を末尾の要素とswapし、pop_back()する

### 計算量オーダーより実行時間

- ヒューリスティック系だと、Nが大きくなかったりするため、計算量オーダー的には`O(N)`でも`O(logN)`より高速に動作する場合がある
- 連結成分を見つけるのにUnionFind(dsu)を使うより、単純にBFSで列挙した方が実行時間が速かったりする

### 動的メモリ確保を減らす

- global変数やstatic変数にして使い回す
- 同じ処理を2回 or 変化したやつだけ保持しておいてそれだけ戻す or undo操作で初期状態に戻す
  - Nが大きいのに変化する要素が少ない場合に有効
- DFS的にノードを辿ったりする場合は、ノードごとに独立に状態を作るのではなく、undo操作＆advance操作でノード間を移動するようにすると状態のコピーがなくなる
- vectorなど、十分な量を最初にreserveしておく
- queueやpriority_queueなど、最後にemptyになるまでpopすれば空にできる