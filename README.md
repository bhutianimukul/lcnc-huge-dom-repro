# lcnc-huge-dom-repro

Synthetic **huge-DOM + N-level nested-iframe** page to reproduce Chromium renderer
OOM ("Aw Snap") in the LCNC robust algorithm (`findAllElementsOfTypeFromPage`)
when it walks and serializes every candidate across same-origin nested iframes.

Auto-deployed to GitHub Pages on every push to `main`.

## URL

```
https://bhutianimukul.github.io/lcnc-huge-dom-repro/?mb=40&depth=10
```

Feed that to a BrowserStack Automate / LCNC replay session and point element
location at `#deep-target` (a button nested inside all 10 iframes).

## Query params (all optional)

| Param | Default | Meaning |
|-------|---------|---------|
| `mb` | 40 | **min serialized DOM size per frame, in MB** |
| `depth` | 10 | iframe nesting levels (each a red-bordered box) |
| `widgets` / `iwidgets` | 800 / 300 | interactive candidates (button/input/link/select) per frame |
| `attrs` | 25 | `data-*` attributes per element (fat outerHTML) |
| `text` | 500 | text length per blob |
| `spine` | 12 | wrapper-div nesting depth (long xpath / cssPath) |
| `imgpx` | 640 | noise-image resolution — bigger = fatter data-URI + more GPU memory |

**Total serialized ≈ `(depth + 1) × mb` MB.** Default ≈ 11 × 40 = **~440 MB**
(may OOM on build — that's the point; scale down for a page that survives build
but stresses the walk, e.g. `?mb=20&depth=10`).

The on-page HUD reports live element count + serialized MB across all frames.

## Structure

```
main page (visual cards + widgets)        ← no border
└─ #frame-lvl-1   (red-bordered iframe)
   └─ #frame-lvl-2
      └─ … down to …
         └─ #frame-lvl-10  →  #deep-target
```

Iframes use `srcdoc` (same-origin), so the injected robust algorithm can descend
into them — cross-origin iframes wouldn't reproduce the OOM path.
