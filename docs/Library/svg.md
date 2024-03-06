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


## Tips

- tooltipsを表示
  - `<g><title>meg</title><rect>...</rect></g>`のようにすると、マウスカーソルを載せたときにmsg情報を表示できる
