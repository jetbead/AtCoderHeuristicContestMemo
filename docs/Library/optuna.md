# optuna

[TOC]

## 公式

- https://optuna.org/
- https://optuna.readthedocs.io/en/stable/tutorial/index.html

## 使用例

- https://tech.preferred.jp/ja/blog/wilcoxonpruner/
- https://blog.knshnb.com/posts/ahc007-optuna/
- https://qiita.com/toast-uz/items/8449537d47d296333871
- https://kuruton.hatenablog.com/entry/2021/12/14/011150
- https://x.com/not_522/status/1866105405287584182
  - https://tech.preferred.jp/ja/blog/optuna-autosampler/

### C++での利用

- いろんなアプローチがあるが、C++のコードの場合、方法によってはリビルドが必要になったり、スコア計算をどうするかなどが問題になる
  - ハイパラの注入方法
  - スコア計算方法(自前か、公式テスターを使うか)
- リビルドあり
  - マクロにしておいて、`-DPARAM1=0.1`などで指定してビルド
  - コードの該当行をreplaceしてビルド
    - configなどで該当行の文字列と置き換え文字列&ハイパラを指定できるようにしておく
- リビルドなし
  - コマンドライン引数で指定する
  - ハイパラを書いた設定ファイルなどで読み込むようにする
    - [Config](./design_patterns/config.md)
