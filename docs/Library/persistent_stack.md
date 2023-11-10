# 永続stack

## 概要

- https://platinum-prog.hatenablog.com/entry/2023/07/28/222536
- https://noshi91.hatenablog.com/entry/2019/02/04/175100
- 永続stackは、更新前のstackの状態を保存しておけるデータ構造
- ビームサーチやゲーム木などで、出力文字列を各ノードに持たせると、stepが進むほど大きくなってしまうが、永続stackに置き換えることで効率化できる

## コード

```cpp
template <class T>
class PersistentStack {
   public:
    struct History {
        T v;
        shared_ptr<History> parent;
        History(const T& v, shared_ptr<History> parent) : v(v), parent(parent) {
        }
    };

    PersistentStack() : head(nullptr) {
    }
    PersistentStack(shared_ptr<History> head) : head(head) {
    }
    bool empty() const {
        return head == nullptr;
    }
    T top() {
        return head->v;
    }
    PersistentStack push(const T& v) {
        return PersistentStack(make_shared<History>(v, head));
    }
    PersistentStack pop() {
        return PersistentStack(head->parent);
    }

   private:
    shared_ptr<History> head;
};
```

## Usage

```cpp
// Stateクラスに`std::vector<int> output;`のように持たせているところを置き換える
struct State {
    PersistentStack<string> stack;
    // ...
};

// 遷移時にstackも更新
State next_state = state;
next_state.advance(op1);
next_state.stack = next_state.stack.push("op1");

// 最終状態から復元
vector<string> ans;
while(!state.stack.empty()){
    ans.push_back(state.stack.top());
    state.stack = state.stack.pop();
}
reverse(ans.begin(), ans.end());
```

## Verified

- なし
