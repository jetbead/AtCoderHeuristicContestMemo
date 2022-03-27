---
title: HTTF2022予選
---

# HACK TO THE FUTURE 2022 予選

- https://atcoder.jp/contests/future-contest-2022-qual

## 問題概要

- N=1000 個のタスクを M=20 人のチームメンバーに割り振りたい
  - 各タスクは高々 1 人に割り振る
  - タスクは割り当てられると、そのタスクが終わるまで他のタスクは割り当てられない
- 各メンバーは K 種類の技能レベルがあり、ベクトル s で表されるが、入力としては与えられない
- 各タスクは各技能に対する要求レベルが決まっており、ベクトル d で表され、入力として与えられる
  - また、タスク間には依存関係(1000<=R<=3000 個の有向辺で与えられる)があり、あるタスクを割り振るためにはそれに依存するすべてのタスクが前日までに終わっている必要がある
- メンバー j がタスク i を行った場合、「各技能の要求レベルに満たない分」の合計を w として、w=0 なら 1 日、w>0 なら w±r(-3 ～ 3 の一様乱数)日だけかかる
- できるだけ短い日数ですべてのタスクが終わるように、各日のタスクの割り当てを求めよ
  - interactive 問題で、各日ごとに、完了したタスクを受け取り、その日の割当を返却するプログラムを作成せよ

## 時間

240 時間

## 解説

(50 位まで&発言を見つけられた方のみ)

- [解説放送(終了直後)](https://www.youtube.com/watch?v=H_MuLN6L-r4)
- [解説まとめ(公式)](https://atcoder.jp/contests/future-contest-2022-qual/editorial/2931?lang=ja)
- [1 位 eivour さん](https://twitter.com/contramundum2/status/1459478557210537985)
  - https://twitter.com/contramundum2/status/1459487317631012865
  - https://qiita.com/contramundum/items/52609b5a4c943bc6a275
- [2 位 phocom さん](https://twitter.com/_phocom/status/1459461706619699201)
- [3 位 toame さん](https://twitter.com/41Toame/status/1459861272049041412)
- [4 位 bowwowforeach さん](https://twitter.com/bowwowforeach/status/1459466965433470977)
  - https://twitter.com/bowwowforeach/status/1459490991153422336
- [5 位 ValGrowth さん](https://twitter.com/ValGrowth/status/1459465199216652289)
  - https://twitter.com/ValGrowth/status/1459463370781097987
- [6 位 c7c7 さん](https://twitter.com/C7C7LL/status/1459462826930552836)
- 7 位 ynasu さん
- [8 位 Shun_PI さん](https://twitter.com/Shun___PI/status/1459463013832880133)
- 9 位 yokozuna57 さん
- [10 位 iehn さん](https://twitter.com/arimasenu/status/1459461165969395712)
- 11 位 siman さん
- [12 位 NKT さん](https://twitter.com/inani_waon/status/1459461945615020032)
  - https://twitter.com/inani_waon/status/1459479269453623302
  - https://twitter.com/inani_waon/status/1459542680073957376
  - https://inaniwa.hatenablog.com/entry/2021/11/15/072751
- [13 位 iwashi31 さん](https://twitter.com/iwashi31/status/1459461324287582210)
  - https://twitter.com/iwashi31/status/1459462724547579907
  - https://twitter.com/iwashi31/status/1459463107860844547
  - https://twitter.com/iwashi31/status/1459465190890631173
  - https://twitter.com/iwashi31/status/1459466272383504389
- [14 位 yochan さん](https://twitter.com/yochan_tech/status/1459462014615851008)
  - https://twitter.com/yochan_tech/status/1459466656225558528
- [15 位 gazelle さん](https://twitter.com/gzlcp/status/1459462267331051521)
  - https://twitter.com/gzlcp/status/1459463457900998658
  - https://twitter.com/gzlcp/status/1459463747970682880
  - https://twitter.com/gzlcp/status/1459464174070038528
  - https://twitter.com/gzlcp/status/1459464947822624768
  - https://twitter.com/gzlcp/status/1459465248499716097
- [16 位 rabot さん](https://qiita.com/tanaka-a/items/dc50b224f984335ab986)
- 17 位 toku さん
- 18 位 tomerun さん
- 19 位 shamio さん
- [20 位 kawatea さん](https://twitter.com/kawatea03/status/1459464321768259585)
- 21 位 ytkn さん
- [22 位 hokekyo1210 さん](https://twitter.com/_hokekyo1210/status/1459479032932990977)
- [23 位 EmK さん](https://twitter.com/EmKjp/status/1459465646371053572)
- [24 位 shibh308 さん](https://twitter.com/shibh308/status/1459469659468877825)
- [25 位 assy さん](https://twitter.com/assy1028/status/1459461980347985924)
- 26 位 kr さん
- [28 位 terry_u16 さん](https://twitter.com/terry_u16/status/1459462317771747328)
  - https://twitter.com/terry_u16/status/1459470465354076167
- [29 位 sumoooru さん](https://twitter.com/sumoooru/status/1459478589309550596)
- [30 位 yupiteru さん](https://twitter.com/yupiteru_kun/status/1459461186466975751)
- [31 位 kens さん](https://twitter.com/kens_kyopro/status/1459462599339556866)
- 32 位 y_kawano さん
- [33 位 ebicochineal さん](https://twitter.com/ebicochineal/status/1459487058087407617)
- [34 位 yowa さん](https://twitter.com/yowa/status/1459985209949302784)
  - https://twitter.com/yowa/status/1460116477097680896
- [35 位 tokotoko7777 さん](https://twitter.com/whimsicott_opu/status/1459472590796308484)
- 36 位 rs02 さん
- [37 位 itigo さん](https://twitter.com/itigo_purokonn/status/1459466863201587202)
  - https://twitter.com/itigo_purokonn/status/1459469271629582343
- [38 位 Trineutron さん](https://twitter.com/trineutron/status/1459471474805198848)
- 39 位 uminorosujin さん
  - https://uminorosu.hatenablog.com/entry/2021/11/23/151939
- [40 位 wanui さん](https://twitter.com/gmeriaog/status/1459480733433548802)
- [41 位 keep_OC さん](https://twitter.com/keep_OC/status/1459462090713088000)
- 42 位 cirno3153 さん
- 43 位 besukohu さん
- [44 位 takumi152 さん](https://twitter.com/takumi152/status/1459464409651171334)
  - https://twitter.com/takumi152/status/1459465554402889728
  - https://takumi152.hatenablog.jp/entry/2021/11/13/191713
- 45 位 spica314 さん
- 46 位 primenumber さん
- [47 位 takoshi さん](https://twitter.com/takoshiiiiiiiii/status/1459464790489780225)
- [48 位 kozima さん](https://twitter.com/t33f/status/1459464692405915651)
  - https://twitter.com/t33f/status/1459467052263952385
  - https://twitter.com/t33f/status/1459467759335575554
  - https://twitter.com/t33f/status/1459478143136256003
- 49 位 shotoyoo さん
- [50 位 nagiss さん](https://twitter.com/lgeuwce/status/1459461720624095232)
  - https://twitter.com/lgeuwce/status/1459469988838862851
  - https://twitter.com/lgeuwce/status/1459475887724789760
  - https://twitter.com/lgeuwce/status/1460106180005564416
  - https://twitter.com/lgeuwce/status/1459677513970434050

## Links

- [agw さん Twitter まとめ](https://togetter.com/li/1802420)
- [Twitter hashtag HTTF](https://twitter.com/hashtag/HTTF)

## 個人的メモ

- ざっくり「スキル推定」と「タスク割当」パート
- 特に、「タスク割当」は、手が空いている人に割り当てだと良くないケースがあり、タスク中の人にも割り当てを考える部分がどの程度できたかで差が出ていた模様

### スキル推定

- それまでに選択したタスクとかかった時間を保持して、絶対誤差/二乗誤差(MSE)あたりが最小になるように山登り/焼きなましなど
  - 自由度があるので、正則化項(L2 ノルムとか)なども
- 最初の方は、あえて探索要素をいれるなど
- ベイズ推定、MCMC

### タスク割り当て

- クリティカルパス(一番時間がかかるタスクのパス)がボトルネックになるので、そのようなパスを重視するなどは必要
  - 深さ、などでも良い
  - クリティカルパス分析
- greedy なタスク割当で難しいところは、人が余ってないときや強い人がタスク中のときに、タスクが終わったからといってその人が不得意な仕事を割り当てることになりえること
  - そこで、「人(全員)と、割り当て可能なジョブ」の組み合わせに対して評価値を考える
    - 手が開いてる人に対してだけだと、評価関数をちゃんとやってても、活かされないような割り当てする可能性がある
    - タスク中でも、終わってからタスクをやった場合というのを考える
    - ここが、R が大きいときに重要で、暫定 97k 点台と 101k 点台ぐらいの差がでていたキモ
  - 反省: 自分は「終わりが近い人も含めて割当を考える」だけをしていた(全員ではない)
  - 人 i にタスク j を割り当てるときの評価関数
    - パスの重み/最長経路/要求技能レベルの総和
    - かかる時間(タスク終了見込み時間)
    - 自分にどのぐらい向いている仕事か
    - あえて少し待ってから仕事をするペナルティ
    - 全タスクが終わるまでにかかる最短時間(可能であれば。終盤だけでも)
    - 先読みしたときのコスト(依存が強い場合)
    - などで調整
  - 実際の割り当て方は、評価関数をもとに、貪欲、山登り/焼きなましなど
- 割り当て方は、最小費用流＋アドホックや、LP ソルバで計算していた方も
  - マッチングを最小費用流で解くやつで、かかる時間の合計が小さくなるような割り当てを求める
    - s->人の部分で辺を複数本に分けてそれぞれコストを分けることで均等になるような工夫(terry_u16 さん)
  - 能力が低い人は別処理、など

### その他

- R によって問題の性質が違っていた
  - R が小さい(1000 付近)と、依存が少ないので、やれるタスクが多い → 自分が得意な仕事ができる
  - R が大きい(3000 付近)と、依存が多く、やれるタスクが少なく、暇になっている人が多い → 得意な人が全部やったほうが早いのに、手があいてるからと不得意な人が手をつけてしまうと逆に遅くなる(あえてやらないほうがよい)
- 「R ごとに調整」するとよかった可能性
  - 3 つぐらいに分割してそれぞれでパラメータを分ける、など
  - ローカルの評価でも、それぞれの R ごとに評価データを分けて確認するなどしたほうが良かった可能性
    - https://twitter.com/_phocom/status/1460164219006259201
    - https://twitter.com/iwashi31/status/1460192127338508292
    - テストケースが少ないと偏りやすい
    - 3000 ケースで評価していればそこまで偏らないのでよいか
  - ヒートマップを見る
    - https://tsutaj.hatenablog.com/entry/2021/11/13/190712
