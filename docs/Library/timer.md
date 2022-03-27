# Timer

## 概要

- 時間計測クラス

### 注意

- `chrono`を利用している
  - AtCoder 環境では、本番では同じスペックのマシンが利用されている模様だが、コンテスト終了後などはスペックが異なるマシンが混在するケースがあり得る模様
  - `rdtsc`を使った方法では、計測時間が実際の時間とずれてしまう恐れがあるため、`chrono`を使っている
  - あまり体感は変わらないが、もしかしたら呼び出し回数がかなり多い場合に差があるかも
- TIME_LIMIT は余裕をもたせる
  - コンテストページでも注意書きされているが、測定時間にブレがあるため、制限時間が 2 秒で`TIME_LIMIT=2.0`とするとオーバーする可能性がある
  - 基本は、「1.8 秒(おそらく余裕)」や「1.9 秒(やや怪しい)」ぐらいにしておくのが良い
  - 短期コンなどシステムテスト形式ではない場合は、ギリギリ(1.95 秒とか)を攻めてもよいかも

## 計算量

## コード

```cpp
class Timer {
    std::chrono::system_clock::time_point start_time;
    std::chrono::system_clock::time_point getNow() {
        return std::chrono::system_clock::now();
    }

   public:
    void start() {
        start_time = getNow();
    }
    float getSec() {
        float count =
            std::chrono::duration_cast<std::chrono::microseconds>(getNow() - start_time).count();
        return count / 1e6;
    }
};
```

## Usage

```cpp
Timer timer;
timer.start();

static const double TIME_LIMIT = 1.9; // 秒
while(true) {
    double sec = timer.getSec();
    if (sec > TIME_LIMIT) break;

    // ...
}
```

## Verified
