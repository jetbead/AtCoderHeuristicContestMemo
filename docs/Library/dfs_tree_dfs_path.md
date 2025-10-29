# ランダムDFS木/DFSパス

## 概要

- グラフやグリッドなどで、なんらかの木構造を作りたいとき、サイクルができないようにランダムにDFSで訪問すると、ランダムなパスや木構造が得られる
  - [AHC002](../ContestMemo/ahc002.md)
  - [AHC027](../ContestMemo/ahc027.md)
  - [AHC054](../ContestMemo/ahc054.md)

## コード(ランダムDFS木生成)

- グリッド上での迷路生成
- dfsで「1つ前に行った方向、連続で選んでいる回数」なども引数で渡すと、条件を満たすDFS木を生成できる

```cpp
constexpr static int vy[4] = {-1, 0, 1, 0};
constexpr static int vx[4] = {0, -1, 0, 1};

bool in_range(int N, const P& p) {
    return 0 <= p.y && p.y < N && 0 <= p.x && p.x < N;
}

void dfs(int N, vector<vector<int>>& v, const P& p) {
    v[p.y][p.x] = 1;
    vector<int> order{0, 1, 2, 3};
    xor128_shuffle(order.begin(), order.end());
    for (int k : order) {
        P nxt(p.y + vy[k], p.x + vx[k]);
        if (!in_range(N, nxt)) continue;
        if (v[nxt.y][nxt.x] == 1) continue;

        // サイクルができるかチェック
        bool ok = true;
        rep(kk, 4) {
            P adj(nxt.y + vy[kk], nxt.x + vx[kk]);
            if (!in_range(N, adj)) continue;
            if (adj == p) continue;
            if (v[adj.y][adj.x] == 1) ok = false;
        }
        if (ok) dfs(N, v, nxt);
    }
}

vector<vector<int>> make_random_dfs_tree(int N, const P& start) {
    vector<vector<int>> v(N, vector<int>(N, 0));
    dfs(N, v, start);
    return v;
}

int main() {
    int N = 10;
    auto v = make_random_dfs_tree(N, P(N/2, 0));
    rep(y, N) {
        rep(x, N) {
            cout << (v[y][x] == 1 ? "*" : " ");
        }
        cout << endl;
    }
    return 0;
}
```

## コード(ランダムDFSパス)

- グリッド上で、2頂点をつなぐランダムなパスを生成
- パスの部分破壊再構築近傍などで利用

```cpp
constexpr static int vy[4] = {-1, 0, 1, 0};
constexpr static int vx[4] = {0, -1, 0, 1};

bool in_range(int N, const P& p) {
    return 0 <= p.y && p.y < N && 0 <= p.x && p.x < N;
}

int dfs_max_len;     // パスの長さ制限
int dfs_count;       // dfsの呼び出し回数制限(見つからない場合は失敗扱いにする)
vector<P> dfs_path;  // 見つけたパス

bool dfs(int N, vector<vector<int>>& v, const P& p, const P& goal) {
    if (dfs_path.size() > dfs_max_len) return false;
    if (dfs_count == 0) return false;
    dfs_count--;

    if (p == goal) {
        return true;
    }

    v[p.y][p.x] = 1;
    vector<int> order{0, 1, 2, 3};
    xor128_shuffle(order.begin(), order.end());
    for (int k : order) {
        P nxt(p.y + vy[k], p.x + vx[k]);
        if (!in_range(N, nxt)) continue;
        if (v[nxt.y][nxt.x] == 1) continue;
        dfs_path.emplace_back(nxt);
        if (dfs(N, v, nxt, goal)) return true;
        if (dfs_count == 0) return false;
        dfs_path.pop_back();
    }
    v[p.y][p.x] = 0;
    return false;
}

bool make_random_dfs_path(int N, int max_len, const P& start, const P& goal) {
    vector<vector<int>> v(N, vector<int>(N, 0));
    dfs_max_len = max_len;
    dfs_count = 1000;
    dfs_path.clear();
    dfs_path.emplace_back(start);
    return dfs(N, v, start, goal);
}

int main() {
    rep(i, 10) {  // 10回ランダムにパスを生成
        // NxNのグリッドの左真ん中から右真ん中に向かってパスを生成
        const int N = 5;
        make_random_dfs_path(N, 10, P(N / 2, 0), P(N / 2, N - 1));
        vector<vector<int>> v(N, vector<int>(N, -1));
        rep(j, dfs_path.size()) v[dfs_path[j].y][dfs_path[j].x] = j;

        cout << "--------" << endl;
        rep(y, N) {
            rep(x, N) {
                if (v[y][x] == -1) {
                    cout << ".";
                } else {
                    cout << v[y][x];
                }
            }
            cout << endl;
        }
    }
    return 0;
}
```

## Verified

- なし
