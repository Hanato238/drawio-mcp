# Edge-Routing Post-Processor — UNUSED

> **This code is not used in production.** It is preserved on this branch
> (`edge-routing-postprocessor`) only. `main` ships the lightweight
> `postprocessor/postprocess.js` at the repo root, which performs xmldom
> normalization only — edge routing and layout are handled client-side
> in the draw.io viewer.

## Why it's here

This directory contains a server-side post-processor that optimizes
draw.io XML edge routing by:

- Simplifying unnecessary waypoints (collinear points, backtracks, trivial jogs)
- Fixing edge-vertex collisions by rerouting around obstacles
- Preserving approach direction for terminal segments (jetty preservation)
- Straightening approach angles and fixing ellipse perimeter offsets

It was introduced in [`2c4c3bf`](../../../../../commit/2c4c3bf) ("feat: add
edge routing post-processor and integrate into all servers"). Subsequent
work in [`3b6df74`](../../../../../commit/3b6df74) ("Preview for mermaid
and ELK support") superseded the active codepath with the
xmldom-only version because edge routing is now handled by the draw.io
editor itself — making this implementation dead code.

## Files

| File | Purpose |
|------|---------|
| `postprocess.js` | Sync CLI driver: parse → metrics → simplify → fix collisions → simplify again → straighten approach |
| `lib/parser.js` | XML/DOM parsing |
| `lib/metrics.js` | Intersections, waypoints, alignment near-misses |
| `lib/optimizer.js` | `simplifyEdges`, `fixCollisions`, `straightenApproach` |
| `lib/geometry.js` | Geometric primitives (intersections, projections, etc.) |

## If revived

To restore this codepath:

1. Update [`mcp-tool-server/src/index.js`](../index.js) to require this
   directory's `postprocess.js` (rather than the xmldom-only version that
   `prepack` copies in from `postprocessor/postprocess.js` at the repo
   root).
2. Update the comment near line 320 — currently says "xmldom
   normalization only … Edge routing is handled by the draw.io editor
   itself" — to reflect server-side edge routing again.
3. Remove the `prepack` copy step in [`mcp-tool-server/package.json`](../../../package.json),
   or have it copy *this* `postprocess.js` instead.
