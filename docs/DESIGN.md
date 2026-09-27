# luce-vector design

## Cells, not pixels

A shape's outlines are segments once luce-svg has parsed it (curves flattened,
the transform applied), in document pixels when it is parsed over the whole
canvas. A cell within a band of any segment — the stroke's reach, where a miter
can go, a pixel or two of anti-aliasing — is an edge cell and is drawn
(`cells.lucb`). Every other cell the shape reaches has no edge in it, so the
nonzero winding at its centre holds for all of it: inside the fill, one color
throughout; outside, nothing. Rows are classified a row of cells at a time from
the fill's crossings with the row's centre line, so the cost follows the
outline, never the area.

## A layer

A layer's elements are merged bottom up (`render.lucb`): an element's edge makes
a cell an edge cell, and its inside, one opaque fill over everything below,
makes the cell inside that element alone. Elements are sorted into bins
(`bins.lucb`) so each window of cells parses only the elements near it; edge
cells are drawn a window at a time on luce-canvas's worker pool
(`windows.lucb`). Each element's inside cells come out one color, so one is
drawn and the others share its tile.

An edit marks the cells the changed element covered before and covers after
(`CellMask`); the caller clears only those, and only those are drawn again.

## The graph

A node graph (`graph`) generates shapes: generators, point sets, Clone,
Transform, Merge and fields drive one another along wires into the output. Each
node's value is cached with a stamp of what it read, so an edit computes again
only downstream of itself, and the caller draws again only the cells of the
shapes that changed (`mark_changed_shapes`).
