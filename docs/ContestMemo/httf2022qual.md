# HACK TO THE FUTURE 2022 予選
- https://atcoder.jp/contests/future-contest-2022-qual

## 問題概要
- N=1000個のタスクをM=20人のチームメンバーに割り振りたい
    - 各タスクは高々1人に割り振る
    - タスクは割り当てられると、そのタスクが終わるまで他のタスクは割り当てられない
- 各メンバーはK種類の技能レベルがあり、ベクトルsで表されるが、入力としては与えられない
- 各タスクは各技能に対する要求レベルが決まっており、ベクトルdで表され、入力として与えられる
    - また、タスク間には依存関係(1000<=R<=3000個の有向辺で与えられる)があり、あるタスクを割り振るためにはそれに依存するすべてのタスクが前日までに終わっている必要がある
- メンバーjがタスクiを行った場合、「各技能の要求レベルに満たない分」の合計をwとして、w=0なら1日、w>0ならw±r(-3～3の一様乱数)日だけかかる
- できるだけ短い日数ですべてのタスクが終わるように、各日のタスクの割り当てを求めよ
    - interactive問題で、各日ごとに、完了したタスクを受け取り、その日の割当を返却するプログラムを作成せよ

## 時間
240時間

## 解説
(50位まで&発言を見つけられた方のみ)

- [解説放送(終了直後)](https://www.youtube.com/watch?v=H_MuLN6L-r4)
- [1位eivourさん](https://twitter.com/contramundum2/status/1459478557210537985)
    - https://twitter.com/contramundum2/status/1459487317631012865
- [2位phocomさん](https://twitter.com/_phocom/status/1459461706619699201)
- [3位toameさん](https://twitter.com/41Toame/status/1459861272049041412)
- [4位bowwowforeachさん](https://twitter.com/bowwowforeach/status/1459466965433470977)
    - https://twitter.com/bowwowforeach/status/1459490991153422336
- [5位ValGrowthさん](https://twitter.com/ValGrowth/status/1459465199216652289)
    - https://twitter.com/ValGrowth/status/1459463370781097987
- [6位c7c7さん](https://twitter.com/C7C7LL/status/1459462826930552836)
- 7位ynasuさん
- [8位Shun_PIさん](https://twitter.com/Shun___PI/status/1459463013832880133)
- 9位yokozuna57さん
- [10位iehnさん](https://twitter.com/arimasenu/status/1459461165969395712)
- 11位simanさん
- [12位NKTさん](https://twitter.com/inani_waon/status/1459461945615020032)
    - https://twitter.com/inani_waon/status/1459479269453623302
    - https://twitter.com/inani_waon/status/1459542680073957376
    - https://inaniwa.hatenablog.com/entry/2021/11/15/072751
- [13位iwashi31さん](https://twitter.com/iwashi31/status/1459461324287582210)
    - https://twitter.com/iwashi31/status/1459462724547579907
    - https://twitter.com/iwashi31/status/1459463107860844547
    - https://twitter.com/iwashi31/status/1459465190890631173
    - https://twitter.com/iwashi31/status/1459466272383504389
- [14位yochanさん](https://twitter.com/yochan_tech/status/1459462014615851008)
    - https://twitter.com/yochan_tech/status/1459466656225558528
- [15位gazelleさん](https://twitter.com/gzlcp/status/1459462267331051521)
    - https://twitter.com/gzlcp/status/1459463457900998658
    - https://twitter.com/gzlcp/status/1459463747970682880
    - https://twitter.com/gzlcp/status/1459464174070038528
    - https://twitter.com/gzlcp/status/1459464947822624768
    - https://twitter.com/gzlcp/status/1459465248499716097
- 16位rabotさん
- 17位tokuさん
- 18位tomerunさん
- 19位shamioさん
- [20位kawateaさん](https://twitter.com/kawatea03/status/1459464321768259585)
- 21位ytknさん
- [22位hokekyo1210さん](https://twitter.com/_hokekyo1210/status/1459479032932990977)
- [23位EmKさん](https://twitter.com/EmKjp/status/1459465646371053572)
- [24位shibh308さん](https://twitter.com/shibh308/status/1459469659468877825)
- [25位assyさん](https://twitter.com/assy1028/status/1459461980347985924)
- 26位krさん
- [28位terry_u16さん](https://twitter.com/terry_u16/status/1459462317771747328)
    - https://twitter.com/terry_u16/status/1459470465354076167
- [29位sumoooruさん](https://twitter.com/sumoooru/status/1459478589309550596)
- [30位yupiteruさん](https://twitter.com/yupiteru_kun/status/1459461186466975751)
- [31位kensさん](https://twitter.com/kens_kyopro/status/1459462599339556866)
- 32位y_kawanoさん
- [33位ebicochinealさん](https://twitter.com/ebicochineal/status/1459487058087407617)
- [34位yowaさん](https://twitter.com/yowa/status/1459985209949302784)
    - https://twitter.com/yowa/status/1460116477097680896
- [35位tokotoko7777さん](https://twitter.com/whimsicott_opu/status/1459472590796308484)
- 36位rs02さん
- [37位itigoさん](https://twitter.com/itigo_purokonn/status/1459466863201587202)
    - https://twitter.com/itigo_purokonn/status/1459469271629582343
- [38位Trineutronさん](https://twitter.com/trineutron/status/1459471474805198848)
- 39位uminorosujinさん
- [40位wanuiさん](https://twitter.com/gmeriaog/status/1459480733433548802)
- [41位keep_OCさん](https://twitter.com/keep_OC/status/1459462090713088000)
- 42位cirno3153さん
- 43位besukohuさん
- [44位takumi152さん](https://twitter.com/takumi152/status/1459464409651171334)
    - https://twitter.com/takumi152/status/1459465554402889728
- 45位spica314さん
- 46位primenumberさん
- [47位takoshiさん](https://twitter.com/takoshiiiiiiiii/status/1459464790489780225)
- [48位kozimaさん](https://twitter.com/t33f/status/1459464692405915651)
    - https://twitter.com/t33f/status/1459467052263952385
    - https://twitter.com/t33f/status/1459467759335575554
    - https://twitter.com/t33f/status/1459478143136256003
- 49位shotoyooさん
- [50位nagissさん](https://twitter.com/lgeuwce/status/1459461720624095232)
    - https://twitter.com/lgeuwce/status/1459469988838862851
    - https://twitter.com/lgeuwce/status/1459475887724789760
    - https://twitter.com/lgeuwce/status/1460106180005564416
    - https://twitter.com/lgeuwce/status/1459677513970434050

## Links
- [agwさんTwitterまとめ](https://togetter.com/li/1802420)
- [Twitter hashtag HTTF](https://twitter.com/hashtag/HTTF)


## 個人的メモ
