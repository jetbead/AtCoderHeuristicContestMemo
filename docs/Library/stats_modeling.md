# 統計モデリング

- 推定系の問題などで、得られたデータから推定を行う方法

## Links

- Hit&Blowで学ぶベイズ推定：理論から実装まで (4etaさん)
  - https://zenn.dev/4eta/articles/072132e44e2075
  - https://zenn.dev/4eta/articles/c00fcfd07e0238

## ベイズの定理

- $P(A|B) = \frac{P(B|A)P(A)}{P(B)}$

### 計算メモ

- 観測データDが与えられたとして、iがjである確率$P(i=j|D)$は、$\frac{P(D|i=j)P(i=j)}{P(D)}$
  - 事前に情報ない場合は$P(i=j)$はすべて同じ値となる
- 更新後、$P(i=j|D)$は、$P(i=j'|D)$など他のに対して計算できる場合、$P(i=j) = \frac{P(i=j|D)}{\sum{P(i=j'|D)}}$
  - $P(i=j)$、$P(D)$は消せて、$P(i=j|D)$は尤度・同時確率なので、確率分布に応じて積などとって計算

## 多変数正規分布(多変量ガウス分布)

- 正規分布を仮定して、要素数分の次元の正規分布でモデリング
  - 多次元(多変量)の場合、平均や分散などはベクトルや行列として扱う
- 条件付き正規分布
  - 2つの変数集合の同時分布が正規分布のとき、一方の変数集合が与えられたときのもう一方の集合の条件付き分布も正規分布になる
    - どちらの変数集合の周辺分布も正規分布
  - ある隠れ変数と観測された値との関係がわかっている場合、観測された値が与えられた場合の隠れ変数の平均、分散が求められる
    - PRML2.3.1〜2.3.3
    - https://speakerdeck.com/yos1up/ahc003-2wei-jie-fa?slide=10

```python
# 適当な例: xが正規分布に従う確率変数で、それらの線形変換後の値yだけ観測できるとき、各xの分布を推定
#  (x1,x2,x3): 平均5、平均3、平均0、分散は4の正規分布に従う
#  (y1,y2,y3): y1=x1+x3, y2=x1+x2, y3=x1+x2+x3の関係になっている観測できる値
import numpy as np

# xとyとの関係(y=Ax+b)
A = np.array([
    [1, 0, 1],
    [1, 1, 0],
    [1, 1, 1]
])
b = np.zeros(3)

# 観測yでの精度行列L
sgm = np.zeros((3,3))
sgm[0,0] = sgm[1,1] = 8.0
sgm[2,2] = 12.0
L = np.linalg.inv(sgm)

# 観測&ベイズ更新
results = []
mu = np.zeros(3) # 平均
lmd = np.ones((3,3)) * 1e-9 # 精度行列(分散の逆行列)
for i in range(1000):
    # 正規乱数
    x1 = np.random.normal(5, 4)
    x2 = np.random.normal(3, 4)
    x3 = np.random.normal(0, 4)
    # 観測できる値
    y1 = x1 + x3
    y2 = x1 + x2
    y3 = x1 + x2 + x3
    # ベイズ更新
    cov = np.linalg.inv(lmd + (A.T @ L @ A))
    E = cov @ ((A.T @ L @ (np.array([y1,y2, y3]) - b).T) + (lmd @ mu))
    mu = E
    lmd = np.linalg.inv(cov)
    results.append(E)
```

### 非正規なデータへの正規分布仮定

- データが正規分布に従っているとは言えなくても、標本平均の分布は正規分布に近い
  - 平均を推定しようとしている場合などはうまくいく
  - 逆に、他の統計量を推定しようとする場合はよくない可能性がある

### ラプラス近似

## ベイズ推定

- 例えば、平均値のわからない(分散はわかる)正規分布に従って生成されたのがわかっているデータが与えられたとき、可能性の高い平均値を求めたい
- 平均値を確率分布(事前分布)として考えて、ベイズの定理でデータ点を使って分布を更新(ベイズアップデート)し、事後分布を得る
- 事後分布は事前分布と同じとは限らないが、同じになるようなケース(共役事前分布)では、各パラメータの更新式だけになる
- 事後分布がいい感じにならない場合は、事後分布からサンプリングして、それを使って代表値(要約値)などを求める(MCMC)、など

- https://platinum-prog.hatenablog.com/entry/2023/05/20/231200
- https://cookie-box.hatenablog.com/entry/2021/01/31/223006

### 共役事前分布

- 正規分布
  - 分散(精度)既知: 正規分布
  - 平均既知: ウィシャート分布、逆ウィシャート分布
    - 逆ガンマ分布
  - 平均、分散未知: 正規-ウィシャート分布
- 多項分布
  - ディリクレ分布
    - https://jetbead.hatenablog.com/entry/20120216/1329408665

#### 正規分布(分散既知)の場合

- 平均θの事前分布: $N(\mu,\tau^2)$
- 事後分布: $N(\mu',\tau'^2)$
  - $\mu' = \frac{(1/\tau^2)\mu + (n/\sigma^2) \bar{x}}{1/\tau^2 + n/\sigma^2}$
  - $1/\tau'^2 = 1/\tau^2 + n/\sigma^2$
    - $n$: 観測個数
    - $x$: 観測値の平均値
    - $\sigma^2$: 分散(既知)

### MCMC

#### ギブスサンプリング

## 確率行列

- 確率行列
  - 各行の和が1であるような行列
- 二重確率行列
  - 各行・各列の和がそれぞれ1であるような行列
- 置換行列
  - 各行、および、各列のなかで1が1つだけありそれ以外は0であるような行列

### Sinkhornアルゴリズム、Sinkhorn-Knoppアルゴリズム

- 各行、各列を交互に正規化を繰り返すと二重確率行列に近似できる

## エントロピー

- https://ja.wikipedia.org/wiki/%E3%82%A8%E3%83%B3%E3%83%88%E3%83%AD%E3%83%94%E3%83%BC
- https://ja.wikipedia.org/wiki/%E6%83%85%E5%A0%B1%E9%87%8F
- (情報理論における)エントロピーとは、確率変数が持つ情報の量を表す尺度(情報量)
- 確率変数Xに対するエントロピーは、$H(X) = -\sum_{x}{P(x)logP(x)}$
  - 一般的には、すべてのxで等確率の場合H(X)は最大で、特定のxだけ確率が1になっている場合に最小(=0)
- 条件付きエントロピー
  - $H(X|y) = -\sum_{x}{P(x|y)logP(x|y)}$
    - 確率変数Yがyの場合のエントロピー
  - $H(X|Y) = \sum_{y}{P(Y=y) H(X|Y=y)}$
    - yに関する平均(期待値)

### エントロピーの差(相互情報量)

- https://ja.wikipedia.org/wiki/%E7%9B%B8%E4%BA%92%E6%83%85%E5%A0%B1%E9%87%8F
- https://manabitimes.jp/math/1403
- ベイズ推定などで、ある情報Aを得ることで事後確率分布が変わるような場合に、事前分布のエントロピーと事後分布のエントロピーの差を考える
  - $H(X) - H(X|A)$
  - この差が大きいほど、情報Aが、特定のxである確率が高くできるような情報であると判断できる
- この差は相互情報量とも呼ばれる
- 2つの確率変数X,Yについて、$I(X;Y)=\sum_{x}\sum_{y}{P(x,y)log\frac{P(x,y)}{P(x)P(y)}}$
- 性質
  - $I(X;Y) = H(X) - H(X|Y)$
  - $I(X;Y) = H(Y) - H(Y|X)$
  - $I(X;Y) = H(X) + H(Y) - H(X,Y)$

## カルマンフィルタ

- https://ja.wikipedia.org/wiki/%E3%82%AB%E3%83%AB%E3%83%9E%E3%83%B3%E3%83%95%E3%82%A3%E3%83%AB%E3%82%BF%E3%83%BC
- https://www.slideshare.net/slideshow/ss-250284912/250284912
- https://qiita.com/s-yonekura/items/7cdc99ba444cdbd4e880
- https://inzkyk.xyz/kalman_filter/multivariate_kalman_filters
- https://www.kalmanfilter.net/JP/default_jp.aspx

### 単純な例: 1次元でのロボットの状態推定(観測誤差、移動誤差あり) 

```cpp
double rx = 0; // 初期位置
double rv = 0; // 初期速度
double a = 0.1; // 制御入力(加速度)
const double epsilon = 1.0; // 速度に乗せる誤差
const double delta = 1.0; // 観測誤差
Vector2d x{0, 0}; // 推定状態(位置、速度)
Matrix2d P{{1.0e-6, 0}, {0, 1.0e-6}}; // 推定値の分散

const Matrix2d F{{1, 1}, {0, 1}};
const Vector2d B{1, 1};
const Matrix2d Q{{epsilon * epsilon, 0}, {0, epsilon * epsilon}};
const Matrix2d R{{delta * delta, 0}, {0, 1}};
const Matrix2d I{{1, 0}, {0, 1}};

for (int t = 0; t < 100; t++) {
    // 真の状態を更新
    rv += a;
    rv += normal_rand(0, epsilon * epsilon);
    rx += rv;

    // 推定状態を更新
    Vector2d ex = F * x + B * a;
    Matrix2d eP = F * P * F.transpose() + Q;
    
    // 観測は、実際の位置(誤差あり)を取得
    double z = rx + normal_rand(0, delta * delta); // 観測値
    
    Vector2d y{z - ex(0), 0};
    Matrix2d K = eP * (eP + R).inverse(); // カルマンゲイン
    Vector2d x2 = ex + K * y;
    Matrix2d P2 = (I - K) * eP;
    // 推定状態を更新
    x = x2;
    P = P2;

    // このターンでの真の位置と推定位置
    cout << t << "\t" << rx << "\t" << x(0) << endl;
}
```

### 単純な例: AHC040の幅の推定(移動なし、観測誤差のみ)

- [AHC040推定パート](../blog/posts/20241222_ahc040_estimate.md)

### パーティクルフィルタ

- https://ja.wikipedia.org/wiki/%E7%B2%92%E5%AD%90%E3%83%95%E3%82%A3%E3%83%AB%E3%82%BF
- https://www.ieice.org/jpn/books/kaishikiji/2005/200512.pdf
- https://qiita.com/MoriKen/items/da8d290dcefad81b478d
- [第一回マスターズ選手権-決勝-](../ContestMemo/masters2024-final.md)


## 最小二乗法

- https://ja.wikipedia.org/wiki/%E6%9C%80%E5%B0%8F%E4%BA%8C%E4%B9%97%E6%B3%95
- https://manabitimes.jp/math/942
- https://manabitimes.jp/math/1054
- https://manabitimes.jp/math/1128
- https://x.com/Shun___PI/status/1866061590661108020
- 正規方程式

### 正則化あり最小二乗法

- 正則化項あり二乗和誤差$E(x)=\frac{1}{2} \| Xx-t \| + \frac{\lambda}{2} \| x \|^2$を最小化
- $x = (X^T X + \lambda I)^{-1} X^T t$

## ベイズ最適化

- https://www.slideshare.net/issei_sato/bayesian-optimization

## 問題

- [AHC003](../ContestMemo/ahc003.md)
- [HTTF2022qual](../ContestMemo/httf2022qual.md)
- [AHC016](../ContestMemo/ahc016.md)
- https://yukicoder.me/problems/no/5006
  - https://twitter.com/square10011/status/1535911569607245824
- [AHC022](../ContestMemo/ahc022.md)
- [AHC025](../ContestMemo/ahc025.md)
- [AHC030](../ContestMemo/ahc030.md)
- [masters2024-final](../ContestMemo/masters2024-final.md)
- [AHC040](../ContestMemo/ahc040.md)
