# boost

AtCoder環境ではboostの新し目のバージョンが利用できる。

## Links

- Boost逆引きリファレンス
  - https://boostjp.github.io/tips.html

## メモ

- brewでインストールしている場合は、`-I/usr/local/Cellar/boost/<version>/include/`を追加

## 多倍長計算

```cpp
#include <boost/multiprecision/cpp_int.hpp>
namespace mp = boost::multiprecision;
using ll = mp::cpp_int;
```

## HashTable

### unordered_flat_map

- flat(メモリ連続な配列)でのunordered_map
- OpenAddressing方式で、SIMD計算で並列に検査していて、かなり高速そう
  - https://www.boost.org/doc/libs/1_82_0/libs/unordered/doc/html/unordered.html
  - https://bannalia.blogspot.com/2022/11/inside-boostunorderedflatmap.html

```cpp
#include <boost/unordered/unordered_flat_map.hpp>
boost::unordered_flat_map<uint64_t, uint64_t> m;
```

## 計算幾何

- https://boostjp.github.io/tips/geometry.html
