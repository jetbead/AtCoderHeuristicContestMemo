# 行列ライブラリEigen

- ヘッダオンリーなC++ライブラリ
  - C++標準ライブラリ以外の依存関係はない
  - ビルドも不要(ドキュメントとテストだけ)

## Links

- 公式
  - https://eigen.tuxfamily.org/index.php?title=Main_Page
- 公開コピー誌 行列ライブラリEigenのメモ(暗黒通信団)
  - http://ankokudan.org/d/dl/pdf/pdf-eigennote.pdf
- C++行列計算ライブラリEigen入門
  - https://qiita.com/yohm/items/a03006790dc1e54a87be
- gitlab?
  - https://gitlab.com/libeigen/eigen

## 高速

- Expression Template(式テンプレート)による遅延評価
- 自動ベクトル化(SIMD)、ループ展開
- 固定サイズの行列で動的メモリ割り当ての回避、
- 大きな行列でのキャッシュの扱いへの配慮


