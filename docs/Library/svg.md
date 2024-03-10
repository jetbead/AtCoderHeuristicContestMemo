# SVGで簡易ビジュアライザ

## 概要

- Scalable Vector Graphics
- xmlベースの2次元ベクタ形式イメージ用画像形式

## Links

- https://www.w3.org/Graphics/SVG/
- https://developer.mozilla.org/ja/docs/Web/SVG

## 表示方法

- chromeなどで開く
- macでpngに変換
  - `qlmanage -t -s 1024 -o . output.svg`で`output.svg.png`に変換できる
- jupyter notebook
  - `from IPython.display import SVG, display`した上で、`display(SVG(...))`で出力できる

## 簡易利用クラス

```cpp
struct RGB {
    int r, g, b;

    RGB() : r(0), g(0), b(0) {
    }
    RGB(int r, int g, int b) : r(r), g(g), b(b) {
    }
    // 0〜1の値の色付け
    void set(double val) {
        val = min(val, 1.0);
        val = max(val, 0.0);
        if (val < 0.5) {
            double x = val * 2.0;
            r = (int)(30.0 * (1.0 - x) + 144.0 * x);
            g = (int)(144.0 * (1.0 - x) + 255 * x);
            b = (int)(255.0 * (1.0 - x) + 30.0 * x);
        } else {
            double x = val * 2.0 - 1.0;
            r = (int)(144.0 * (1.0 - x) + 255 * x);
            g = (int)(255.0 * (1.0 - x) + 30.0 * x);
            b = (int)(30.0 * (1.0 - x) + 70.0 * x);
        }
    }
    string to_string() const {
        stringstream ss;
        ss << "#";
        ss << hex << r;
        ss << hex << g;
        ss << hex << b;
        return ss.str();
    }
};

class SimpleSVG {
    int H, W;
    vector<string> xmls;

    void add_arrow_marker() {
        stringstream ss;
        ss << "<marker id=\"arrow\" viewBox=\"-5 -5 10 10\" orient=\"auto\">";
        ss << " <polygon points=\"-5,-5 5,0 -5,5\" fill=\"black\" stroke=\"none\"/>";
        ss << "</marker>";
        xmls.push_back(ss.str());
    }

    void init() {
        add_arrow_marker();
    }

   public:
    SimpleSVG(int H, int W) : H(H), W(W) {
        init();
    }

    void rect(int y, int x, int h, int w, const string& fill = "none",
              const string& stroke = "black", const string& stroke_width = "1") {
        stringstream ss;
        ss << "<rect";
        ss << " x=\"" << x << "\" y=\"" << y << "\"";
        ss << " width=\"" << w << "\" height=\"" << h << "\"";
        ss << " fill=\"" << fill << "\"";
        ss << " stroke=\"" << stroke << "\"";
        ss << " stroke-width=\"" << stroke_width << "\"";
        ss << " />";
        xmls.push_back(ss.str());
    }

    void rect_with_wall(int y, int x, int h, int w, bool U = false, bool D = false, bool L = false,
                        bool R = false, const string& fill = "none", const string& stroke = "black",
                        const string& stroke_width = "1", const string& wall_stroke_width = "5") {
        rect(y, x, h, w, fill, stroke, stroke_width);
        if (U) line(y, x, y, x + w, stroke, wall_stroke_width);
        if (D) line(y + h, x, y + h, x + w, stroke, wall_stroke_width);
        if (L) line(y, x, y + h, x, stroke, wall_stroke_width);
        if (R) line(y, x + w, y + h, x + w, stroke, wall_stroke_width);
    }

    void circle(int cy, int cx, int r, const string& fill = "none", const string& stroke = "black",
                const string& stroke_width = "1") {
        stringstream ss;
        ss << "<circle";
        ss << " cx=\"" << cx << "\" cy=\"" << cy << "\"";
        ss << " r=\"" << r << "\"";
        ss << " fill=\"" << fill << "\"";
        ss << " stroke=\"" << stroke << "\"";
        ss << " stroke-width=\"" << stroke_width << "\"";
        ss << " />";
        xmls.push_back(ss.str());
    }

    void line(int y1, int x1, int y2, int x2, const string& stroke = "black",
              const string& stroke_width = "1") {
        stringstream ss;
        ss << "<line";
        ss << " x1=\"" << x1 << "\" y1=\"" << y1 << "\"";
        ss << " x2=\"" << x2 << "\" y2=\"" << y2 << "\"";
        ss << " stroke=\"" << stroke << "\"";
        ss << " stroke-width=\"" << stroke_width << "\"";
        ss << " />";
        xmls.push_back(ss.str());
    }

    void arrow(int y1, int x1, int y2, int x2, const string& stroke_width = "4") {
        stringstream ss;
        ss << "<line";
        ss << " x1=\"" << x1 << "\" y1=\"" << y1 << "\"";
        ss << " x2=\"" << x2 << "\" y2=\"" << y2 << "\"";
        ss << " stroke=\"black\"";
        ss << " stroke-width=\"" << stroke_width << "\"";
        ss << " marker-end=\"url(#arrow)\"";
        ss << " />";
        xmls.push_back(ss.str());
    }

    // 左上揃え: dominant_baseline=hanging, text_anchor=start
    void text(int y, int x, const string& str, int font_size = 15, const string& fill = "black",
              const string& dominant_baseline = "middle", const string& text_anchor = "middle") {
        stringstream ss;
        ss << "<text";
        ss << " x=\"" << x << "\" y=\"" << y << "\"";
        ss << " font-size=\"" << font_size << "\"";
        ss << " fill=\"" << fill << "\"";
        ss << " dominant-baseline=\"" << dominant_baseline << "\"";
        ss << " text-anchor=\"" << text_anchor << "\"";
        ss << ">" << str << "</text>";
        xmls.push_back(ss.str());
    }

    void image(int y, int x, int h, int w, const std::string& file_path) {
        std::stringstream ss;
        ss << "<image";
        ss << " x=\"" << x << "\" y=\"" << y << "\"";
        ss << " height=\"" << h << "\"";
        ss << " width=\"" << w << "\"";
        ss << " xlink:href=\"" << file_path << "\"";
        ss << "></image>";
        xmls.push_back(ss.str());
    }

    void clear() {
        xmls.clear();
    }

    string dump(const string& id = "") {
        stringstream ss;
        ss << "<svg ";
        if (id != "") ss << "id=\"" << id << "\" ";
        ss << "xmlns=\"http://www.w3.org/2000/svg\"";
        ss << " viewBox=\"0 0 " << W << " " << H << "\"";
        ss << " width=\"" << W << "\" height=\"" << H << "\">" << endl;

        for (const string& xml : xmls) {
            ss << xml << endl;
        }

        ss << "</svg>" << endl;
        return ss.str();
    }

    void save(const string& file_path, const string& id = "") {
        ofstream ofs(file_path);
        ofs << dump(id);
    }
};

```

## jupyter notebookでアニメーション


- `pip install ipywidgets`しておく
  - notebookは起動してた場合は再起動する
- 1ファイルに各ターンのsvg`<svg>〜</svg>`を連続して書き込んだファイルを用意する
- 以下のような感じでnotebook上で読み込んで表示する

```
# outs.svgの中身のイメージ
<svg>
...
</svg>

<svg>
...
</svg>

<svg>
...
</svg>
```
```python
from IPython.display import display, SVG, HTML
from ipywidgets import Play, Layout, IntSlider, jslink, HBox, Button, interactive_output

# 複数のsvgを連続して出力してあるファイルを読み込む
with open("outs.svg") as f:
    svgs = list(map(lambda x: x + "</svg>", f.read().replace("\n","").split("</svg>")))
    del(svgs[-1])

# widget表示
def display_svg(turn):
    display(SVG(svgs[turn-1]))
play = Play(value=1, min=1, max=len(svgs), step=1, interval=200)
slider = IntSlider(1, 1, len(svgs), 1, layout=Layout(width="500px"))
jslink((play, "value"), (slider, "value"))

def increment_step(b):
    if slider.value + 1 < len(svgs):
        slider.value += 1
def decrement_step(b):
    if slider.value - 1 >= 0:
        slider.value -= 1
inc_button = Button(description="+",layout=Layout(width="50px"))
inc_button.on_click(increment_step)
dec_button = Button(description="-",layout=Layout(width="50px"))
dec_button.on_click(decrement_step)

ui = HBox([play, inc_button, dec_button, slider])
display(ui, interactive_output(display_svg, {"turn": slider}))
```


## emscriptenで公式ビジュアライザのように動かす

- emscriptenを使って、公式のビジュアライザのようにWebAssemblyにして動かせる
- C++で書くことで、解答のC++コードと同じコードが使い回せる

### 環境構築

- emsdkをインストール
  - https://github.com/emscripten-core/emsdk
  - https://emscripten.org/docs/getting_started/downloads.html
  - インストールして、activateしておく(emccが使えるようになる)
  - アンインストールは、emsdkのディレクトリを消せばOK
- pythonをインストール
  - ローカルサーバーを立ち上げるのに利用(サーバーに置く場合は不要)

### コード例

(間違った書き方してるかもしれないので注意)

```cpp
// vis.cc
#include <emscripten/bind.h>
#include <emscripten/emscripten.h>

// ...

// input,outputに対してターンtの状態をsvg文字列で返す
string vis(const string& input, const string& output, int t) {
    string svg_text = "<svg> ... </svg>";
    return svg_text;
}

// 最大ターン数
int get_max_turn(const string& input, const string& output) {
    int turn = 100;
    return turn;
}

EMSCRIPTEN_BINDINGS(mod) {
    emscripten::function("vis", &vis);
    emscripten::function("get_max_turn", &get_max_turn);
}
```
```html
<!-- index.html(細かいところは公式ビジュアライザを参考) -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
  </head>
  <body>
    <!-- フォームやdivなどを用意 -->
    <!-- ... -->
    <script type="module">
      // 描画を更新
      function visualize() {
        const input = document.getElementById("input").value;
        const output = document.getElementById("output").value;
        const t = Number(document.getElementById("turn").value);
        try {
          const ret = window.Module.vis(input, output, t);
          document.getElementById("result").innerHTML = ret;
        } catch (error) {
          console.log(error);
          document.getElementById("result").innerHTML = "<p>Invalid</p>";
        }
      }
      window.visualize = visualize; // window.hogeにいれることでグローバルでアクセスできる(html側から呼び出せる)

      // tの更新があった場合の処理
      function update_t(t) {
        const max_turn = Number(document.getElementById("turn").max);
        const new_turn = Math.min(Math.max(0, t), max_turn);
        document.getElementById("turn").value = new_turn;
        document.getElementById("t_bar").value = new_turn;
        visualize();
      }
      window.update_t = update_t;

      // アニメーションなどのコードを追加
      // ...
    </script>
    <script async type="text/javascript" src="vis.js"></script>
  </body>
</html>
```

### ビルド

```
# vis.jsとvis.wasmができる
emcc -lembind vis.cc -O3 -o vis.js
```

### 利用方法(ローカル)

```
# vis.js, vis.wasm, index.htmlがあるディレクトリで以下を実行
python -m http.server 8000

# http://localhost:8000/ にアクセス
```


## Tips

- tooltipsを表示
  - `<g><title>meg</title><rect>...</rect></g>`のようにすると、マウスカーソルを載せたときにmsg情報を表示できる
