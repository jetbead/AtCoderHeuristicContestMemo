# Visualizer

[TOC]

## 可視化する目的

- 基本的には、状態や動きについて可視化することで、デバッグや何らかの知見を得ることが目的
  - AHCだと、公式ビジュアライザが提供されているため、それで十分な場合が多い
- しかし、公式ビジュアライザで見れない内部情報(推定値など)や、探索の経過(焼きなましのスコア推移など)を見るためには、別途、改造するか用意する必要がある

## 可視化に使う情報

- 入力情報
  - 問題の入力、テスター用の追加入力情報
- テスターの内部情報、出力情報
  - 真の値、判定結果、スコア(途中/最終)
- ソルバーの内部情報、出力情報
  - 推定値、出力解、デバッグ用の内部情報
  - ハイパラ
  - 探索途中の情報

## 状態や動きを可視化

- 公式ビジュアライザは、基本的には、出力情報となる状態や動きを可視化するものが用意される
  - ソルバーの出力結果を貼り付けて可視化するような使い方

### 参考

- [ヒューリスティックコンテストでビジュアライザを開発する方法に関するメモ(yunix さん)](https://yunix-kyopro.hatenablog.com/entry/2023/12/17/150534)
- [ビジュアライザ筋トレ2025年 chatGPT活用編](https://yunix-kyopro.hatenablog.com/entry/2025/01/16/204053)
- [マラソンマッチのビジュアライザいろいろ(threecourse さん)](https://threecourse.hatenablog.com/entry/2021/05/04/181226)
- [簡易にビジュアライザを作る方法の調査記(gobi さん)](https://gobi-tk.hatenablog.com/entry/2023/04/16/184952)
- [AHCのジャッジコードを流用して作るvisualzer作成(AtCoder第二回マスターズ選手権-予選-)(tsukammo さん)](https://tsukammo.hatenablog.com/entry/2025/03/03/011646)
- [コンテストリスト](../ContestMemo/index.md)

### 自作する場合

#### テスターを兼用

- 実際のソルバーの出力を入力とする場合、テスター側の情報がないため、途中状態のスコアなどがわからない
- そのため、テスターの処理をVisualizerの内部で持つことで、テスターとVisualizerを兼用する方針が考えられる
- AHCは公式テスターがRustで提供されるので、RustでVisualizerを用意すると移植などが楽で安全
  - 他の言語に移植すると、挙動の違いや実装ミスなどの危険性がある

#### コメントで内部情報を扱う

- AHCでは`#`から始まる出力はコメントとして扱い、テスターでは`#p x y`などデバッグやVisualizerに使える情報を出力している場合がある
- Visualizerでは、テスターとソルバーのどちらの情報も使う必要がある場合が多いため、どちらもコメント文に出力し、それを使って可視化する方針が考えられる
- テスターコードを持たなくても済むが、必要な情報をコメント文で出力する必要があるため、出力の肥大化や文字列で処理するため遅くなる危険性がある

```
例:

#p y x
→ 真の位置(公式テスターで出力)

#ep y x
→ 推定位置(ソルバーで出力)

#bp y x
→ ビームサーチなどでの2番目以降の候補情報(複数指定可)(ソルバーで出力)

#score s
→ 現在のスコア(公式テスター or ソルバーで出力)

#text y x t
→ 位置(y,x)にテキストtを表示(ソルバーで出力)
```

#### メモ

- [svgで簡易ビジュアライザ](./svg.md)
- [gnupolotで簡易ビジュアライザ](./gnuplot.md)

## 探索途中の状態を可視化

- 焼きなましの探索途中の推移や、ビームサーチのビーム内のスコア分布など、出力情報に含まれないが、デバッグや調整に有用
  - 公式ビジュアライザでも、時々、複数ケースを入力すると連続して表示できるようにしてある場合がある
- 基本的には、matplotlibなどで可視化するほうがわかりやすい

### 可視化例

- 横軸時間、縦軸スコア、でスコアの推移を可視化
  - 焼きなましの途中経過、など
  - 量が多い場合は、何ターンかごとのをプロット
- 横軸ターン数、縦軸スコア(min/ave/median/max)、で、スコアの推移を可視化
  - ビームサーチのビーム内のスコア分布、など

## 入出力情報、ハイパラの可視化

### 可視化例

- 入力情報の可視化
  - 入力パラメータの分布、など
- 入力パラメータと出力スコアの分布を可視化
- ハイパラを変えた時の出力スコアの分布を可視化
  - 焼きなましの初期温度/最終温度に対する最終スコア(ヒートマップで表示)、など
- [実行結果の分析](./result_analy.md)

## 機械学習系

### 可視化例

- confusion matrix
- pandas.plotting.scatter_matrix

## Visualize Tools

- kmyk/longcontest-visualizer-framework
  - https://github.com/kmyk/longcontest-visualizer-framework
- kyuridenamida/marathon-general-visualizer
  - https://github.com/kyuridenamida/marathon-general-visualizer
- kenkoooo/ahc001-gen-vis-wasm
  - https://kenkoooo.github.io/ahc001-gen-vis-wasm/
  - https://github.com/kenkoooo/ahc001-gen-vis-wasm
- shr_pc さん c++から svg 動画を html 形式で出力
  - https://twitter.com/shr_pc/status/1371121053330595840
- p5visualizer
  - https://github.com/shindannin/p5visualizer/tree/main
- OpenSiv3D
  - https://github.com/Siv3D/OpenSiv3D
  - https://zenn.dev/reputeless/books/siv3d-documentation

## 自作でVisualizeする場合の手段の例

- 公式visualizer改造
- svg
- python
  - PIL
  - matplotlib
  - seaborn
  - networkx, gephi
  - plotly
  - tcl/tk
- gnuplot
- c++
  - OpenSiv3D https://github.com/Siv3D/OpenSiv3D
  - SDL
  - MatPlot++ https://alandefreitas.github.io/matplotplusplus/
- js
  - p5.js https://p5js.org/
  
