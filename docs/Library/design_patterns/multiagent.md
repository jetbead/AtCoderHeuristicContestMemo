# 異なるタスクを実行しうるマルチエージェントの協調動作

## 概要

- 複数のエージェントが、それぞれ異なるタスクを実行したり、部分的に協調的にタスクを実行する場合、「命令(Command)」単位で扱うとわかりやすい
  - AI がいくつか命令を発行し、命令にエージェントをアサインする
  - 命令ごとに目標を達成するためにエージェントを動かす

## Reference

- https://iwashi31.hatenablog.com/entry/2022/02/28/194503

## コードメモ

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
    virtual ~Command() {
    }
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
