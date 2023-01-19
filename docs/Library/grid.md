# グリッド操作

- 時折、2次元グリッドに対して何かをする問題が出題される
- グリッド固有の小ネタなど

## 隣接マスの事前列挙列挙

- https://twitter.com/bowwowforeach/status/1437396768023404548
- 各マスについて、隣接マスを前計算しておくことで、範囲外かどうかのチェックを省ける
  - constexprでコンパイル時計算も

### 番兵を置く

- グラフをグリッドを外側に拡張して、そこに番兵を置くことで、範囲外チェックをなくす

## Dancing Links

- https://en.wikipedia.org/wiki/Dancing_Links
- https://japlj.hatenadiary.org/entry/20090902/1251901464
- https://matsu7874.hatenablog.com/entry/2018/07/15/200000
- (以下、もともとのDancing Linksとは微妙に違うかもしれないことに注意)
- 各セルについて、値のある/なし、がある場合、値がある各セルに「4近傍方向について、その方向に次に値があるセル」を保持するようにする(リンク)
- こうすることで、グリッドが疎な場合に、4近傍方向のセルの検索や削除を高速に行える
  - 隣のセルを検索する場合は、リンク情報を見ればよい
  - 削除する場合は、各方向のセルのリンク情報を書き換えてから取り除けば良い
  - 追加する場合は、リンクまたはセルをたどる


## 市松模様

```
oxoxoxo
xoxoxox
oxoxoxo
xoxoxox
```

- 性質がよい形
- `(i+j)%2`
- oの頂点集合とxの頂点集合で2部グラフになる
- いくつかのセルが使えない場合での、1x2サイズのタイル敷き詰めなど
  - o->xのみ辺を張ってフローを流す
  - https://atcoder.jp/contests/practice2/tasks/practice2_d
- https://koyumeishi.hatenablog.com/entry/2017/03/26/191726


## 全マスを通って戻ってくるパス生成(ハミルトンパス)

- 2x2を1マスと見て、それでできるグリッド上で全域木を作って、その壁に沿うようにマスをたどる
  - パスとして扱うと、1次元で考えることができる

## 柵で囲う最小個数

- https://phyllo-algo.hatenablog.com/entry/2017/02/05/040632
  - http://www.kupc.jp/static/media/E.67ad1a46.pdf
- 最小カット