# Visualizer

## メモ

- AHCにおいては、基本的に公式ビジュアライザが提供されているので、それで十分な場合が多い
- ビジュアライザが提供されなかったり、デバッグやよりリッチな情報を表示したい場合に自作を検討する
- ただし、公式テスターがRustで提供されていて流用できるため、Rustでビジュアライザを用意したほうが安全性(バグの恐れ、挙動の違い)やコスパ(準備にかかる時間)はよい可能性がある

## まとめ

- [ヒューリスティックコンテストでビジュアライザを開発する方法に関するメモ](https://yunix-kyopro.hatenablog.com/entry/2023/12/17/150534)
- [マラソンマッチのビジュアライザいろいろ(threecourse さん)](https://threecourse.hatenablog.com/entry/2021/05/04/181226)
- [簡易にビジュアライザを作る方法の調査記(gobi さん)](https://gobi-tk.hatenablog.com/entry/2023/04/16/184952)

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
  - plotly
  - tcl/tk
- gnuplot
- c++
  - OpenSiv3D https://github.com/Siv3D/OpenSiv3D
  - SDL
  - MatPlot++ https://alandefreitas.github.io/matplotplusplus/
- js
  - p5.js https://p5js.org/
  
