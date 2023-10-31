# グリッド操作

- 時折、2次元グリッドに対して何かをする問題が出題される
- グリッド固有の小ネタなど

## 隣接マスの事前列挙

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
  - ただし、この方法は全パターン生成されるわけではないことに注意
- TSP

## 柵で囲う最小個数

- https://phyllo-algo.hatenablog.com/entry/2017/02/05/040632
  - http://www.kupc.jp/static/media/E.67ad1a46.pdf
- 最小カット

## 3x3関節点高速化

- 連結成分が操作によってちぎれないかを判定する(=関節点判定)を単純にBFSとかしてしまうと遅い
- 完全な判定ではないが、近似的に8近傍のみで判定する方法で高速化できる
- https://twitter.com/takumi152/status/1705891739020525662
- https://twitter.com/chokudai/status/1706124817915908481
  - 連結性を保つ用途以外にも、大きな輪っかで連結になるような連結を禁止するような判定にも使える
- https://twitter.com/Shun___PI/status/1705978257437532498

### 曲率の離散類似による連結判定

- https://yokozuna57.hatenablog.com/entry/2023/10/27/155346
  - https://twitter.com/ethylene_66/status/1719190628675047894

### 消去可能性

- 画像処理で、あるセルxを削除してトポロジー構造を維持できるかの判定を以下の式で行うことができる
  - 連結成分だけでなく、穴の数とかも変えない条件がつく

```
432
501
678
```

- セルの値が0/1として、4近傍の場合、1+3+5+7-1\*2\*3-3\*4\*5-5\*6\*7-7\*8\*1の値が1ならば消去可能

```cpp
bool can_delete(int y, int x, int c) {
    int x1 = (grid[y][x + 1] == c) ? 1 : 0;
    int x2 = (grid[y - 1][x + 1] == c) ? 1 : 0;
    int x3 = (grid[y - 1][x] == c) ? 1 : 0;
    int x4 = (grid[y - 1][x - 1] == c) ? 1 : 0;
    int x5 = (grid[y][x - 1] == c) ? 1 : 0;
    int x6 = (grid[y + 1][x - 1] == c) ? 1 : 0;
    int x7 = (grid[y + 1][x] == c) ? 1 : 0;
    int x8 = (grid[y + 1][x + 1] == c) ? 1 : 0;
    return x1 + x3 + x5 + x7 - x1 * x2 * x3 - x3 * x4 * x5 - x5 * x6 * x7 - x7 * x8 * x1 == 1;
}
```
