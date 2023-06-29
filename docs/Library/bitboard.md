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
    int get_H() const {
        return H;
    }
    int get_W() const {
        return W;
    }

    void set(int y, int x, bool f) {
        board[y * W + x] = f;
    }
    bool get(int y, int x) const {
        return board[y * W + x];
    }
    void flip(int y, int x) {
        board[y * W + x] = ~board[y * W + x];
    }
    void flip() {
        board.flip();
    }

    int count() const {
        return board.count();
    }
    bool test() const {
        return board.test();
    }
    bool all() const {
        return board.all();
    }
    bool any() const {
        return board.any();
    }
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
    bitset<H * W> mask_left, mask_right;

    void make_mask() {
        for (int y = 0; y < H; y++) {
            mask_left |= bitset<H * W>(1) << (y * W);
            mask_right |= bitset<H * W>(1) << (y * W + W - 1);
        }
        mask_left = ~mask_left;
        mask_right = ~mask_right;
    }

   public:
    BitBoardUtils() {
        make_mask();
    }

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
