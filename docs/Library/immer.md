# immer

immutableな永続データ構造のライブラリ。AtCoder環境で使える。

Heuristic Contest的には、ビームサーチなどでの出力保存や差分操作などで活用できる(かも)。

[TOC]

## vector

```cpp
#include <immer/vector.hpp>
const auto v0 = immer::vector<int>{};
const auto v1 = v0.push_back(13);
cout << v0.size() << endl;
cout << v1.size() << endl;
cout << v1[0] << endl;

const auto v2 = v1.set(0, 42);
cout << v1.size() << endl;
cout << v1[0] << endl;
cout << v2.size() << endl;
cout << v2[0] << endl;
```

## map

```cpp
#include <immer/map.hpp>
const auto v0 = immer::map<string, int>{};
const auto v1 = v0.set("hello", 42);
cout << v0["hello"] << endl;
cout << v1["hello"] << endl;
```

## Links

- https://github.com/arximboldi/immer
- https://sinusoid.es/immer/#contents
