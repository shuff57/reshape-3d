# reSHape

**reSHape** is a beginner-friendly layer over [JSCAD](https://openjscad.xyz/)
(`@jscad/modeling` + `@jscad/regl-renderer`, both MIT, vendored) that gives 3D
modeling **plain-words function names** for the classroom: `box`, `rect`,
`disc`, `ball`, `tube`, `cone`, `ring`, `poly`, `extrude`, `revolve`, `turn`,
`sit`. Built for the [shCode](https://github.com/shuff57/shCode) high-school
CS course, where students model real 3D parts in a sandboxed iframe.

```js
function main() {
  const arm = translate([50, 0, 0], box(40, 20, 20))
  const cap = sit(ring(14, 4))

  return [turn(90, arm), cap]
}
```

Positional arguments for the values a shape cannot exist without, every named
extra in a trailing `{ }` — `disc(50, 4)`, `revolve(profile, { segments: 64 })`.
Subtract works on arrays, so cutting holes is one call:

The layer is **strictly additive** — it never renames or wraps a real JSCAD
name, so reSHape code and raw `@jscad/modeling` code mix freely in one file.
When a student is ready to "graduate", the same program runs on
[jscad.app](https://jscad.app) after a mechanical rename; the docs include the
conversion table.

## Try it

Open **[the live demo](https://shuff57.github.io/reshape-3d/)** — it embeds the
runner in a sandboxed iframe, exactly the way a host app is meant to.

## Use it in your own page

reSHape runs inside a host iframe. The host loads `runner.html?code=...` with
a **sandboxed iframe without `allow-same-origin`** — student/visitor code then
runs in an opaque origin and cannot touch your app:

```html
<iframe sandbox="allow-scripts allow-downloads"
        src="https://shuff57.github.io/reshape-3d/runner.html?code=..."></iframe>
```

Sketch source is passed as base64url in `?code=`. Inside the sketch:
`main()` builds and returns a shape or array of shapes; the runner renders it
in a WebGL viewport with orbit/zoom controls and a save bar (STL / 3MF / OBJ /
SVG).

Console output and runtime errors are piped to the host with `postMessage`
(`source: 'preview-console'` / `'preview-error'`), and render timing arrives
as `reshape-rebuilt`. Student-facing parameter sliders post `reshape-params`.

## In this repo

| File | Covers |
|---|---|
| `reshape.js` | The additive naming layer (~1,000 lines, IIFE, refuses to shadow a real JSCAD name) |
| `runner.html` | Sandboxed iframe host: CJS scope shim, vendored bundles, `?code=` injection, save bar |
| `svg.js` | SVG serializer for 2D designs (`geom2` has no polygon mesh for STL/3MF/OBJ) |
| `lib/` | Vendored `@jscad/modeling@2.13.0` + `@jscad/regl-renderer@2.6.15` + `@jscad/io` UMD bundles (MIT). Nothing loads from a CDN. |
| `docs/` | `reference.md` (hand-authored API reference with the graduation/renaming table), challenge ladder, integration notes, license notes |

## Security model

- Sketches run in an **opaque origin**: the iframe must be embedded with
  `sandbox="allow-scripts allow-downloads"` and **without** `allow-same-origin`.
- `runner.html` refuses to execute `?code=` when opened as a top-level document
  (`window.top === window.self`). It is defence in depth behind the sandbox
  attribute — embedded, the sandbox is what contains the code; direct-open is
  refused so a link with code in the URL never runs on the app's own origin.
- All libraries are vendored static files. There is no server, no telemetry,
  no CDN fetches.

## History

reSHape began life as the in-app 3D runtime of the shCode course (originally
under `public/jscad/`, layer named "shCAD"). The public repo is a
fresh-history extract of shCode's `public/reshape/`. The `svg.js` serializer
was written for shCode's graded assignments and ships here unchanged.

## License

MIT — see [LICENSE](LICENSE). The vendored JSCAD bundles remain MIT under the
JSCAD Organization's copyright (see `docs/LICENSE.md` for the per-file notes).

[JSCAD]: https://openjscad.xyz/