# How to get started!

AHC の始め方など、まとめておきます。

## AtCoder Heuristic Contest(AHC)とは?

from https://atcoder.jp/contests/ahc001

> AtCoder Heuristic Contest（AHC）とは？  
> AtCoder にて新たに定期的に開催されるプログラミングコンテストです。ABC/ARC/AGC などのアルゴリズムコンテストと異なり、最適解を出すのが難しい問題に対し、出来るだけ良い解を作成するコンテストとなります。出題例などについては過去の類似形式のコンテストを参照下さい。

### ABC/ARC/AGCと比較して

- 最適解を出す必要がない
  - 解答の質が「スコア」で表現され、より良いスコアを目指すため、最適解を出さなくても良い
  - スコアが良くなるように試行錯誤する
- 非減少型レーティング
  - AHCのレートは、ABC/ARC/AGCのレーティングシステムとは別になっており、非減少型のレーティングになっている
  - なので、パフォーマンスが悪かったからといってレートが下がらないシステムのため、参加するハードルがABC/ARC/AGC等と比べて低い
- 基本的にコンテストが長期間
  - おおよそコンテスト開催期間は、短期は4時間程度、長期は1週間〜2週間程度のため、ABC/ARC/AGCよりも長く、1問か2問の問題に取り組む
- 解法/アプローチの自由度が高い
  - 基本的に様々な解法が有効で、いろんなアプローチを取ることができる
    - コンテスト後の参加者の解説などで特定の手法が出やすくても、有効なだけで、他の手法がダメとは限らない
  - また、入力のパラメータによっては、単一の手法ではなく、パラメータによって異なる手法を適用できるようにするなどもスコア改善には必要になる
  - 高スコアを出すためには、十分な最適化や問題の性質の有効活用などが必要なため、深い考察や調査、試行錯誤が必要

## 取り組み方解説

どのように取り組めばよいかの解説

- [Introduction to Heuristics Contest 解説(公式)](https://img.atcoder.jp/intro-heuristics/editorial.pdf)
  - [Introduction to Heuristics Contest](https://atcoder.jp/contests/intro-heuristics)
- [AtCoder Heuristic Contest 001 (AHC001) 初心者向け解説(terry_u16 さん)](https://www.terry-u16.net/entry/ahc001-how-to)
- [競プロ解法紹介～レベル別マラソンの戦い方～(tsukammo さん)](https://qiita.com/tsukammo/items/7041a00e429f9f5ac4ae)
- [マラソンマッチにおける精神論(chokudai さん)](https://chokudai.hatenablog.com/entry/2014/12/04/000132)
- [マラソンマッチの取り組み方(colun さん)](http://www.colun.net/archives/294)
- [AHC のおはなし(NNMochi さん)](https://trap.jp/post/1304/)
- [Topcoder マラソンマッチの探索問題で重要なこと(takapt0226 さん)](https://qiita.com/takapt0226/items/b2f6d1d77a034b529e21)
- [競プロ〜ヒューリスティック/マラソン事始め〜(tanaka-a さん)](https://qiita.com/tanaka-a/items/3c2a1bca63759ec71e7f)

## 感想戦、解説記事

- コンテスト終了後は、他の人の解法などが twitter やブログなどで共有されることが多い
  - 自分の手法や問題分析との違い、次回以降に有用そうな情報などを知ることができる
- [ContestMemo](../ContestMemo/index.md)には、過去のコンテストの情報や反省をまとめています

## 過去問

ヒューリスティック系のコンテストは、AtCoder 以外にもあり、過去問とその上位解法を知っておくことで、考察に役立つ可能性がある。
解法ブログや twitter まとめなどが参考になる。

- AHC の過去問
- AtCoder のマラソンタイプのコンテストの過去問
  - [AtCoder 過去のコンテスト(マラソン)](https://atcoder.jp/contests/archive?ratedType=0&category=1200&keyword=)
- TopCoder Marathon マッチの過去問
- その他最適化コンテストの過去問

## ツールセット

- [マラソンマッチのためのツールセットはどのようなものになるか(kmyk さん)](https://kmyk.github.io/blog/blog/2021/03/18/what-is-a-toolset-for-marathon-matches/)

## 資料集

- [マラソンマッチの資料集(threecourse さん)](https://threecourse.hatenablog.com/entry/%3Fp%3D1164)
- [ヒューリスティック最適化資料集(threecourse さん)](https://heuristic-ja.growi.cloud/)
