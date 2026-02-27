# ビットボード

## 概要

- 2次元グリッドの各マスを1bitで表現
- 省メモリや、bit演算で処理することによる高速化が期待できる

## コード

```cpp
template <int H, int W>
struct BitBoard {
   public:
    static_assert(H > 0 && W > 0);
    static constexpr int height = H;
    static constexpr int width = W;
    static constexpr int size = H * W;

    using Bits = bitset<size>;

   private:
    Bits board_{};

    static constexpr int idx(int y, int x) {
        return y * W + x;
    }

    // row mask
    static const Bits& row_mask(int y) {
        static const auto masks = [] {
            std::array<Bits, H> m{};
            for (int yy = 0; yy < H; ++yy) {
                Bits r{};
                for (int x = 0; x < W; ++x) r.set(idx(yy, x));
                m[yy] = r;
            }
            return m;
        }();
        return masks[y];
    }
    // col mask
    static const Bits& col_mask(int x) {
        static const auto masks = [] {
            std::array<Bits, W> m{};
            for (int xx = 0; xx < W; ++xx) {
                Bits c{};
                for (int y = 0; y < H; ++y) c.set(idx(y, xx));
                m[xx] = c;
            }
            return m;
        }();
        return masks[x];
    }
    // 左端列以外が1
    static const Bits& not_left_col_mask() {
        static const Bits m = ~col_mask(0);
        return m;
    }
    // 右端列以外が1
    static const Bits& not_right_col_mask() {
        static const Bits m = ~col_mask(W - 1);
        return m;
    }

   public:
    BitBoard() = default;
    explicit BitBoard(const Bits& b) : board_(b) {
    }

    const Bits& board() const {
        return board_;
    }
    Bits& board() {
        return board_;
    }

    // (y,x)をfにする
    void set(int y, int x, bool f = true) {
        board_.set(idx(y, x), f);
    }
    // (y,x)の状態を取得
    bool get(int y, int x) const {
        return board_.test(idx(y, x));
    }
    // (y,x)が1になっているか判定
    bool test(int y, int x) const {
        return board_.test(idx(y, x));
    }
    // (y,x)を0にする
    void reset(int y, int x) {
        board_.reset(idx(y, x));
    }
    // (y,x)をフリップする
    void flip(int y, int x) {
        board_.flip(idx(y, x));
    }
    // 盤面全体をフリップする
    void flip_all() {
        board_.flip();
    }
    // 盤面全体を0にする
    void reset_all() {
        board_.reset();
    }
    // 盤面全体を1にする
    void set_all() {
        board_.set();
    }

    // 盤面の1の個数
    size_t count() const {
        return board_.count();
    }
    // 盤面すべてが1か
    bool all() const {
        return board_.all();
    }
    // 盤面のいずれかが1か
    bool any() const {
        return board_.any();
    }
    // 盤面すべてが0か
    bool none() const {
        return board_.none();
    }

    friend bool operator==(const BitBoard& a, const BitBoard& b) {
        return a.board_ == b.board_;
    }
    friend bool operator!=(const BitBoard& a, const BitBoard& b) {
        return !(a == b);
    }
    BitBoard& operator|=(const BitBoard& other) {
        board_ |= other.board_;
        return *this;
    }
    BitBoard& operator&=(const BitBoard& other) {
        board_ &= other.board_;
        return *this;
    }
    BitBoard& operator^=(const BitBoard& other) {
        board_ ^= other.board_;
        return *this;
    }
    BitBoard operator~() const {
        BitBoard ret = *this;
        ret.board_.flip();
        return ret;
    }
    friend BitBoard operator|(BitBoard a, const BitBoard& b) {
        return a |= b;
    }
    friend BitBoard operator&(BitBoard a, const BitBoard& b) {
        return a &= b;
    }
    friend BitBoard operator^(BitBoard a, const BitBoard& b) {
        return a ^= b;
    }

    // 盤面全体をシフト
    void shift_up() {
        board_ >>= W;
    }
    void shift_down() {
        board_ <<= W;
    }
    void shift_left() {
        board_ = (board_ & not_left_col_mask()) >> 1;
    }
    void shift_right() {
        board_ = (board_ & not_right_col_mask()) << 1;
    }
    BitBoard shifted_up() const {
        BitBoard r = *this;
        r.shift_up();
        return r;
    }
    BitBoard shifted_down() const {
        BitBoard r = *this;
        r.shift_down();
        return r;
    }
    BitBoard shifted_left() const {
        BitBoard r = *this;
        r.shift_left();
        return r;
    }
    BitBoard shifted_right() const {
        BitBoard r = *this;
        r.shift_right();
        return r;
    }

    // 指定行/列だけシフト
    void shift_col_up(int x) {
        assert(0 <= x && x < W);
        Bits c = board_ & col_mask(x);
        c >>= W;
        board_ = (board_ & ~col_mask(x)) | (c & col_mask(x));
    }
    void shift_col_down(int x) {
        assert(0 <= x && x < W);
        Bits c = board_ & col_mask(x);
        c <<= W;
        board_ = (board_ & ~col_mask(x)) | (c & col_mask(x));
    }
    void shift_row_left(int y) {
        assert(0 <= y && y < H);
        Bits r = (board_ & row_mask(y)) >> (y * W);
        r >>= 1;
        board_ = (board_ & ~row_mask(y)) | (r << (y * W));
    }
    void shift_row_right(int y) {
        assert(0 <= y && y < H);
        Bits r = (board_ & row_mask(y)) >> (y * W);
        r <<= 1;
        r &= row_mask(0);
        board_ = (board_ & ~row_mask(y)) | (r << (y * W));
    }

    // 長方形対応右回転
    BitBoard<W, H> rotated_90() const {
        BitBoard<W, H> rot;
        for (int y = 0; y < H; ++y)
            for (int x = 0; x < W; ++x) rot.set(x, H - 1 - y, board_.test(idx(y, x)));
        return rot;
    }

    // 長方形対応左回転
    BitBoard<W, H> rotated_l90() const {
        BitBoard<W, H> rot;
        for (int y = 0; y < H; ++y)
            for (int x = 0; x < W; ++x) rot.set(W - 1 - x, y, board_.test(idx(y, x)));
        return rot;
    }

    // 右回転
    void rotate_90() {
        static_assert(H == W, "requires H == W");
        *this = rotated_90();
    }
    // 左回転
    void rotate_l90() {
        static_assert(H == W, "requires H == W");
        *this = rotated_l90();
    }

    // 行/列フリップ
    void flip_row(int y) {
        assert(0 <= y && y < H);
        board_ ^= row_mask(y);
    }
    void flip_col(int x) {
        assert(0 <= x && x < W);
        board_ ^= col_mask(x);
    }

    // expand（上下左右の隣接）
    void expand() {
        *this = shifted_up() | shifted_down() | shifted_left() | shifted_right();
    }
    BitBoard expanded() const {
        BitBoard r = *this;
        r.expand();
        return r;
    }

    // startからの距離dで到達可能なマス集合
    // - free_mask: 1 = 通行可 0 = 通行不可
    // - 返り値: layers[d] := start から「ちょうどd手」で到達可能なマス集合
    static std::vector<BitBoard> reachable_layers(const BitBoard& free_mask, int start_y,
                                                  int start_x, int max_d) {
        std::vector<BitBoard> layers(max_d + 1);
        if (start_y < 0 || start_y >= H || start_x < 0 || start_x >= W) return layers;
        int start = idx(start_y, start_x);
        if (!free_mask.board_.test(start)) return layers;
        BitBoard visited;
        BitBoard frontier;
        frontier.board_.set(start);
        layers[0] = frontier;
        for (int d = 1; d <= max_d; ++d) {
            visited |= frontier;
            BitBoard next = frontier;
            next.expand();
            next &= free_mask;
            next.board_ &= ~(visited.board_);
            layers[d] = next;
            frontier = next;
            if (frontier.none()) break;
        }
        return layers;
    }
    // max_d以内に到達できるならその最短距離を返す(不可能なら-1)
    static int shortest_moves_from_layers(const BitBoard& free_mask, int start_y, int start_x,
                                          int target_y, int target_x, int max_d) {
        if (start_y < 0 || start_y >= H || start_x < 0 || start_x >= W) return -1;
        if (target_y < 0 || target_y >= H || target_x < 0 || target_x >= W) return -1;
        int start = idx(start_y, start_x);
        int target = idx(target_y, target_x);
        if (start == target) return 0;
        if (!free_mask.board_.test(start) || !free_mask.board_.test(target)) return -1;
        auto layers = reachable_layers(free_mask, start_y, start_x, max_d);
        for (int d = 1; d <= max_d; ++d) {
            if (layers[d].board_.test(target)) return d;
        }
        return -1;
    }
};

template <int H, int W>
ostream& operator<<(ostream& os, const BitBoard<H, W>& bb) {
    for (int i = 0; i < BitBoard<H, W>::width + 2; i++) {
        if (i == 0 || i == BitBoard<H, W>::width + 1) {
            os << "+";
        } else {
            // os << "-";
            os << (i - 1) % 10;
        }
    }
    os << '\n';
    for (int i = 0; i < BitBoard<H, W>::height; i++) {
        // os << "|";
        os << i % 10;
        for (int j = 0; j < BitBoard<H, W>::width; j++) {
            os << (bb.get(i, j) ? "#" : ".");
        }
        os << "|" << '\n';
    }
    for (int i = 0; i < BitBoard<H, W>::width + 2; i++) {
        if (i == 0 || i == BitBoard<H, W>::width + 1) {
            os << "+";
        } else {
            os << "-";
        }
    }
    return os;
}
```

## Usage

```cpp
int main() {
    constexpr int H = 15, W = 20;
    using Board = BitBoard<H, W>;
    Board board;

    board.set(5, 14, true);
    board.set(5, 15, true);

    cout << board << endl;
    board.expand();
    cout << board << endl;
    board.expand();
    cout << board << endl;

    return 0;
}
```

## Verified

- なし
