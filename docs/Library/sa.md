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
- その他
  - k-opt(3-opt, 4-opt, double-bridge)
  - Lin-Kernighan Heuristic

### kick 近傍

- Introduction to Heuristics Contest 解説 p.7
  - https://img.atcoder.jp/intro-heuristics/editorial.pdf
- k-swap-kick
  - TSP などで、k 個のセグメント s1,s2,...,sk を s1,sk,...,s2 にする感じ
  - 2-opt ではたどり着くのが難しいような double bridge みたいな形にキックする
