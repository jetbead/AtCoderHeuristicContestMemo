# pythonスニペット

## 引数処理

```python
import argparse

# コマンド引数で読み込み
parser = argparse.ArgumentParser(description="")
parser.add_argument("arg", help="")
parser.add_argument("--arg1", required=True, help="")
parser.add_argument("--arg2", type=int, default=0, help="")
parser.add_argument("--arg3", default="", help="")
parser.add_argument("--flag", action='store_true')
args = parser.parse_args()
# args = vars(parser.parse_args()) # dict形式に変換

# jsonファイルを読み込み
import json
args = json.load(open('settings.json'))

# json形式で引数を保存
import json
with open("settings.json", "w") as f:
    json.dump(args.__dict__, f, indent=4)

```

## tqdm

- https://tqdm.github.io/

```python
# iTerm2でtqdmのバーがずれるときは、Preferences > Profiles > Textから
# Ambiguous characters are double-widthのチェックを外す
from tqdm import tqdm

for i in tqdm(range(10000)):
    ...
```

## logger

- https://docs.python.org/ja/3/howto/logging.html
- https://docs.python.org/ja/3/howto/logging-cookbook.html

```python
# (使い方が雑なので注意)
# main
import logging
logging.basicConfig(
    format='[%(asctime)s %(levelname)s %(name)s:%(lineno)d] %(message)s',
    encoding='utf-8', level=logging.DEBUG)
logger = logging.getLogger(__name__)
logger.info("info message")

# module
from logging import getLogger
logger = getLogger(__name__)
```

## pathlib

- https://www.kaggle.com/code/ttahara/elementary-usage-example-of-pathlib-path

## joblib(並列処理)

```python
from joblib import Parallel, delayed

def func(i):
    return i

result = joblib.Parallel(n_jobs=-1)(joblib.delayed(func)(i) for i in range(10))
```