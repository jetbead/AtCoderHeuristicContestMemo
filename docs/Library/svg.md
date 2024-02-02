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

    void save(const string& file_path) {
        ofstream ofs(file_path);
        ofs << "<svg xmlns=\"http://www.w3.org/2000/svg\"";
        ofs << " viewBox=\"0 0 " << W << " " << H << "\"";
        ofs << " width=\"" << W << "\" height=\"" << H << "\">" << endl;

        for (const string& xml : xmls) {
            ofs << xml << endl;
        }

        ofs << "</svg>" << endl;
    }
};
```

## Tips

- tooltipsを表示
  - `<g><title>meg</title><rect>...</rect></g>`のようにすると、マウスカーソルを載せたときにmsg情報を表示できる
