# 焼きなまし

## Links

- 誰でもできる焼きなまし法(gasin さん)
  - https://gasin.hatenadiary.jp/entry/2019/09/03/162613
- 焼きなまし法のコツ Ver. 1.3(shindannin さん)
  - https://shindannin.hatenadiary.com/entry/2021/03/06/115415
- 詳解 焼きなまし法(hakomo さん)
  - https://github.com/hakomo/Simulated-Annealing-Techniques
- 競技プログラミングにおいて焼きなまし法に堕ちずに落とすコツ(tsukammo さん)
  - https://qiita.com/tsukammo/items/b410f3202372fe87c919
- chokudai 先生の焼きなまし講座
  - https://togetter.com/li/607979
- 貪欲法、山登り法、焼きなまし、ビームサーチ、これらの間の関係について(kmyk さん)
  - https://kmyk.github.io/blog/blog/2019/03/07/local-search-and-greedy/

## 近傍

### 2-opt 近傍

- https://en.wikipedia.org/wiki/2-opt
- 巡回セールスマン問題などで、2 点を交換する 2-swap 近傍は 4 辺変化してしまうが、そこを 2 辺変化させるような近傍
- 実装方法にもよるが、訪問点の順を状態に持っている場合は、ある範囲を reverse する感じになる
  - O(N)程度かかる
  - (辺の付替え時に分離しないように付け替える必要があるが、その判定のためにO(N)で辺をたどる必要があるため)
    - https://twitter.com/hotpepsi/status/1509563061027610624
- その他
  - k-opt(3-opt, 4-opt, double-bridge)
  - Lin-Kernighan Heuristic

### kick 近傍

- Introduction to Heuristics Contest 解説 p.7
  - https://img.atcoder.jp/intro-heuristics/editorial.pdf
- k-swap-kick
  - TSP などで、k 個のセグメント s1,s2,...,sk を s1,sk,...,s2 にする感じ
- 2-opt ではたどり着くのが難しいような double bridge みたいな形にキックする

## 評価関数

- スコアをそのまま利用
- [AHC011](../ContestMemo/ahc011.md)
  - 2タイルをswapする近傍で、「一番大きな木のサイズ」を評価関数にすると、それ以外のタイルの状態が考慮できないので、「連結成分の個数(が1になるようにする)」や「連結成分のサイズの2乗和」などにすると、収束が速い

## スコア計算の高速化

### 差分計算

- [AHC012](../ContestMemo/ahc012.md)
  - 直線の追加削除は、その直線の両側の領域だけを考えれば良い
  - ある領域にある個数は座圧＋二次元累積和でO(1)で求められ、領域数はO(K)程度

### 状態更新を後に持っていくテク

- [AHC009](../ContestMemo/ahc009.md)
  - 前からDP・後からDPを持っておくと、差分計算が高速に求められる
  - さらに、近傍更新時の変更箇所をターン順方向に固定すると、状態更新の方も高速化可能

## その他
### Invalidな状態を許容する焼きなまし

- [AHC011](../ContestMemo/ahc011.md)
  - 最終的に「与えられたタイルで作れる」必要があるが、辺の追加・削除を近傍に、与えられたタイルで作れない状態も探索
    - ただし、評価関数は各利用タイル数の差にして0になるよう目指す
