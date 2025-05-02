# ビームサーチ

## Links

- https://hakomof.hatenablog.com/entry/2018/12/06/000000
- https://qiita.com/tsukammo/items/02e8ad2469c5441d2956
- https://note.com/nyanyan_cubetech/n/n5e30c52139d3
- https://platinum-prog.hatenablog.com/entry/2023/07/28/222536
- https://platinum-prog.hatenablog.com/entry/2023/08/09/203217
- https://twitter.com/ethylene_66/status/1767393143987880121
- https://qiita.com/thun-c/items/98f22c8fcdb1fd827b00

## 確実に前進

- https://qiita.com/takapt0226/items/b2f6d1d77a034b529e21#%E9%81%B7%E7%A7%BB

## 置換表(transposition table, hash table)

- ゲームAIの置換表におけるreplacement strategyについて
  - https://yaneuraou.yaneu.com/2023/06/09/replacement-strategy-in-transposition-table/

## 出力の持ち方

- [永続stack](./persistent_stack.md)

## 同一局面によるループを防ぐ

- 前の状態に戻る操作などでループが発生してしまう場合、無限ループになってしまう可能性がある
- 局面に対するハッシュ値などですでに出現した状態は計算しないようにしておく
- ZobristHashを用いる
  - [乱数/乱択](./random.md)

## 重複排除、多様性確保

- ビームの上位が同じか、似たような状態ばかりになると、多様性が確保できず、良い解を見つけられない可能性がある
- ZobristHashなどを用いて、厳密なハッシュ値や、荒く分割したもののハッシュ値を求めるなどで、似たような盤面を排除する
  - [乱数/乱択](./random.md)
- 同スコアのものは排除する
- 状態のなにかの要素で区別(別のビームに)して、それぞれでビームするようにする
- 確定した部分は無視する
- https://twitter.com/Rafbill_pc/status/1767494993923477774
  - https://gist.github.com/Rafbillpc/814f226576d59b8aa58c552c373f8217

### 小さい幅のビーム

- ある程度の幅をもたせた(1つの)ビームではなく、小さい幅＋多様性をもたせた(複数の)ビーム
  - AHC011 wataさん解
  - AHC019 USAさん解
- 評価関数がうまく設計できたり、ある程度良い解が多い場合など、高速に、よい解が得られる

## chokudaiサーチの軸

- chokudaiサーチでは、軸(キー)をどう用意するか、いろいろバリエーションが考えられる
  - ターン制などでも、複数の操作を1手として動かす場合、その1手をキーにするか、ターンをキーにするか、など
    - 次の状態生成時に、次の状態にしかいかないか、2つ以上先の状態もいくか
  - 軸にしたものは、評価関数に入ってなくても揃うので、調整がしやすくなるかも

## ビーム幅の自動調整

- アクションが固定など処理時間が安定している場合はハイパラ探索などで調整すればよいが、アクション数が盤面によって変わったり計算時間が変わる場合は調整が難しい
- 動的に調整する場合、各ターンでは、それまでのビーム幅(の合計値)、計算時間、残りターン数、残り時間の情報などが使える

## 評価値

- [評価関数設計](./eval_function.md)

## 状態の差分更新で木のノードを行き来するテク

- advance/undo操作で状態を行き来する(差分)やつ
  - 状態の差分更新をすることで、状態のコピーをなくす
- https://gitlab.com/rafaelbocquet-cpcontests/euler-tour-beam-search (euler-tour beam-search)
- https://qiita.com/rhoo/items/f2be256cde5ad2e62dde
- https://qiita.com/rhoo/items/2f647e32f6ff2c6ee056
- https://twitter.com/threecourse/status/1381102400400809992
- https://twitter.com/search?q=from%3Acolun%20until%3A2018-04-19%20since%3A2018-04-16&src=typed_query&f=live
- https://twitter.com/chokudai/status/986484347069915138
- https://github.com/niuez/niuristic/blob/main/algorithm/beam_search/beam_search.md
- https://eijirou-kyopro.hatenablog.com/entry/2024/02/01/115639
- https://twitter.com/rho__o/status/1753063890219642931

## 上位N件を高速に取る話

- https://zenn.dev/siman/articles/e94f63246f6cb3
  - https://twitter.com/_simanman/status/1618415422277292032
  - https://twitter.com/koyumeishi_/status/1618430006207877120
  - https://twitter.com/colun/status/1618419150384418816
  - https://twitter.com/FakePsyho/status/1618522597481938945
- https://twitter.com/yosupot/status/1779512387810570716

## 近傍設計

### 過去改変

- 手順や操作列を状態に持つとき、最後に加えるのではなく、途中に操作列を挿入するような近傍
- [AHC021](../ContestMemo/ahc021.md)
- [AHC042](../ContestMemo/ahc042.md)
- [AHC046](../ContestMemo/ahc046.md)
- kaggle santa 2023
  - https://github.com/wata-orz/santa2023_permutation_puzzle

## 高度な話題

- https://twitter.com/threecourse/status/1380460108551843841
