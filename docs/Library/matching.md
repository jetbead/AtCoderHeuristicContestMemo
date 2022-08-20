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
- 最小重み最大マッチング(割当問題)
  - ハンガリアン法

## マッチングの問題、高度な話題

- https://koyumeishi.hatenablog.com/entry/2017/03/26/191726
- https://speakerdeck.com/wata_orz/santakonpedeer-du-quan-wan-sitahua
  - https://www.youtube.com/watch?v=bM1Xtziy6xc

## Links

- https://ikatakos.com/pot/programming_algorithm/graph_theory/bipartite_matching
- https://blog.hamayanhamayan.com/entry/2017/05/09/120253