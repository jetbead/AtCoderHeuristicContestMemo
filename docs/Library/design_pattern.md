# デザインパターン

(コードメモは適当に書いたやつなので注意)

## ゲームと AI の分離

### 概要

- ゲーム(環境情報、スコア計算など)に関するものと、AI(方針、戦略など)に関するものを分ける
- AI 部分は、試行錯誤する部分なので、アルゴリズムを切り替えられるよう Strategy パターンなどにする
- 分けておくことで、いろんな戦略を切り替えやすくなる

### Reference

- https://eijirou-kyopro.hatenablog.com/entry/2022/02/27/003135

### コードメモ

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

## 異なるタスクを実行しうるマルチエージェントの協調動作

### 概要

- 複数のエージェントが、それぞれ異なるタスクを実行したり、部分的に協調的にタスクを実行する場合、「命令(Command)」単位で扱うとわかりやすい
  - AI がいくつか命令を発行し、命令にエージェントをアサインする
  - 命令ごとに目標を達成するためにエージェントを動かす

### Reference

- https://iwashi31.hatenablog.com/entry/2022/02/28/194503

### コードメモ

```c++
#include <bits/stdc++.h>
using namespace std;

struct Human {
    int x;
};

struct Game {
    Game(int num) : humans(num) {
    }

    vector<Human> humans;
};

struct Command {
    virtual void execute(Game& game) = 0;
    void assign(int index) {
        assigned.emplace_back(index);
    }

    bool is_completed = false;  // 命令の状態
    vector<int> assigned;       // アサインされた人のindex
};

struct GotoTargetCommand : public Command {
    GotoTargetCommand(int tx) : tx(tx) {
    }

    void execute(Game& game) override {
        is_completed = true;
        for (int index : assigned) {
            Human& human = game.humans[index];
            if (tx < human.x)
                human.x--;
            else if (tx > human.x)
                human.x++;

            if (human.x != tx) is_completed = false;
        }
    }

    int tx;  // 目標地点
};

struct GotoOriginCommand : public Command {
    void execute(Game& game) override {
        is_completed = true;
        for (int index : assigned) {
            Human& human = game.humans[index];
            if (0 < human.x)
                human.x--;
            else if (0 > human.x)
                human.x++;

            if (human.x != 0) is_completed = false;
        }
    }
};

int main() {
    // 3人を適当に配置
    Game game(3);
    game.humans[0].x = 3;
    game.humans[1].x = 4;
    game.humans[2].x = 10;

    vector<shared_ptr<Command>> commands;

    // x=5に向かう命令(indexが0と2の人をアサイン)
    commands.emplace_back(make_shared<GotoTargetCommand>(5));
    commands.back()->assign(0);
    commands.back()->assign(2);

    // 原点に向かう命令(indexが1の人をアサイン)
    commands.emplace_back(make_shared<GotoOriginCommand>());
    commands.back()->assign(1);

    for (int turn = 1; turn <= 10; turn++) {
        for (auto cmd : commands)
            if (!cmd->is_completed) cmd->execute(game);

        cerr << "turn " << turn << ":";
        for (int i = 0; i < game.humans.size(); i++) {
            cerr << " x=" << game.humans[i].x;
        }
        cerr << endl;
    }

    return 0;
}
```

## 状態遷移

### 概要

- 有限オートマトンで表現されるような状態遷移を実装する場合、いくつか方法が考えられる
- 遷移が単純な場合は、`enum class State`などで状態を用意し、現在の状態を保持しながら更新していくとか
- 状態ごとに異なる複雑な挙動が必要になる場合は、状態をオブジェクトとして扱って、状態に挙動を持たせるようにするなど

### コードメモ

```c++
#include <bits/stdc++.h>
using namespace std;

struct Human {
    enum class State { STOP, WALK, JUMP };

    Human() {
        change_state(State::STOP);
    }

    // 状態遷移処理
    void change_state(State next_state) {
        // exit処理
        // ...

        current_state = next_state;

        // enter処理
        if (next_state == State::STOP)
            step = 2;
        else if (next_state == State::WALK)
            step = 3;
        else if (next_state == State::JUMP)
            step = 1;

    }
    // 状態の更新
    void update() {
        if (current_state == State::STOP)
            update_stop();
        else if (current_state == State::WALK)
            update_walk();
        else if (current_state == State::JUMP)
            update_jump();
    }
    void update_stop() {
        cerr << "stop" << endl;
        step--;
        if (step == 0) change_state(State::WALK);
    }
    void update_walk() {
        cerr << "walk" << endl;
        step--;
        if (step == 0) change_state(State::JUMP);
    }
    void update_jump() {
        cerr << "Jump!" << endl;
        step--;
        if (step == 0) change_state(State::STOP);
    }

    State current_state;
    int step;
};

int main() {
    Human human;

    for (int i = 0; i < 20; i++) {
        human.update();
    }
    return 0;
}
```

```c++
#include <bits/stdc++.h>
using namespace std;

struct Human;

struct HumanState {
    HumanState(shared_ptr<Human> owner) : owner(owner) {
    }
    virtual void update() = 0;
    virtual void on_enter() = 0;
    virtual void on_exit() = 0;

    shared_ptr<Human> owner;
};

struct Human {
    void register_state(const string& name, shared_ptr<HumanState> state) {
        state_map[name] = state;
    }

    void set_initial_state(const string& name) {
        assert(state_map.count(name) != 0);
        current_state = state_map[name];
        current_state->on_enter();
    }

    void change_state(const string& name) {
        assert(state_map.count(name) != 0);
        current_state->on_exit();
        current_state = state_map[name];
        current_state->on_enter();
    }

    void update() {
        current_state->update();
    }

    unordered_map<string, shared_ptr<HumanState>> state_map;
    shared_ptr<HumanState> current_state;
};

struct StopState : public HumanState {
    StopState(shared_ptr<Human> owner) : HumanState(owner) {
    }
    void update() override {
        cerr << "stop" << endl;
        step--;
        if (step == 0) owner->change_state("walk");
    }
    void on_enter() override {
        step = 2;
    }
    void on_exit() override {
    }

    int step;
};

struct WalkState : public HumanState {
    WalkState(shared_ptr<Human> owner) : HumanState(owner) {
    }
    void update() override {
        cerr << "walk" << endl;
        step--;
        if (step == 0) owner->change_state("jump");
    }
    void on_enter() override {
        step = 3;
    }
    void on_exit() override {
    }

    int step;
};

struct JumpState : public HumanState {
    JumpState(shared_ptr<Human> owner) : HumanState(owner) {
    }
    void update() override {
        cerr << "Jump!" << endl;
        step--;
        if (step == 0) owner->change_state("stop");
    }
    void on_enter() override {
        step = 1;
    }
    void on_exit() override {
    }

    int step;
};

int main() {
    shared_ptr<Human> human = make_shared<Human>();
    human->register_state("stop", make_shared<StopState>(human));
    human->register_state("walk", make_shared<WalkState>(human));
    human->register_state("jump", make_shared<JumpState>(human));
    human->set_initial_state("stop");

    for (int i = 0; i < 20; i++) {
        human->update();
    }
    return 0;
}
```
