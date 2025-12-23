# 機械学習

[TOC]

## AHCでの使い道

- パラメータの推定など
  - [AHC003](../ContestMemo/ahc003.md)
    - https://twitter.com/Komaki__/status/1398961656781185026
- ソルバーやルールの切り替え判断などにも使える可能性
  - https://www.terry-u16.net/entry/ahc008
- ヒューリスティックコンテストで機械学習しよう(nagissさん)
  - https://speakerdeck.com/nagiss/hiyurisuteitukukontesutodeji-jie-xue-xi-siyou

## 線形回帰

```python
from sklearn import linear_model
import numpy as np

clf = linear_model.LinearRegression()

x = np.array([[-1.2], [-0.6], [0.0], [0.6], [1.2],
             [1.8], [2.4], [3.0], [3.6], [4.2]])
y = np.cos(x)

clf.fit(x, y)

print(clf.coef_)
print(clf.intercept_)

for xv in x:
    yv = np.cos(xv)
    pyv = clf.predict([xv])
    print(f"{xv[0]}\t{yv[0]}\t{pyv[0][0]}")
```

## ロジスティック回帰

## 決定木系

- treelite
  - https://github.com/dmlc/treelite
  - https://twitter.com/phyllo/status/1175421755919228928

## ガウス過程回帰

- https://gist.github.com/jetbead/ac017f930bf7140494db777286eeb252
  - https://twitter.com/phyllo/status/1177248224270094336

## Neural Network

### 線形モデル(pytorch)

```python
import torch
import torch.optim as optim
import torch.nn as nn

# 線形モデル w * x + b
linear_model = nn.Linear(1, 1)

# 学習データ(入力、出力は正規化(-1〜1)されている方が良い)
t_c = [...] # 入力
t_u = [...] # 出力
t_c = torch.tensor(t_c).unsqueeze(1)
t_u = torch.tensor(t_u).unsqueeze(1)

# シャッフルかつ分割
n_samples = t_u.shape[0]
n_val = int(0.2 * n_samples)
shuffled_indices = torch.randperm(n_samples)
train_indices = shuffled_indices[:-n_val]
val_indices = shuffled_indices[-n_val:]
train_indices, val_indices

t_u_train = t_u[train_indices]
t_c_train = t_c[train_indices]
t_u_val = t_u[val_indices]
t_c_val = t_c[val_indices]

def training_loop(n_epochs, optimizer, model, loss_fn, t_u_train, t_u_val,
                  t_c_train, t_c_val):
    for epoch in range(1, n_epochs + 1):
        t_p_train = model(t_u_train)
        loss_train = loss_fn(t_p_train, t_c_train)

        t_p_val = model(t_u_val)
        loss_val = loss_fn(t_p_val, t_c_val)
        
        optimizer.zero_grad()
        loss_train.backward()
        optimizer.step()

        if epoch == 1 or epoch % 1000 == 0:
            print(f"Epoch {epoch}, Training loss {loss_train.item():.4f},"
                  f" Validation loss {loss_val.item():.4f}")

optimizer = optim.SGD(linear_model.parameters(), lr=1e-2)
training_loop(
    n_epochs = 3000, 
    optimizer = optimizer,
    model = linear_model,
    loss_fn = nn.MSELoss(),
    t_u_train = t_un_train,
    t_u_val = t_un_val, 
    t_c_train = t_c_train,
    t_c_val = t_c_val)

# 学習結果
linear_model.weight
linear_model.bias
```
