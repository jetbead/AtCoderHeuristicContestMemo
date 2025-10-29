# 最短経路木

## 概要

- 無向グラフで、root頂点から各頂点への移動が最短になる木＝最短経路木の構築と、差分更新をするクラス
  - 有向グラフならちょっと修正が必要
- 最初にadd_edge()したあと、build()で構築し、updateありでadd_edge/remove_edgeする
  - 次数が少なめが前提かも、、、

## 注意

- add/removeする辺は、重複がない・元のグラフに存在すること
- 橋の辺も削除できるが、その場合は、到達できない頂点はINFになる
- グラフの辺の削除の部分は線形探索しているので、高速化が必要ならIndexSetに置き換える
- 差分更新の部分はまだ高速化できそうだが、バグりそうなので、とりあえず、最低限だけ実装してある

## コード

```cpp
template <class T>
class ShortestPathTree {
    static constexpr T INF = numeric_limits<T>::max();
    struct Edge {
        int src, dest;
        T cost;
        int idx;
        Edge() : src(-1), dest(-1), cost(-1), idx(-1) {
        }
        Edge(int src, int dest, T cost, int idx = -1) : src(src), dest(dest), cost(cost), idx(idx) {
        }

        bool operator>(const Edge& others) const {
            return cost > others.cost;
        };
    };

    struct Graph {
        int N;
        vector<vector<Edge>> G;

        Graph(int N) : N(N), G(N) {
        }

        void add_edge(int src, int dest, T cost) {
            assert(cost > 0);
            G[src].emplace_back(src, dest, cost);
        }

        void remove_edge(int src, int dest) {
            if (G[src].size() == 0) return;
            for (int i = 0; i < G[src].size(); i++) {
                if (G[src][i].dest == dest) {
                    swap(G[src][i], G[src][G[src].size() - 1]);
                    break;
                }
            }
            if (G[src].back().dest == dest) G[src].pop_back();
        }

        void add_undirected_edge(int src, int dest, T cost) {
            add_edge(src, dest, cost);
            add_edge(dest, src, cost);
        }

        void remove_undirected_edge(int src, int dest) {
            remove_edge(src, dest);
            remove_edge(dest, src);
        }
    };

    struct ShortestPath {
        vector<T> dist;
        vector<int> parent;
        ShortestPath() {
        }
        void reset(int N) {
            dist.assign(N, INF);
            parent.assign(N, -1);
        }
    };

    enum class UpdateType { ADD, REMOVE };

    Graph graph;
    vector<int> starts;
    vector<ShortestPath> shortest_paths;

    vector<pair<UpdateType, pair<int, int>>> ops;

    void make_shortest_path_tree(int root) {
        ShortestPath& sp = shortest_paths[root];
        sp.reset(graph.N);

        priority_queue<Edge, vector<Edge>, greater<Edge>> que;
        que.emplace(-1, root, 0);
        while (!que.empty()) {
            const auto e = que.top();
            que.pop();

            if (sp.dist[e.dest] <= e.cost) continue;
            shortest_paths[root].dist[e.dest] = e.cost;
            shortest_paths[root].parent[e.dest] = e.src;

            for (const auto& ne : graph.G[e.dest]) {
                if (sp.dist[ne.dest] <= e.cost + ne.cost) continue;
                que.emplace(e.dest, ne.dest, e.cost + ne.cost);
            }
        }
    }

    void update_add_shortest_path(int x, int y, T cost) {
        for (int root : starts) {
            ShortestPath& sp = shortest_paths[root];
            if (sp.dist[x] == sp.dist[y]) continue;

            // 距離が遠かった方をstart, 近かった方をprv
            int start = (sp.dist[x] > sp.dist[y]) ? x : y;
            int prv = (start == y) ? x : y;

            // 短くなるなら全部更新、そうでないなら更新不要
            if (sp.dist[prv] + cost >= sp.dist[start]) continue;

            // startからダイクストラで最短路木を再更新(部分木以外も更新されうる)
            priority_queue<Edge, vector<Edge>, greater<Edge>> que;
            que.emplace(prv, start, sp.dist[prv] + cost);
            while (!que.empty()) {
                const auto e = que.top();
                que.pop();

                if (sp.dist[e.dest] <= e.cost) continue;
                shortest_paths[root].dist[e.dest] = e.cost;
                shortest_paths[root].parent[e.dest] = e.src;

                for (const auto& ne : graph.G[e.dest]) {
                    if (sp.dist[ne.dest] <= e.cost + ne.cost) continue;
                    que.emplace(e.dest, ne.dest, e.cost + ne.cost);
                }
            }
        }
    }

    void update_remove_shortest_path(int x, int y) {
        for (int root : starts) {
            ShortestPath& sp = shortest_paths[root];
            // 距離が遠かった方から先の部分木を更新
            int start = (sp.dist[x] > sp.dist[y]) ? x : y;

            // 悪化するなら全部更新、そうでないなら更新不要
            T best = INF;
            for (const Edge& e : graph.G[start]) {
                if (sp.parent[e.dest] == start) continue;
                best = min(best, sp.dist[e.dest] + e.cost);
            }
            if (best == sp.dist[start]) continue;

            // start以下の部分木をdist[i]=INFとして初期化
            priority_queue<Edge, vector<Edge>, greater<Edge>> que;
            queue<int> vque;  // 部分木探索用
            vque.push(start);
            while (!vque.empty()) {
                int node = vque.front();
                vque.pop();

                for (const Edge& e : graph.G[node]) {
                    if (sp.parent[e.dest] == node) {
                        vque.push(e.dest);
                    }
                }
                sp.dist[node] = INF;
            }
            // INFの頂点の周囲から最短路木を再更新
            vque.push(start);
            while (!vque.empty()) {
                int node = vque.front();
                vque.pop();

                for (const Edge& e : graph.G[node]) {
                    if (sp.parent[e.dest] == node) {
                        vque.push(e.dest);
                    } else {
                        if (sp.dist[e.dest] != INF) {
                            que.emplace(e.dest, node, e.cost + sp.dist[e.dest]);
                        }
                    }
                }
            }
            while (!que.empty()) {
                const auto e = que.top();
                que.pop();

                if (sp.dist[e.dest] <= e.cost) continue;
                shortest_paths[root].dist[e.dest] = e.cost;
                shortest_paths[root].parent[e.dest] = e.src;

                for (const auto& ne : graph.G[e.dest]) {
                    if (sp.dist[ne.dest] <= e.cost + ne.cost) continue;
                    que.emplace(e.dest, ne.dest, e.cost + ne.cost);
                }
            }
        }
    }

   public:
    ShortestPathTree(int N) : graph(N), shortest_paths(N) {
    }

    void add_edge(int src, int dest, T cost, bool update = false) {
        graph.add_undirected_edge(src, dest, cost);
        if (update) update_add_shortest_path(src, dest, cost);
    }

    void remove_edge(int src, int dest, bool update = false) {
        graph.remove_undirected_edge(src, dest);
        if (update) update_remove_shortest_path(src, dest);
    }

    void build() {
        vector<int> v(graph.N);
        iota(v.begin(), v.end(), 0);
        build(v);
    }

    void build(const vector<int>& v_start) {
        starts = v_start;
        for (int start : starts) make_shortest_path_tree(start);
    }

    const vector<T> get(int root) const {
        return shortest_paths[root].dist;
    }
};
```


## テストコード

- 小さめのグラフで、実際に最短経路を全部作り直ししたものと比較して差分がないことを確認

```cpp
void check(const ShortestPathTree<int>& tree, const vector<vector<int>>& G) {
    const int INF = numeric_limits<int>::max();
    rep(start, G.size()) {
        using ST = pair<int, int>;
        vector<int> dist(G.size(), INF);
        priority_queue<ST, vector<ST>, greater<ST>> que;
        que.emplace(0, start);
        while (!que.empty()) {
            const auto st = que.top();
            que.pop();

            if (dist[st.second] <= st.first) continue;
            dist[st.second] = st.first;

            rep(i, G[st.second].size()) {
                if (G[st.second][i] == 0) continue;
                if (dist[i] <= st.first + 1) continue;
                que.emplace(st.first + 1, i);
            }
        }

        const auto actual = tree.get(start);
        rep(i, actual.size()) {
            if (actual[i] != dist[i]) {
                cerr << start << endl;
                rep(j, actual.size()) {
                    cerr << " " << actual[j];
                }
                cerr << endl;
                rep(j, dist.size()) {
                    cerr << " " << dist[j];
                }
                cerr << endl;
            }
            assert(actual[i] == dist[i]);
        }
    }
}

void test() {
    int N = 50;
    vector<vector<int>> G(N, vector<int>(N, 0));
    rep(i, 10000) {
        int a = xor128() % N;
        int b = xor128() % N;
        while (a == b) b = xor128() % N;

        if (G[a][b] == 0) {
            G[a][b] = 1;
            G[b][a] = 1;
        } else {
            G[a][b] = 0;
            G[b][a] = 0;
        }
    }
    ShortestPathTree<int> tree(N);
    rep(i, N) {
        REP(j, i + 1, N) {
            if (G[i][j] == 1) {
                tree.add_edge(i, j, 1);
            }
        }
    }
    tree.build();
    check(tree, G);

    rep(i, 10000) {
        int a = xor128() % N;
        int b = xor128() % N;
        while (a == b) b = xor128() % N;

        if (G[a][b] == 0) {  // add_edge
            G[a][b] = 1;
            G[b][a] = 1;

            tree.add_edge(a, b, 1, true);
            cerr << i << " add_edge " << a << " " << b << endl;

            check(tree, G);

        } else {  // remove_edge
            G[a][b] = 0;
            G[b][a] = 0;

            tree.remove_edge(a, b, true);
            cerr << i << " remove_edge " << a << " " << b << endl;

            check(tree, G);
        }
    }
}
```

## Verified

- なし
