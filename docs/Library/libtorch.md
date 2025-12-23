# LibTorch

PyTorchのC++版のライブラリ。AtCoder環境で使える。

## メモ

- 試してみた感じ、main()の前後あたりで別途処理が入ってそうで、main()内で計測できない数百ms程度のオーバーヘッドがありそう
  - main()内で2秒とかに収めてもTLEになってしまう

## Links

- https://docs.pytorch.org/cppdocs/frontend.html
