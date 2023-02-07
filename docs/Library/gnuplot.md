# gnuplotで簡易ビジュアライザ

## 概要

- gnuplotは、2次元や3次元のグラフを作成するツールだが、簡単なビジュアライザとしても利用できる
  - c++でプログラムを書いてpltファイルを出力してプロットして確認、みたいな利用
  - pythonならmatplotlib
- AHCやTopcoder MMでは公式ビジュアライザが用意されており、そちらで十分な場合が多いが、より細かい情報や焼きなましの過程など確認したい場合、自分で用意したほうが良い
- よく使う機能をまとめておく
- (4系と5系での違いがあったりするので、使っているバージョンの本家マニュアルを参照したほうが良い)

## Links

- [公式ページ](http://www.gnuplot.info/)

## Usage
簡単な使い方としては、gnuplotのコマンド書いたファイル(例えば`vis.plt`)を用意して、`$ gnuplot vis.plt`と実行する。

<img src="../imgs/gnuplot.png" width=300>

```
# vis.pltの中身の例
set terminal pngcairo size 500,500
set output "output.png"
set size ratio 1
set grid
set nokey
set xrange [0:100]
set yrange [100:0]
set format x ""
set x2tics
set mxtics 10
set mx2tics 10
set mytics 10

set label 1 "start" at 5,5 front
set label 2 "start" at 80,80 front
set object 3 rect from 25,50 to 40,70 fc "#a3a3a3"
set object 4 rect from 60,20 to 80,25 fc "#a3a3a3"

plot "-" w lp pt 7, "-" w lp pt 7
5.0 5.0
25.0 60.0
15.0 80.0
e
80.0 80.0
100.0 60.0
63.0 25.0
40.0 55.0
60.0 70.0
e
```

## コマンドメモ

### set関連

```
# pngで出力、画像サイズ
set terminal pngcairo size 500,500

# 出力ファイル名
set output "output.png"

# サイズ比を1にする
set size ratio 1

# 大きな目盛の表示
set xtics 10
set ytics 10

# 小さな目盛の表示
set mxtics 10
set mxtics 10

# 軸の数字の非表示
set notics
または
set format x ""

# 格子の表示
set grid

# 凡例の非表示
set nokey

# 表示範囲
set xrange [0:100]
set yrange [0:100]
反転させる場合は、(5系からreverseは使えない)
set yrange [100:0]

# ラベルの表示
set xlabel "label x"
set ylabel "label y"

# タイトルの表示
set title "graph title"

# 上または右の目盛り
set x2tics
set y2tics
```

### object関連
`<id>`は連番でかぶらないように振っておく。

```
# 長方形の表示
set object <id> rect from 25,50 to 40,70 fc "#a3a3a3"

# 円(楕円)、多角形なども表示できる
(省略)

# 文字の表示(frontは描画要素より前に表示される)
set label <id> "start" at 5,5 front

# 文字を中央揃えで表示
set label <id> "start" center at 5,5 front
```

### font関連

```
set title "hoge" font "Times-Roman,20"
```

### plot関連

```
# file.datの1列目と3列目をx,yにプロット
plot "file.dat" using 1:3

# 線の種類(withなどはwと省略可能)
plot "file.dat" using 1:2 with 線の種類 linecolor rgb 色 ...

## 線の種類
lines: 直線
docs: 点
points: 記号
lp: lines+points
impluses: x軸に垂線を下ろす(棒グラフ)
steps,fsteps,histsteps: ヒストグラム
boxes: 棒グラフ
yerrorbars,xerrorbars,xyerrorbars: 誤差付きデータ点
vector: ベクトル

# インラインデータ(データを埋め込む場合、"-"を指定してeまでがデータとして扱われる)
plot "-" w lp
0 0
1 2
e

# 複数データ(同じファイルに、2行以上の空行を区切りとして複数データがある場合、0-indexで指定できる)
plot "file.dat" index 0, "" index 1

# インラインで複数データ
plot "-" w lp, "-" w lp
5.0 5.0
25.0 60.0
15.0 80.0
e
80.0 80.0
100.0 60.0
63.0 25.0
40.0 55.0
60.0 70.0
e
```

## プロットタイプ別

### グリッド

- `vis.plt`をダンプ、または、`vis.plt`を生成するスクリプトを用意
- `noborder`、`noxtics`、`noytics`して、各セルを長方形objectとして表示

### アニメーション

- 一応、pltファイル内でループを書くこともできるが、複数pltファイルを出力して、あとでまとめたほうがよいかも

```
# pltファイル内でループを書く場合の例
if(exist("n")==) n=0
filename = sprintf("out_%03d.png", n)

# plot ...

n=n+1
pause 0.1
if(n<50) reread
```

## ファイルを経由しないで直接実行

```c++
FILE* gp;
if ((gp = popen("gnuplot", "w")) == nullptr) {
    cerr << "error: pipe open error." << endl;
    return;
}
std::stringstream ss;
ss << "set terminal png size 512,512\n";
ss << "set output \"" << filename << "\"\n";
ss << "set size ratio 1\n";
ss << "set title \"" << title << "\"\n";
ss << "set grid\n";
ss << "set nokey\n";
ss << "unset xtics\n";
ss << "unset ytics\n";
ss << "set xrange [0:10000]\n";
ss << "set yrange [10000:0] reverse\n";
//...
ss << "\n";
ss << "plot \"-\" w p\n";
ss << "-1 -1\n";
ss << "e\n";

fprintf(gp, ss.str().c_str());
fflush(gp);
fprintf(gp, "exit\n");
pclose(gp);
```