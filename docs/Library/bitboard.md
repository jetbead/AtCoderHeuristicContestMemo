# ビットボード

## 概要

- 2次元グリッドの各マスを1bitで表現
- 省メモリや、bit演算で処理することによる高速化が期待できる

## コード

```cpp
template <int H, int W>
struct BitBoard {
    bitset<H * W> board;

   public:
    // 盤面の高さ
    int get_H() const {
        return H;
    }
    // 盤面の幅
    int get_W() const {
        return W;
    }

    // (y,x)をfにする
    void set(int y, int x, bool f) {
        board[y * W + x] = f;
    }
    // (y,x)の状態を取得
    bool get(int y, int x) const {
        return board[y * W + x];
    }
    // (y,x)の状態をフリップ
    void flip(int y, int x) {
        board[y * W + x] = ~board[y * W + x];
    }
    // 盤面全体をフリップ
    void flip() {
        board.flip();
    }

    // 盤面でのtrueの個数
    int count() const {
        return board.count();
    }
    // 盤面すべてがtrueか
    bool all() const {
        return board.all();
    }
    // 盤面のいずれかがtrueか
    bool any() const {
        return board.any();
    }
    // 盤面すべてがfalseか
    bool none() const {
        return board.none();
    }

    bool operator==(const BitBoard<H, W>& other) const {
        return board == other.board;
    }
    bool operator!=(const BitBoard<H, W>& other) const {
        return !(board == other.board);
    }
    BitBoard<H, W>& operator|=(const BitBoard<H, W>& other) {
        board |= other.board;
        return *this;
    }
    BitBoard<H, W>& operator&=(const BitBoard<H, W>& other) {
        board &= other.board;
        return *this;
    }
    BitBoard<H, W>& operator^=(const BitBoard<H, W>& other) {
        board ^= other.board;
        return *this;
    }
    BitBoard<H, W> operator~() {
        BitBoard<H, W> ret = *this;
        ret.board.flip();
        return ret;
    }
};

template <int H, int W>
ostream& operator<<(ostream& os, const BitBoard<H, W>& bb) {
    for (int i = 0; i < bb.get_W() + 2; i++) {
        if (i == 0 || i == bb.get_W() + 1) {
            os << "+";
        } else {
            // os << "-";
            os << (i - 1) % 10;
        }
    }
    os << endl;
    for (int i = 0; i < bb.get_H(); i++) {
        // os << "|";
        os << i % 10;
        for (int j = 0; j < bb.get_W(); j++) {
            os << (bb.get(i, j) ? "#" : ".");
        }
        os << "|" << endl;
    }
    for (int i = 0; i < bb.get_W() + 2; i++) {
        if (i == 0 || i == bb.get_W() + 1) {
            os << "+";
        } else {
            os << "-";
        }
    }
    return os;
}

template <int H, int W>
class BitBoardUtils {
    array<bitset<H * W>, H> mask_w;
    array<bitset<H * W>, W> mask_h;
    bitset<H * W> mask_left, mask_right;

    void make_mask() {
        for (int x = 0; x < W; x++) {
            mask_w[0].set(x);
            for (int y = 0; y < H; y++) {
                mask_h[x] |= bitset<H * W>(1) << (y * W + x);
            }
        }
        for (int y = 1; y < H; y++) {
            mask_w[y] = mask_w[0] << (y * W);
        }
        mask_left = ~mask_h[0];
        mask_right = ~mask_h[W - 1];
    }

   public:
    BitBoardUtils() {
        make_mask();
    }

    // 盤面全体を上下左右にずらす
    void shift_up(BitBoard<H, W>& board) {
        board.board >>= W;
    }
    void shift_down(BitBoard<H, W>& board) {
        board.board <<= W;
    }
    void shift_left(BitBoard<H, W>& board) {
        board.board = (board.board & mask_left) >> 1;
    }
    void shift_right(BitBoard<H, W>& board) {
        board.board = (board.board & mask_right) << 1;
    }
    // 指定行/列のみを上下左右にずらす
    void shift_up(BitBoard<H, W>& board, int x) {
        bitset<H * W> column = board.board & mask_h[x];
        column >>= W;
        board.board = (board.board & ~mask_h[x]) | (column & mask_h[x]);
    }
    void shift_down(BitBoard<H, W>& board, int x) {
        bitset<H * W> column = board.board & mask_h[x];
        column <<= W;
        board.board = (board.board & ~mask_h[x]) | (column & mask_h[x]);
    }
    void shift_left(BitBoard<H, W>& board, int y) {
        bitset<H * W> row = (board.board & mask_w[y]) >> (y * W);
        row >>= 1;
        board.board = (board.board & ~mask_w[y]) | (row << (y * W));
    }
    void shift_right(BitBoard<H, W>& board, int y) {
        bitset<H * W> row = (board.board & mask_w[y]) >> (y * W);
        row <<= 1;
        row &= mask_w[0];
        board.board = (board.board & ~mask_w[y]) | (row << (y * W));
    }
    // 指定行/列のフリップ
    void flip_row(BitBoard<H, W>& board, int y) {
        board.board ^= mask_w[y];
    }
    void flip_column(BitBoard<H, W>& board, int x) {
        board.board ^= mask_h[x];
    }

    // 盤面全体を90度右回転 O(HW)
    void rotate_90(BitBoard<H, W>& board) {
        BitBoard<H, W> rot;
        for (int y = 0; y < H; y++) {
            for (int x = 0; x < W; x++) {
                rot.set(x, H - 1 - y, board.get(y, x));
            }
        }
        swap(board, rot);
    }
    // 盤面全体を90度左回転 O(HW)
    void rotate_l90(BitBoard<H, W>& board) {
        BitBoard<H, W> rot;
        for (int y = 0; y < H; y++) {
            for (int x = 0; x < W; x++) {
                rot.set(W - 1 - x, y, board.get(y, x));
            }
        }
        swap(board, rot);
    }

    // boardの位置から上下左右に1マス移動できる場所
    // 注意: 元の位置は含まないので、含むようにしたい場合はorを取る
    void expand(BitBoard<H, W>& board) {
        BitBoard<H, W> bb_up = board;
        shift_up(bb_up);
        BitBoard<H, W> bb_down = board;
        shift_down(bb_down);
        BitBoard<H, W> bb_left = board;
        shift_left(bb_left);
        BitBoard<H, W> bb_right = board;
        shift_right(bb_right);
        board.board = bb_up.board | bb_down.board | bb_left.board | bb_right.board;
    }
};
```

## Usage

```cpp
int main() {
    constexpr int H = 15, W = 20;
    using Board = BitBoard<H, W>;
    using BoardUtils = BitBoardUtils<H, W>;
    Board board;
    BoardUtils board_utils;

    // board.set(5, 15, true);
    // board.set(5, 14, true);

    board_utils.expand(board);
    board_utils.expand(board);

    cout << board << endl;

    cout << sizeof(board) << endl;
    return 0;
}

```

## Verified

- なし
