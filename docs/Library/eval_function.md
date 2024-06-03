# 評価関数設計

## アプローチ

- 局所探索系
  - [局所探索/山登り/焼きなまし](./sa.md)
- ゲームでのヒューリスティックな評価関数設計
  - [ゲーム木](./game_tree.md)

## アイデアネタ

- 考えられる要素を洗い出して入れてみる、Optunaなどで調整
  - 要素、関数の形、など
- 生スコアに含まれていない周りの状態を含める
- 避けたい状態にペナルティ
- 残りターンの下限/下界を考える
  - [AHC033](../ContestMemo/ahc033.md)
    - https://shuu0914.hatenablog.com/entry/2024/05/27/190612
- ベーススコアとの差
  - [AHC028](../ContestMemo/ahc028.md)
    - https://speakerdeck.com/terryu16/ahc028jie-shuo?slide=12
  - 「どれだけ理想状態よりムダな動きをするか？」を評価値とする
