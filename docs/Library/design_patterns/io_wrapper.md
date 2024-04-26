# IOラッパー

## 概要

- 自前でローカルテスターを移植したりする場合など、ローカルテスター用の入出力と提出時の入出力が異なったりする
- 入出力部分をラップし、ローカルの時と提出時の違いを吸収する
- また、ローカルテスター部分は、何個も解を作って一番良いのを返す、ような使い方もできる

## Reference

- https://iwashi31.hatenablog.com/entry/2023/11/05/234449

## コードメモ

```c++
// ゲーム情報
struct Game {
    // ...

    void init() {
    }
};

// ローカルテスター
struct LocalTester {
    // ...

    void init(Game& game) {
        // ...
    }

    int get_score() {
        return 0;
    }
};

struct IO {
    virtual Game init() = 0;
    virtual void op1() = 0;
    virtual int op2(int x) = 0;
    virtual int answer(int ans) = 0;
};

class JudgeIO : public IO {
    Game init() override {
        cerr << "JudgeIO" << endl;
        Game game;
        game.init();
        return game;
    }
    void op1() override {
        // ...
    }
    int op2(int x) override {
        // ...
        return 0;
    }
    int answer(int ans) override {
        // ...
        return 123;
    }
};

class LocalIO : public IO {
    LocalTester local_tester;

    Game init() override {
        cerr << "LocalIO" << endl;
        Game game;
        game.init();
        local_tester.init(game);
        return game;
    }
    void op1() override {
        // ...
    }
    int op2(int x) override {
        // ...
        return 0;
    }
    int answer(int ans) override {
        return local_tester.get_score();
    }
};

int main() {
#ifdef LOCAL
    shared_ptr<IO> io = make_shared<LocalIO>();
#else
    shared_ptr<IO> io = make_shared<JudgeIO>();
#endif
    Game game = io->init();

    return 0;
}
```
