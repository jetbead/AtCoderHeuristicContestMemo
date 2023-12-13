# 実行結果の分析

- 入力の制約ごとのスコア分布や実行時間などをプロットすることで、プログラムの改善点を見つけられる可能性がある
  - 制約などで戦略を変えたり、など
- pythonのpandas/matplotlib等での可視化についてまとめておく
  - 本当は、ツール化しておけるのがよいのだけれど、、、

## 前提

入力のパラメータとそのときの実行結果のスコアをtsvファイルなどにしておく。(以下はAHC013のもの)

```bash
$ head score.tsv
K	N	score
3	33	7834
4	29	6275
5	39	7172
2	29	6754
3	33	7810
4	33	7313
5	24	3084
2	24	7229
3	18	5029
```

notebookで作業する。

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
plt.rcParams["figure.figsize"] = (10,5)

df = pd.read_csv('score.tsv', sep='\t')
df.head()
```

## パラメータごとの散布図

```python
for K in [2,3,4,5]:
    tmp = df[df['K'] == K]
    plt.scatter(tmp['N'], tmp['score'], alpha=0.5)
plt.show()
```

## N,Kごとのscoreの平均値

```python
# Nが細かすぎるので5刻みにする
df['GN'] = df['N'].apply(lambda x: f"{int(x/5)*5}-{(int(x/5)+1)*5-1}")

# N, Kごとのscoreの平均値
pd.options.display.precision = 2
pd.pivot_table(df, index='GN', columns='K', values='score', fill_value='-')
```

## ヒートマップ表示

```python
sns.heatmap(pd.pivot_table(df, index='N', columns='K', values='score', fill_value=0))
```


## Kごとについて、ヒストグラムを並べて表示

```python
df.groupby('K').plot(kind='hist', by=['K', 'GN'], column='score', layout=(4,6), sharex=True, sharey=True, subplots=True, legend=None)
plt.show()
```

## その他matplotlibによる可視化

- https://twitter.com/rosso016/status/1734957163343397338
  - https://matplotlib.org/stable/plot_types/index.html
  - https://qiita.com/yubais/items/c95ba9ff1b23dd33fde2
