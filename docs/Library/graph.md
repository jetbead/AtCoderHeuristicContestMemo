# グラフ

## グラフの種類

- 完全グラフ
- スター
- 2部グラフ
- K正則グラフ
- 隣接行列がブロック行列的なやつ
  - m個頂点で1つの頂点とみなし(倍化)、辺はすべての頂点同士をつなぐ
  - (ハイパーグラフ？)
- [名称のあるグラフのギャラリー(wikipedia)](https://ja.wikipedia.org/wiki/%E5%90%8D%E7%A7%B0%E3%81%AE%E3%81%82%E3%82%8B%E3%82%B0%E3%83%A9%E3%83%95%E3%81%AE%E3%82%AE%E3%83%A3%E3%83%A9%E3%83%AA%E3%83%BC)

## ラベルあり/ラベルなし

- ラベルありグラフ
  - https://oeis.org/A006125 (ラベルありグラフの数)
    - 各辺についてあるかないかなので、2^(N*(N-1)/2)
  - https://oeis.org/A001187 (連結な場合)
  - N個のラベルあり頂点を持つ木の個数は、n^(n-2)
    - Cayley's formula
    - プリューファ列が一対一対応する
    - https://kyopro.hateblo.jp/entry/2019/01/16/200456
- ラベルなしグラフ
  - https://oeis.org/A000088 (ラベルなしグラフの数)
  - https://oeis.org/A004251 (次数列の場合。N=5から同じになってしまうものができてしまう)
    - https://mathworld.wolfram.com/DegreeSequence.html

## ラベルなしグラフの生成(enumeration of unlabeled graphs)

- N-1頂点のラベルなしグラフに1頂点を追加するような感じで生成すると、N=8,9あたりで分や時間オーダーになってしまう

## ラベルなしグラフの同型判定

- ラベルの付け方N!通りについて、辺の有無をチェック O(N! N^2)
  - https://atcoder.jp/contests/abc232/tasks/abc232_c
  - (N=8,9ぐらいが限界)
- VF2アルゴリズム
  - http://satemochi.blog.fc2.com/blog-entry-224.html

## 連結判定、連結を保って〜

- UnionFind、BFS
- 関節点(LowLink)
  - その頂点を削除すると非連結になる頂点
  - グリッドの場合、周囲の3x3マスの状態を見ることで、簡易判定(高速化)
    - [AHC019](../ContestMemo/ahc019.md)
    - [AHC023](../ContestMemo/ahc023.md)
- 木構造で、ある2頂点をつないで、その頂点からDFSでサイクルを見つけて、サイクル上の任意の1辺を削除すると別の木構造が得られる

## 燃やす埋める

- https://www.slideshare.net/shindannin/project-selection-problem
- https://koyumeishi.hatenablog.com/entry/2021/01/14/052223

## 最大クリーク

- MaxCliqueSearch
  - A Simple and Faster Branch-and-Bound Algorithm for Finding a Maximum Clique
    - http://www.etlab.lab.uec.ac.jp/~tomita/WALCOM2010.pdf
  - A Much Faster Algorithm for Finding a Maximum Clique with Computational Expertiments
    - https://www.jstage.jst.go.jp/article/ipsjjip/25/0/25_667/_pdf/-char/ja

## 次数列からグラフ生成

- Havel-Hakimi algorithm
  - https://kopricky.github.io/code/Academic/random_graph_generator.html
    - Efficient and simple generation of random simple connected graphs with prescribed degree sequence
  - https://twitter.com/komora71_/status/1594278415850356737

## TSP

- [TSP(巡回セールスマン問題)](./tsp.md)

## 最短距離

### Single-Source Shortest Path(SSSP)

- https://yosupo.hatenablog.com/entry/2015/04/03/224649

#### 近似/差分更新/高速化

- [木構造](./tree.md)
  - [AHC017](../ContestMemo/ahc017.md)
    - Shortest Path Tree
    - 最短経路差分でグラフを作ってBFS
    - など

### All-pair Shortest Path(APSP)

- https://koyumeishi.hatenablog.com/entry/2021/07/03/165922

### Astar

### 双方向BFS

## コミュニティ抽出

- https://www.jstage.jst.go.jp/article/jsoft/21/4/21_500/_pdf
