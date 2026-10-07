# luce-vector

Vector layers for [luced-2d](https://github.com/dymokomi/luced-2d), in Luce Base:
shapes and Bézier paths drawn into [luce-canvas](https://github.com/dymokomi/luce-canvas)
tiles through [luce-svg](https://github.com/dymokomi/luce-svg) only where their
edges are, redrawn only where an edit reaches, and a node graph that generates
shapes. A 15000 × 24000 vector layer costs the outlines it draws, never its area.

## Use

```
def dependency "luce-vector" {
    str owner = "dymokomi"
    str version = "^0.1.0"
}
```

| Export | What it holds |
| --- | --- |
| `vector` | `ShapeSpec` (rectangle, ellipse, line, polygon or path, with fill, stroke and matrix), `VectorElement` and its list edits, `draw_elements` (a layer's elements into its tiles), `CellMask` (the cells an edit reaches), hit testing, the elements in a Prism manifest, and SVG import |
| `graph` | `Graph` (nodes and wires), `evaluate_graph` into a `ShapeList` with a `GraphCache` that computes again only downstream of an edit, the graph in a Prism manifest |

```luce
from luce_vector.vector import ShapeSpec, VectorElement, draw_elements

var no_path: f64[]
var list: VectorElement[1] = [VectorElement(name = "Box", visible = true,
    spec = ShapeSpec(kind = 0, x = 100.0, y = 100.0, width = 400.0, height = 300.0, filled = true, fill_red = 0.8, path = no_path))]
var tiles = try Tiles.create(device, 2000, 1500)
try draw_elements(&tiles, list[..<1], none)
```

[luce-image](https://github.com/dymokomi/luce-image) keeps the elements and the
graph on a document's layers, with their history. See
[docs/DESIGN.md](docs/DESIGN.md) for how a layer is drawn.

## Test

`luc test` runs both modules' tests. GPU tests skip where no device opens.

## License

MIT; see [LICENSE](LICENSE).
