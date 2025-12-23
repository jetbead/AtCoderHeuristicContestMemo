# TSP(巡回セールスマン問題)

[TOC]

## 特殊ケース

- https://en.wikipedia.org/wiki/Travelling_salesman_problem
- Metric(delta TSP)
  - 三角不等式を満たす場合
- Euclidian TSP
  - ユークリッド距離の場合
- Synmetric/Asynmetric
  - 頂点間の距離が方向によらず同じか、異なるか

## 厳密解

- すべての並びを列挙 `O(N!)`
- bitDP `O(N^2 2^N)`
  - 蟻本pp.173-175

## 近似解法

### 2-opt/3-opt/k-opt/Lin-Kernighan heuristic

- Chained Lin-Lernighan
  - Applegate et al., Chained Lin-Kernighan for large traveling salesman problems, INFORMS Journal on Computing, 2003


### MSTを使った2近似アルゴリズム

- https://manabitimes.jp/math/1130

### クリストフィードのアルゴリズム

- Christofides algorithm
- 三角不等式が成り立つ距離のグラフでのTSPの近似アルゴリズム(3/2以下)
- https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%AA%E3%82%B9%E3%83%88%E3%83%95%E3%82%A3%E3%83%BC%E3%83%89%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0


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

## 未分類

- https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8520904/
- https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8075568/
- Data Structures for Traveling Salesman
  - https://dl.acm.org/doi/pdf/10.5555/313559.313668
- https://github.com/GAStudyGroup/GPX2
- http://tsp-basics.blogspot.com/

## Links

- http://www.math.uwaterloo.ca/tsp/index.html
- https://future-architect.github.io/articles/20211201a/
- https://twitter.com/shunji_umetani/status/977545293049688064
