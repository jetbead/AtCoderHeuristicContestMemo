---
title: 日本橋ハーフ2021増刊号(RECRUIT)
---

# RECRUIT 日本橋ハーフマラソン 2021 ～増刊号～

- https://atcoder.jp/contests/rcl-contest-2021-long

## 問題概要

- N \* N マスのフィールドがあり、特定の時間に価値 v の野菜が現れ、時間が立つと消える
- 野菜を収穫するには収穫機が必要で、価格は「(現在持っている台数+1)^3」円かかる
- T ターン中、「収穫機を買って設置する」か「収穫機を移動する」か選ぶ
  - 設置位置や移動先には他の収穫機があってはいけない
- 各ターンの終了時点で、そのマスに野菜と収穫機がある場合収穫され、「野菜の価値 \* その収穫機と 4 方向で連結している収穫機の数」円だけ得られる
- 最終的に得られるお金を最大化せよ

## 時間

168 時間

## 解説

(50 位まで&発言を見つけられた方のみ)

- 1 位 Jirotech さん
  - https://twitter.com/Jiro_tech15/status/1436979563079163907
  - https://vimeo.com/603126256?ref=tw-share
  - https://twitter.com/Jiro_tech15/status/1437008103799541764
- [4 位 phocom さん](https://twitter.com/_phocom/status/1436981894499880967)
- [7 位 fuppy0716 さん](https://twitter.com/fuppy_kyopro/status/1436980675916144640)
- [8 位 siman さん](https://simanman.hatenablog.com/entry/2021/09/13/155633)
- [9 位 kawatea さん](https://twitter.com/kawatea03/status/1436978583197548546)
- [10 位 bowwowforeach さん](https://twitter.com/bowwowforeach/status/1436982453579616256)
- [12 位 fky\_さん](https://twitter.com/fkyrz_0111/status/1436978724826537984)
- [14 位 assy さん](https://twitter.com/assy1028/status/1436979233373360132)
- [16 位 Shun_PI さん](https://twitter.com/Shun___PI/status/1437012665293889539)
- [17 位 nagiss さん](https://twitter.com/lgeuwce/status/1436982098103992331)
- [18 位 shibh308 さん](https://twitter.com/shibh308/status/1436979647992909824)
- [20 位 wanui さん](https://twitter.com/gmeriaog/status/1437764559662505984)
- [21 位 tsukammo さん](https://twitter.com/tsukammo/status/1436983837645422599)
- [22 位 c7c7 さん](https://twitter.com/C7C7LL/status/1436979425359237130)
- [23 位 eivour さん](https://twitter.com/contramundum2/status/1437000102975926273)
- [24 位 cuthbert さん](https://twitter.com/ethylene_66/status/1437009336308436995)
- [25 位 eijirou さん](https://twitter.com/eijirou_kyopro/status/1436980647457738755)
- [27 位 hirokazu1020 さん](https://twitter.com/hirokazu1020/status/1436982503286317063)
- 28 位 prd_xxx さん
  - https://twitter.com/prd_xxx/status/1436981945963986951
  - https://twitter.com/prd_xxx/status/1436984188817707013
  - https://twitter.com/prd_xxx/status/1436999142924972034
  - https://prd-xxx.hateblo.jp/entry/2021/09/15/003013
- [29 位 yowa さん](https://twitter.com/yowa/status/1437071686537416706)
- [30 位 nrvft さん](https://twitter.com/nrvkpr/status/1436998757262893058)
- [32 位 udon1206 さん](https://jupiro.hatenablog.com/entry/rcl-2021)
- [36 位 Kiri8128 さん](https://twitter.com/kiri8128/status/1436980713140588549)
- [44 位 shim0 さん](https://twitter.com/idolikeshishamo/status/1436981033778380801)
- [47 位 yochan さん](https://twitter.com/yochan_tech/status/1436978962723270659)
- [48 位 komori3 さん](https://twitter.com/komora71_/status/1436983005382254596)
- [49 位 terry_u16 さん](https://twitter.com/terry_u16/status/1436978268935127044)

- [tomerun さん](https://twitter.com/tomerun/status/1438163487205724166)

## Links

- [agw さん Twitter まとめ](https://togetter.com/li/1771914)
- [Twitter hashtag R_procon](https://twitter.com/hashtag/R_procon)

## 個人的メモ

- 盤面に合わせてうにゃうにゃとアメーバが動くような感じ
  - 固定配置＋遊撃隊だと、盤面に合わないときにスコアが出せない(平均 5M 円超えは難しそう)
- 「連結性を維持して」は関節点(LowLink など)
  - 関節点以外を動かせる
  - (もしかして差分計算できる？)
- 野菜を収穫済みか？の保持の仕方
  - https://twitter.com/C7C7LL/status/1436986015088996354
  - 野菜は 5000 個だが、各マスの野菜の有無を状態に毎ターン更新すれば 16\*16=256 個で済む(State が軽くなる)
- 各操作は int1 つで持てる
  - https://jupiro.hatenablog.com/entry/rcl-2021
- マスの評価値(時間、空間)
  - 時間方向
    - 未来に出現/消滅する野菜に対して重み付け(先読み)
    - ターン t でマス x に発生する野菜の価値 v について、t までのターン数で減衰させる
  - 空間方向
    - 現在出現している野菜との距離に対して重み付け
    - 出現しているマス x の野菜の価値 v について、その距離で減衰させる(そこまで 0 点だったときの平均になる v/d 的な指数減衰など)
    - 取得済みを除く、消滅までの期間で距離を制限、複数の収穫機が同じマスを目指して動いて無駄にならないような工夫も必要？
- コーナーケース
  - [時刻 0 にひとつも野菜がないケース](https://twitter.com/_phocom/status/1436978429010710528)
- マスの評価関数+貪欲/ビームサーチ
  - 基本連結を保って、マスの評価値が最小のマスから最大のマスに動くような貪欲、または、いくつかのパターンを試すビームサーチ
  - (結構、同じアプローチも、評価関数の作り方や細かい実装などでスコアの差がでやすかった？)
- 焼きなましの可能性
  - https://twitter.com/t33f/status/1437003245621157894
  - https://qiita.com/kusano_k/items/eb44e796adf237ae35d6
  - 回収する野菜の集合を焼き鈍す、移動先の焼きなます
