# 不確かさの取り扱い

正確な情報が得られない場合や隠れパラメータについて、推定したい。

## 表現方法、推定方法

- 区間(値の取りうる範囲の最大最小を保持)
  - 平均
  - 統計を取ってアドホックに調整
- 確率、確率分布
  - 確率行列、二重確率行列
  - ガウス分布
- ベイズ推定
- モンテカルロ法、MCMC、ギブスサンプリング、カルマンフィルタ、パーティクルフィルタ
- 機械学習で推定
  - 絶対誤差、二乗誤差などを最小化

## アプローチ

- 統計を取ってみて、どのような分布になっているか確認する
- もし、特徴的ならばそれを活用する
- 既知の分布なら、その分布を使う
- 未知の分布でも、思い切って正規分布であると仮定してみる、など

## 逐次推定

- 「どの情報を取得するか？」が自分で選べる場合、不確かさが一番減るようなものを選ぶ
  - エントロピー減少量(相互情報量)
  - 誤差分散の減少量

