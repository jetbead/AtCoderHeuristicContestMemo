# プリム法ベースシュタイナー木

## 概要

- https://bowwowforeach.hatenablog.com/entry/2023/06/18/213212
- プリム法ベースのシュタイナー木
  - 厳密解ではなく、1つのある程度良い解を求めるアルゴリズム

## コード

```cpp
namespace PrimSteinerTree {

template <class T>
struct Edge {
    int src, dest;
    T cost;
    Edge() : src(-1), dest(-1), cost(-1) {
    }
    Edge(int src, int dest, T cost) : src(src), dest(dest), cost(cost) {
    }

    bool operator<(const Edge& others) const {
        return cost < others.cost;
    };
};

template <class T>
class PrimSteinerTree {
    static constexpr T INF = numeric_limits<T>::max();
    using Cand = pair<T, Edge<T>>;

    int N;
    vector<vector<Edge<T>>> G;

    vector<Edge<T>> get_nearest_node_path(const vector<int>& is_terminals, const vector<int>& used,
                                          const vector<int>& used_nodes) {
        vector<T> dist(N, INF);
        vector<Edge<T>> prv(N);

        priority_queue<Cand, vector<Cand>, greater<Cand>> que;
        for (int node : used_nodes) que.emplace(0, Edge(-1, node, 0));

        int target = -1;
        while (!que.empty()) {
            const auto cand = que.top();
            que.pop();

            if (dist[cand.second.dest] != INF) continue;
            dist[cand.second.dest] = cand.first;
            prv[cand.second.dest] = cand.second;
            if (used[cand.second.dest] == 0 && is_terminals[cand.second.dest] == 1) {
                target = cand.second.dest;
                break;
            }

            for (const Edge<T>& edge : G[cand.second.dest]) {
                if (dist[edge.dest] != INF) continue;
                que.emplace(cand.first + edge.cost, edge);
            }
        }
        assert(target >= 0);

        vector<Edge<T>> ret;
        while (true) {
            ret.push_back(prv[target]);
            target = prv[target].src;
            if (used[target] == 1) break;
        }

        return ret;
    }

   public:
    PrimSteinerTree(int N) : N(N), G(N) {
    }

    void add_edge(int s, int t, T cost) {
        G[s].emplace_back(s, t, cost);
        G[t].emplace_back(t, s, cost);
    }

    vector<vector<Edge<T>>> build(const vector<int>& terminals) {
        vector<vector<Edge<T>>> ret(N);

        // ターミナル頂点
        int num_remain_terminal = terminals.size();
        vector<int> is_terminals(N, 0);
        for (int t : terminals) is_terminals[t] = 1;

        // シュタイナー木の頂点集合管理
        vector<int> used(N, 0);
        vector<int> used_nodes;

        // ターミナル頂点の1つを木に入れる
        num_remain_terminal--;
        used_nodes.push_back(terminals[0]);
        used[terminals[0]] = 1;

        // 木から一番近いターミナル頂点までのパスを見つけるのを繰り返す
        while (num_remain_terminal > 0) {
            const auto path = get_nearest_node_path(is_terminals, used, used_nodes);
            for (const Edge<T>& edge : path) {
                if (used[edge.src] == 0) {
                    used[edge.src] = 1;
                    used_nodes.push_back(edge.src);
                }
                if (used[edge.dest] == 0) {
                    used[edge.dest] = 1;
                    used_nodes.push_back(edge.dest);
                }
                ret[edge.src].push_back(edge);
                ret[edge.dest].emplace_back(edge.dest, edge.src, edge.cost);
            }
            num_remain_terminal--;
        }
        return ret;
    }
};

};  // namespace PrimSteinerTree
```

## テストコード

- bowwowforeachさんのブログの例

```cpp
void test() {
    PrimSteinerTree::PrimSteinerTree<int> pst(6);

    pst.add_edge(0, 4, 5);
    pst.add_edge(0, 5, 5);
    pst.add_edge(1, 3, 6);
    pst.add_edge(1, 5, 5);
    pst.add_edge(2, 3, 5);
    pst.add_edge(2, 4, 7);
    pst.add_edge(3, 4, 3);
    pst.add_edge(3, 5, 5);
    pst.add_edge(4, 5, 3);

    const auto tree = pst.build({0, 1, 2});
    rep(i, 6) {
        for (const auto& edge : tree[i]) {
            cout << edge.src << " " << edge.dest << " " << edge.cost << endl;
        }
    }
}
```

## Verified

- なし
