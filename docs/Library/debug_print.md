# デバッグ出力

## 概要

- デバッグしやすいよう、コンテナや構造体のデバッグ出力をまとめて`debug(x)`や`debugs(x, y)`でできるようにしたもの

## コード
ソースコードの上の方に貼り付けて使う。

```c++
#ifdef LOCAL
#define ESC_BLACK "\033[30m"
#define ESC_RED "\033[31m"
#define ESC_GREEN "\033[32m"
#define ESC_YELLOW "\033[33m"
#define ESC_BLUE "\033[34m"
#define ESC_MAGENTA "\033[35m"
#define ESC_CYAN "\033[36m"
#define ESC_WHITE "\033[37m"
#define ESC_DEFAULT "\033[39m"
#define ESC_BG_BLACK "\033[40m"
#define ESC_BG_RED "\033[41m"
#define ESC_BG_GREEN "\033[42m"
#define ESC_BG_YELLOW "\033[43m"
#define ESC_BG_BLUE "\033[44m"
#define ESC_BG_MAGENTA "\033[45m"
#define ESC_BG_CYAN "\033[46m"
#define ESC_BG_WHITE "\033[47m"
#define ESC_BG_DEFAULT "\033[49m"
#define ESC_FT_UNDERBAR "\033[4m"
#define ESC_FT_BOLD "\033[1m"
#define ESC_FT_REVERSE "\033[7m"
#define ESC_FT_CLEAR "\033[0m"
// 画面のクリア
void ESC_CLEAR() {
    cerr << "\033[2J" << flush;
}
// (y行目,x列目)にカーソルを移動
void ESC_MOVE(int y, int x) {
    cerr << "\033[" << y << ";" << x << "H" << flush;
}

// 複数引数用
#define debugs(...)                 \
    cerr << "L" << __LINE__ << " "; \
    cerr << #__VA_ARGS__ << ": ";   \
    debugs_local(__VA_ARGS__);
void debugs_local() {
    cerr << endl;
}
template <class Head, class... Tail>
void debugs_local(Head&& head, Tail&&... tail) {
    cerr << head;
    if (sizeof...(tail) > 0) cerr << ", ";
    debugs_local(move(tail)...);
}
/*
template <class... Args>
void debugs_local(const Args&... args) {
    bool is_first = true;
    for (auto const& e : {args...}) {
        if (is_first)
            cerr << " ";
        else
            cerr << ", ";
        is_first = false;
        cerr << e;
    }
}
*/

// 変数名あり
//#define debug(x) cerr << #x << ": " << (x) << endl;
// 行数あり
#define debug(x) cerr << "L" << __LINE__ << " " << #x << ": " << (x) << endl;
// 色あり
//#define debug(x) cerr << ESC_YELLOW << "L" << __LINE__ << " " << #x << ": " << (x) << ESC_DEFAULT
//<< endl;
// ファイル出力
// ofstream logfs("log.txt");
//#define debug(x) logfs << "L" << __LINE__ << " " << #x << ": " << (x) << endl;

// コンテナ出力
template <class T>
ostream& operator<<(ostream& os, const vector<T>& v) {
    os << "[";
    for (const auto& x : v) os << " " << x;
    os << " ]";
    return os;
}
template <class T>
ostream& operator<<(ostream& os, const vector<vector<T>>& v) {
    // os << "[";
    // for(const auto& e: v) os << " " << e;
    // os << " ]";
    for (size_t i = 0; i < v.size(); i++) {
        os << endl;
        for (size_t j = 0; j < v[i].size(); j++) {
            // os << v[i][j];
            os << setw(3) << v[i][j];
        }
    }
    return os;
}
template <class T>
ostream& operator<<(ostream& os, const set<T>& v) {
    os << "[";
    for (const auto& x : v) os << " " << x;
    os << " ]";
    return os;
}
template <class T, size_t N>
ostream& operator<<(ostream& os, const array<T, N>& v) {
    os << "[";
    for (const auto& x : v) os << " " << x;
    os << " ]";
    return os;
}
template <class T, class U>
ostream& operator<<(ostream& os, const map<T, U>& m) {
    os << "{";
    for (const auto& x : m) os << " " << x.first << ":" << x.second;
    os << " }";
    return os;
}

#else
#define debug(x)
#define debugs(...)
#endif

// 構造体などを追加する場合
struct P {
    int y, x;
    P() : y(0), x(0) {
    }
    P(int y, int x) : y(y), x(x) {
    }
};
ostream& operator<<(ostream& os, const P& p) {
    os << "(" << p.y << "," << p.x << ")";
    return os;
}
```

## Usage

```c++
#include <bits/stdc++.h>
using namespace std;

// 上記のコード
// ...

int main() {
    vector<vector<int>> v(5, vector<int>(5, 0));
    debug(v);
  
    vector<P> ps;
    ps.emplace_back(1, 2);
    ps.emplace_back(3, 4);
    debug(ps);

    int a = 1, b = 5;
    debugs(a, b);

    return 0;
}
```

```
# LOCALをdefineしている場合のみ出力
$ g++ solver.cc -DLOCAL
$ ./a.out
L150 v:
  0  0  0  0  0
  0  0  0  0  0
  0  0  0  0  0
  0  0  0  0  0
  0  0  0  0  0
L155 ps: [ (1,2) (3,4) ]
L158 a, b: 1, 5

# LOCALがなければ出力なし(本番環境用)
$ g++ solver.cc
$ ./a.out
```

## Verified

- なし
  - バグがあるかもしれないので、見つけたら適時修正します
