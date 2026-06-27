# ハイパーパラメータの管理クラス(Configクラス)

## 概要

- ハイパーパラメータはConfigクラスなどで管理し、`cfg.param1`のように参照しておくと、変更しやすい
- 設定ファイルや引数からの読み込みにも対応させておくと、Optunaによるチューニングなどもしやすい
  - またはconfig.hhにしておいてincludeするなども考えられるが、ビルドし直しが必要になる
- dump()機能を持たせておくと、実行時の情報をまとめて出力しておける

## コード例

```cpp
struct Config {
    int param1 = 100;
    double param2 = 5.5;

    Config(const char* path = "params.json") {
#ifdef LOCAL
        load_json_file(path);
        dump();
#endif
    }

#ifdef LOCAL
    void load_json_file(const char* path) {
        ifstream fin(path);
        if (!fin) return;
        nlohmann::json j;
        fin >> j;

        param1 = j.value<int>("param1", param1);
        // param1 = j.at("param1").get<int>();
        param2 = j.value<double>("param2", param2);
    }
    void dump() const {
        cerr << "[Config]" << endl;
        cerr << "param1 = " << param1 << endl;
        cerr << "param2 = " << param2 << endl;
    }
    void dump(nlohmann::json& result_json) const {
        result_json["param1"] = param1;
        result_json["param2"] = param2;
    }
#endif
};
```

params.jsonは以下のように用意。

```
{
  "param1": 50,
  "param2": 3.4
}
```

使うときは、以下のように用意。

```cpp
class Solver {
    const Config cfg;

   public:
    Solver() : cfg() {
    }
};
```
