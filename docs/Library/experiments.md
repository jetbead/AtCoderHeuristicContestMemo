# 実験、環境構築

[TOC]

## コンパイラ設定

- AtCoderの各コンテストtopページのfooter部分にある「ルール」から、使用可能な言語とコンパイラオプション、実行コマンドが確認できる

## サーバー環境

- 各コンテストの「コードテスト」で言語をBash等にすることで、サーバー環境の情報をコマンドで確認できる
  - `cat /proc/cpuinfo`
    - model name
    - flags
    - など
  - ローカル環境でクロック数などを揃える場合に確認する

## 設定ファイル

自分が使っている設定のメモ。

### .clang-format

```
BasedOnStyle: Google
IndentWidth: 4
AllowShortIfStatementsOnASingleLine: true
AllowShortFunctionsOnASingleLine: false
ColumnLimit: 100
```

## Links

- https://speakerdeck.com/butsugiri/increasing-number-of-attempts-ver-2021
- https://github.com/abhishekkrthakur/approachingalmost
- https://takuti.me/ja/note/data-science-project-structure/
- https://upura.hatenablog.com/entry/2018/12/28/225234
- https://goodresearch.dev/
- https://speakerdeck.com/hpprc/zi-yuan-tositejian-rushi-yan-puroguramu
- https://twitter.com/mamas16k/status/1660011592870948864
