# マッチング

## 二部グラフマッチング

- https://qiita.com/drken/items/e805e3f514acceb87602
  - 重みなし
    - s-tグラフを作って、フロー(最大流)を流す
    - https://snuke.hatenablog.com/entry/2019/05/07/013609
  - 最大重み
    - 重みをマイナスにしてコストに入れて、s-tグラフを作って最小費用流
      - マッチングできる頂点数は流量で表現
    - マッチングサイズがわかっていれば、コストを非負に変換して高速化できる
      - ベルマンフォードからdijkstraに変更
- 最小重み最大マッチング(割当問題)
  - ハンガリアン法

## 整数計画法に定式化

- OR-Toolsなどで解く

## マッチングの問題、高度な話題

- https://koyumeishi.hatenablog.com/entry/2017/03/26/191726
- https://speakerdeck.com/wata_orz/santakonpedeer-du-quan-wan-sitahua
  - https://www.youtube.com/watch?v=bM1Xtziy6xc
- https://www.docswell.com/s/hitonanode/K22QM4-2024-11-29#p55
- https://www.youtube.com/watch?v=vVJNlhQtfgo

## Links

- https://ikatakos.com/pot/programming_algorithm/graph_theory/bipartite_matching
- https://blog.hamayanhamayan.com/entry/2017/05/09/120253
