# ハイパーパラメータの管理クラス(Configクラス)

## 概要

- ハイパーパラメータはConfigクラスなどで管理し、`config.param1`のように参照しておくと、変更しやすい
- 設定ファイルや引数からの読み込みにも対応させておくと、Optunaによるチューニングなどもしやすい
- dump()機能を持たせておくと、実行時の情報をまとめて出力しておける

## コード例

```cpp
class Config {
    double time_limit;
    int beam_width;
    int param1;
    int param2;

#ifdef LOCAL
    void dump(nlohmann::json& result_json) {
        result_json["time_limit"] = time_limit;
        result_json["beam_width"] = beam_width;
        result_json["param1"] = param1;
        result_json["param2"] = param2;
    }
#endif
};
```
