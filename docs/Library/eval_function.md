# 評価関数設計

[TOC]

## アプローチ

- 局所探索系
  - [局所探索/山登り/焼きなまし](./sa.md)
- ターン系
  - [ゲーム木](./game_tree.md)
  - [ビームサーチ](./beam_search.md)

## アイデアネタ

- 考えられる要素を洗い出して入れてみる、Optunaなどで調整
  - 要素、関数の形、など
- 生スコアに含まれていない周りの状態を含める
- 避けたい状態にペナルティ
- 最終的なスコアとしてあり得る下限/下界を考える
  - [AHC033](../ContestMemo/ahc033.md)
    - https://shuu0914.hatenablog.com/entry/2024/05/27/190612
  - [AHC040](../ContestMemo/ahc040.md)
- 最終状態での値を仮定して使う
  - [AHC044](../ContestMemo/ahc044.md)
- ベーススコアとの差
  - [AHC028](../ContestMemo/ahc028.md)
    - https://speakerdeck.com/terryu16/ahc028jie-shuo?slide=12
  - 「どれだけ理想状態よりムダな動きをするか？」を評価値とする

## Links

- https://www.mcdigital.jp/blog/20250331techblog/
