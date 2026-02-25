# グリッド操作

時折、2次元グリッドに対して何かをする問題が出題される。グリッド固有の小ネタなど。

[TOC]

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

## 1次元にして考える

- グリッド上の各マスを動くような処理の場合、パスを考えてそのパス上を順番に動くことを考えると1次元で考えられる
- いろんなパスを考えて、一番よいものを選ぶ、などに使える

### グリッド上のパスをルールで生成、パターン生成

- 1列ずつ左から右に順番に処理
- 左から右、下、右から左、下、・・・を繰り返す
- 渦巻き
  - 外側から
  - 内側から

### 全マスを通って戻ってくるパス生成(ハミルトンパス)

- 2x2を1マスと見て、それでできるグリッド上で全域木を作って、その壁に沿うようにマスをたどる
  - ただし、この方法は全パターン生成されるわけではないことに注意
- 全域木は、1辺追加してできるサイクル上のどれか1辺を削除することで、別の全域木を生成できる
- TSP
- ヒルベルト曲線

### 全域木を作って訪問

- 各マスをノードとして、DFS木などで木構造にして、順番に訪問する
  - 行きがけ順、帰りがけ順
- 各マスは1回以上訪問することになる
  - 隣接マスの移動回数が固定

### 全マスを通るパス生成(訪問順焼きなまし)

- マスを複数回通るのも許容して、各マスの訪問順を焼き鈍す

## 柵で囲う最小個数

- https://phyllo-algo.hatenablog.com/entry/2017/02/05/040632
  - http://www.kupc.jp/static/media/E.67ad1a46.pdf
- 最小カット

## 3x3関節点高速化

- 連結成分が操作によってちぎれないかを判定する(=関節点判定)を単純にBFSとかしてしまうと遅い
- 完全な判定ではないが、近似的に8近傍のみで判定する方法で高速化できる
  - 連結性を保つ用途以外にも、内部に穴ができるような連結操作を禁止するような判定にも使える(OFFマスがちぎれないようにする)
  - 3x3だけでなく、5x5なども同様に考えられる(4近傍のマスをUnionFindとかで連結性をチェック？)
- https://twitter.com/takumi152/status/1705891739020525662
- https://twitter.com/chokudai/status/1706124817915908481
- https://twitter.com/Shun___PI/status/1705978257437532498

```
> 完全な判定ではないが

例えば、
***
*.*
***
のような形状のとき、どの点も非連結になる判定になってしまう
```

```cpp
// 連結性チェック(3x3関節点判定)
// 注意: ちゃんとverifyしてない
class ArticulationPoint3x3 {
    bool hole_ok;
    vector<bool> memo;

    // (y,x)の地点のON/OFFを入れ替えられるかを確認
    // - maskは、(y,x)と周囲8近傍を含む3x3の範囲のON/OFF情報
    // 0 1 2
    // 3 4 5
    // 6 7 8
    bool check_mask(const vector<int>& mask) const {
        assert(mask.size() == 9);

        // 穴ができるのが許容で、OFF->ONにする場合は、4近傍にONがあればONにしてOK
        if (hole_ok && mask[4] == 0) {
            if (mask[1] == 1 || mask[3] == 1 || mask[5] == 1 || mask[7] == 1)
                return true;
            else
                return false;
        }

        // 4近傍がすべて同じなら、ONにもOFFにもできない
        if (mask[3] == mask[1] && mask[5] == mask[1] && mask[7] == mask[1]) return false;

        // 周囲にある2頂点について、真ん中のON/OFFが変わった場合に非連結になるかチェック
        // (コメントアウト部分は、遠回りで連結になる場合でもよい場合のチェック)
        static const vector<vector<vector<int>>> check = {
            {{1, 3}, {0} /*, {2, 5, 6, 7, 8}*/}, {{3, 7}, {6} /*, {0, 1, 2, 5, 8}*/},
            {{7, 5}, {8} /*, {0, 1, 2, 3, 6}*/}, {{5, 1}, {2} /*, {0, 3, 6, 7, 8}*/},
            {{1, 7}, {0, 3, 6}, {2, 5, 8}},      {{3, 5}, {0, 1, 2}, {6, 7, 8}}};
        for (const auto& c : check) {
            if (mask[c[0][0]] == mask[4] && mask[c[0][1]] == mask[4]) {
                bool ok = false;
                for (int i = 1; i < c.size(); i++) {
                    bool f = true;
                    for (const auto& idx : c[i]) {
                        if (mask[idx] != mask[4]) f = false;
                    }
                    if (f) {
                        ok = true;
                        break;
                    }
                }
                if (!ok) return false;
            }
        }
        return true;
    }

    void build() {
        memo.assign(1 << 9, false);
        for (int S = 0; S < (1 << 9); S++) {
            vector<int> mask(9, 0);
            for (int i = 0; i < 9; i++)
                if ((S >> i) & 1) mask[i] = 1;
            memo[S] = check_mask(mask);
        }
    }

   public:
    // hole_ok=trueなら穴ができるのを許容、falseなら穴ができるのは禁止
    ArticulationPoint3x3(bool hole_ok) : hole_ok(hole_ok) {
        build();
    }

    // 3x3の真ん中のON/OFFが変わった時、連結を保っているかどうか(falseなら(局所情報的には)非連結になると判断)
    bool check(int mask) const {
        assert(0 <= mask && mask < memo.size());
        return memo[mask];
    }

    bool check(const vector<vector<int>>& grid, int y, int x) const {
        int mask = 0;
        int mi = 0;
        for (int dy = -1; dy <= 1; dy++) {
            for (int dx = -1; dx <= 1; dx++) {
                int ty = y + dy;
                int tx = x + dx;
                if (0 <= ty && ty < grid.size() && 0 <= tx && tx < grid[ty].size()) {
                    if (grid[ty][tx] != 0) {
                        mask |= 1 << mi;
                    }
                }
                mi++;
            }
        }
        return check(mask);
    }
};
```

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

### 曲率の離散類似による連結判定

- https://yokozuna57.hatenablog.com/entry/2023/10/27/155346
  - https://twitter.com/ethylene_66/status/1719190628675047894



## ビット演算による距離計算

- [ビットボード](./bitboard.md)
- ビットボードにのる程度のサイズの場合は、現在地点から何ターンで目的地に移動できるか？をビットボードの1ターン分の移動を繰り返すことで求められる
  - ターンごとに持っておけば、経路復元もできる
  - maskとandを取るなどすれば、障害物や他の物体との衝突判定や有効な領域内かの判定などもできる
- 青木, ゲームで学ぶ探索アルゴリズム実践入門 7.4節
- [AHC033](../ContestMemo/ahc033.md)


## 時間軸を拡張

- ターン制のなにかを考える場合、グリッドのxy方向に、時間軸tを拡張して考えると、見通しがよくなる場合がある
  - `grid[t][x][y]`
- 目的地への距離や経路
  - 時刻tから時刻t+1に向かってBFSなどする
- マルチエージェントで他のエージェントと衝突しないか
  - 他のエージェントの各時刻の通過マスをメモしておき、それと重なる\(時刻,マス\)が判定
- xターン後に評価されるマスの伝搬
  - xターン後に評価値が高いようなマスは、過去の時刻と周辺マスに向かって減衰させながら評価値を伝搬させる、など


## 空間充填曲線

- [パターン生成](./patterns.md)
