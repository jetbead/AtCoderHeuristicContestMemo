# ボロノイ図/ドロネー三角形分割

## 概要

- ボロノイ図は、与えられた頂点集合について、各位置はどの頂点に近いかで領域分けした図
- ドロネー三角形は、ボロノイ図の双対で、ボロノイ領域での隣接関係を表している
- 平面グラフを得る手段の一つとして使える可能性がある
  - [AHC045](../ContestMemo/ahc045.md)
  - [AHC051](../ContestMemo/ahc051.md)

## アルゴリズム、実装

- https://qiita.com/tsukasa__diary/items/a835c17e5bf4935636c4
- https://en.wikipedia.org/wiki/Fortune%27s_algorithm
- https://github.com/mapbox/delaunator
- https://github.com/delfrrr/delaunator-cpp
- Boost voronoi diagram

## Boostを使った実装例

- RGBとSimpleSVGクラスは[svgで簡易ビジュアライザ](./svg.md)のもの

```cpp
namespace Voronoi {

#pragma GCC diagnostic push
#pragma GCC diagnostic ignored "-Wdeprecated-enum-float-conversion"
#include "boost/polygon/voronoi.hpp"
using boost::polygon::voronoi_builder;
using boost::polygon::voronoi_diagram;
#pragma GCC diagnostic pop

struct Point {
    int y, x;
    Point() : y(0), x(0) {
    }
    Point(int y, int x) : y(y), x(x) {
    }
};

namespace boost {
namespace polygon {
template <>
struct geometry_concept<Point> {
    typedef point_concept type;
};

template <>
struct point_traits<Point> {
    typedef int coordinate_type;

    static inline coordinate_type get(const Point& point, orientation_2d orient) {
        return (orient == HORIZONTAL) ? point.x : point.y;
    }
};

}  // namespace polygon
}  // namespace boost

void sample() {
    const int H = 1000;
    const int W = 1000;
    const int L = 1000;

    vector<Point> points;

    rep(i, 10) {
        points.emplace_back(xor128() % H, xor128() % W);
    }
    /*
    points.push_back(Point(100, 100));
    points.push_back(Point(100, 200));
    points.push_back(Point(200, 200));
    */
    voronoi_diagram<double> vd;
    construct_voronoi(points.begin(), points.end(), &vd);

    SimpleSVG svg(H, W);

    for (const auto& cell : vd.cells()) {
        const voronoi_diagram<double>::edge_type* edge = cell.incident_edge();
        const auto* e_start = edge;
        // ボロノイ図の境界辺を列挙(無限の場合は、十分遠いところで近似)
        do {
            if (edge->is_primary()) {
                Point p0(-1, -1), p1(-1, -1);

                if (edge->is_finite()) {
                    const auto* v0 = edge->vertex0();
                    const auto* v1 = edge->vertex1();
                    if (edge->cell()->source_index() < edge->twin()->cell()->source_index()) {
                        p0 = Point(v0->y(), v0->x());
                        p1 = Point(v1->y(), v1->x());
                        cerr << "!(" << p0.y << "," << p0.x << ")"
                             << " -> "
                             << "(" << p1.y << "," << p1.x << ")" << endl;
                        svg.line(p0.y, p0.x, p1.y, p1.x);
                    }
                } else {
                    const auto* v0 = edge->vertex0();
                    if (v0) {
                        Point pp0 = points[edge->cell()->source_index()];
                        Point pp1 = points[edge->twin()->cell()->source_index()];
                        Point ep((pp0.x - pp1.x) * (-L), (pp0.y - pp1.y) * (L));
                        p0 = Point(v0->y(), v0->x());
                        p1 = ep;
                        cerr << "(" << p0.y << "," << p0.x << ")"
                             << " -> "
                             << "(" << p1.y << "," << p1.x << ")" << endl;
                        svg.line(p0.y, p0.x, p1.y, p1.x);
                    }
                }
            }

            // ドロネー三角形分割のときの頂点の隣接ペアを列挙
            {
                int i0 = edge->cell()->source_index();
                int i1 = edge->twin()->cell()->source_index();
                Point pp0 = points[i0];
                Point pp1 = points[i1];
                cerr << i0 << "<->" << i1 << endl;
                svg.line(pp0.y, pp0.x, pp1.y, pp1.x, "#ccc");
            }
            edge = edge->next();
        } while (edge != e_start);
    }

    for (const auto& p : points) {
        svg.circle(p.y, p.x, 3, "red", "none");
    }

    svg.save("out.svg");
}

}  // namespace Voronoi
```
