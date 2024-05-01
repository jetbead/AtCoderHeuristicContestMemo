# ビームサーチ

## Links

- https://hakomof.hatenablog.com/entry/2018/12/06/000000
- https://qiita.com/tsukammo/items/02e8ad2469c5441d2956
- https://note.com/nyanyan_cubetech/n/n5e30c52139d3
- https://platinum-prog.hatenablog.com/entry/2023/07/28/222536
- https://platinum-prog.hatenablog.com/entry/2023/08/09/203217

## 確実に前進

- https://qiita.com/takapt0226/items/b2f6d1d77a034b529e21#%E9%81%B7%E7%A7%BB

## 置換表(transposition table, hash table)

- ゲームAIの置換表におけるreplacement strategyについて
  - https://yaneuraou.yaneu.com/2023/06/09/replacement-strategy-in-transposition-table/

## 出力の持ち方

- [永続stack](./persistent_stack.md)

## 重複排除

- ZobristHashを用いる
  - [乱数/乱択](./random.md)
- 確定した部分は無視する

## 多様性

- 状態のなにかの要素で区別して、それぞれでビームするようにする

### 小さい幅のビーム

- ある程度の幅をもたせた(1つの)ビームではなく、小さい幅＋多様性をもたせた(複数の)ビーム
  - AHC011 wataさん解
  - AHC019 USAさん解
- 評価関数がうまく設計できたり、ある程度良い解が多い場合など、高速に、よい解が得られる

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

## 高度な話題

- https://twitter.com/threecourse/status/1380460108551843841
