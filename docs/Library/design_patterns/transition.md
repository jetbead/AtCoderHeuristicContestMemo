# 状態遷移

## 概要

- 有限オートマトンで表現されるような状態遷移を実装する場合、いくつか方法が考えられる
- 遷移が単純な場合は、`enum class State`などで状態を用意し、現在の状態を保持しながら更新していくとか
- 状態ごとに異なる複雑な挙動が必要になる場合は、状態をオブジェクトとして扱って、状態に挙動を持たせるようにするなど

## コードメモ

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
    virtual ~HumanState() {
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
