# ゲームと AI の分離

## 概要

- ゲーム(環境情報、スコア計算など)に関するものと、AI(方針、戦略など)に関するものを分ける
- AI 部分は、試行錯誤する部分なので、アルゴリズムを切り替えられるよう Strategy パターンなどにする
- 分けておくことで、いろんな戦略を切り替えやすくなる

## Reference

- https://eijirou-kyopro.hatenablog.com/entry/2022/02/27/003135

## コードメモ

```c++
#include <bits/stdc++.h>
using namespace std;

class Game {
   public:
    void update() {
        cerr << "Game update()" << endl;
    }
};

template <class Strategy>
class AI {
   public:
    explicit AI(Game& game) : game(game) {
        strategy.init(game);
    }
    void update() {
        strategy.update(game);
    }

   private:
    Game& game;
    Strategy strategy;
};

class StrategyX {
   public:
    void init(Game& game) {
        cerr << "StrategyX init()" << endl;
    }
    void update(Game& game) {
        cerr << "StrategyX update()" << endl;
    }
};

int main() {
    Game game;
    AI<StrategyX> ai(game);

    for (int turn = 1; turn <= 5; turn++) {
        ai.update();
        game.update();
    }

    return 0;
}
```