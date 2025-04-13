# 木構造

## 解の形を木構造に限定して考える

- 木には良い性質が多いので、グラフなどの問題で、解の形を「木」に限定して探索する、など
  - 最適解が木構造になる、または、最適スコアに近いものが得られる場合でないと、形を限定するのは普通はあまり良くない
- [AHC036](../ContestMemo/ahc036.md)

### 分解

- 木分解
- 重心分解
- Heavy-Light Decomposition

### 部分変更

- 木に1辺を追加するとサイクルになるので、サイクル上の別の1辺を削除すると別の木が得られる
  - [AHC011](../ContestMemo/ahc011.md)

### グリッドで木構造を考える

- [グリッド](./grid.md)

## DFS木、BFS木

- 各頂点に1回だけ訪問するようにグラフを深さ優先探索順か、幅優先探索順かで辿ったときに使った辺でできる木
- 巡回方法(2分木の場合)
  - 行きがけ順(pre-order)
    - DFSで、今の頂点、子の頂点、の順で処理
  - 通りがけ順(in-order)
    - DFSで、左の子、今の頂点、右の子の順で処理
  - 帰りがけ順(post-order)
    - DFSで、子の頂点、今の頂点の順で処理
  - レベル順(level-order)
    - BFS

## 全域木

- クラスカル法、プリム法、ブルーフカ法
- 全域木に含まれない辺は、その辺を追加してできるサイクルの中で一番長い
  - [AHC045](../ContestMemo/ahc045.md)
- Euclidean MST
  - O(VlogV)
  - https://www.mathenachia.blog/euclidean-mst/
  - https://en.wikipedia.org/wiki/Euclidean_minimum_spanning_tree
- 三角不等式を満たす場合、MSTを使ったTSPの2近似解法があり、「(MSTの総長) <= (TSPの総長) <= 2\*(MSTの総長)」になるようなTSPが構築できる
  - https://manabitimes.jp/math/1130

### O(N^2) prim法

- 完全グラフの場合、訪問済み頂点から未訪問済み頂点への最短距離のみ保持すればよい
  - [AHC045](../ContestMemo/ahc045.md)
- 余計な辺などを保持・計算しないためか、高速に動作

## 最短経路木(Shortest-path tree)

- ある頂点から他の頂点までの移動コストが最小になるような木
  - 辺のコストがある場合はダイクストラ、コストなければBFS、などで構築
  - 全域木
- グラフの構造が変化する場合(頂点や辺の追加削除など)、最短経路木の該当の部分のみ修正し、その部分木のみを更新すれば、高速に更新できる
  - https://bowwowforeach.hatenablog.com/entry/2023/02/09/210819
  - https://soumat.hatenablog.com/entry/2024/09/07/152734
  - [AHC017](../ContestMemo/ahc017.md)
  - [AHC036](../ContestMemo/ahc036.md)

## 最小シュタイナー木

- プリム法ベースのシュタイナー木構築
  - https://bowwowforeach.hatenablog.com/entry/2023/06/18/213212
  - [AHC020](../ContestMemo/ahc020.md)

## ドロネー図/ドロネー三角形分割/ボロノイ図

- https://qiita.com/tsukasa__diary/items/a835c17e5bf4935636c4
- https://en.wikipedia.org/wiki/Fortune%27s_algorithm
- https://github.com/mapbox/delaunator
- boost voronoi diagram
- (平面グラフの辺数は高々3N-6本)

## オイラーツアーテクニック

- 木の辺を2つの有向辺とみなしてオイラー閉路をDFSで作り、通過した辺を順番に配列にメモる
- 部分木が、メモした配列の連続区間に対応する
- ビームサーチで、状態遷移の過程をオイラーツアーの木で持って処理するなどにも使える
  - [ビームサーチ](./beam_search.md)


