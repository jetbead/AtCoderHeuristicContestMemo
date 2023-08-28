# CheckList

コンテスト開始直後や、詰まったりスコアが伸び悩んだときに見直すチェックリスト

## コンテスト開始直後

- 問題を正しく理解できているか？誤読していないか？
  - 時間制限は何秒か？
  - シンプルな解法で正のスコアが得られているか？
    - ローカルテスターで確認できているか？
    - 提出してみて手元のスコアとおおよそ一致しているか？
- 類似する過去の問題はあるか？
  - 有名問題や類似問題になっていないか？
- 問題固有の性質はないか？
  - 高スコアに繋がりそうな重要な性質(キモ)はないか？
  - 状態の要素に区別はあるか？交換可能か？
  - 状態の要素間に依存はどの程度あるか？
- 大まかな解法は複数列挙できるか？
  - 自由度が高すぎる問題の場合は、自明なパターンで動かして見てヒントになるような性質は見つかるか？
- 理論値が出せないか？
  - 無限に時間やリソースがあった場合どのような解法が考えられるか？
  - 小さいケースでは理論値出せないか？
    - それをベースにした手法は考えられないか？

## 詰まったとき

- 最適解(理論値)はどのような形になると考えられるか？
  - なぜそうできないのか？何が難しいか？
  - 最適解と思っていたものが間違っている可能性はないか？
  - 順位表上位のスコアはどこまでできていると考えられるか？
- 実装したものは期待した挙動をしているか？
  - アルゴリズムの途中の状態や統計を見て期待挙動しているか？
- 重要な性質を見落としていないか？
  - 高スコアが出ている解はどういうパターンを含んでいるか？
  - 良い性質、パターンはないか？
  - 局所性はないか？部分的に改善できないか？
    - できるなら局所探索(山登り、焼きなまし)が検討できないか？
- 問題を複数の部分問題に分けられないか？
  - 片方を探索、もう片方をシンプルな最適化などにできないか？
  - 探索空間を小さくできないか？
- 近傍は網羅できているか？
  - 小さい近傍、より小さい近傍はないか？
  - 大きい近傍はないか？
  - 良い解間を行き来できているか？
- 公式ビジュアライザで見れない情報はないか？
  - 自作ビジュアライザは必要か？
- 統計情報は見れているか？
  - 各問題での解や状態内の統計情報(頂点次数、深さ、依存関係、など)で差がないか？
  - パラメータごとのスコアや統計情報で差がないか？