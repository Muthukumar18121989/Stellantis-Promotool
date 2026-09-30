# Promotool — Promotion Optimization

A self-contained HTML prototype of the TwinX / Stellantis **Promotool — Promotion
Optimization** product. The whole application is one file: open `index.html` in a
browser and it runs, with no build step, no server and no dependencies.

## The workflow it covers

```
PROJECT → EXPERIMENT → CONFIGURATION → SIMULATION → VALIDATION → REPORTS
```

- **Experiments** — projects, filters, lifecycle stages and the experiment table,
  with search, sorting, paging, selection and a Create New Promo flow.
- **Promotion Configuration** — a three-step wizard: promotion parameters, family
  and article selection (with exclusions, volume analysis and price analysis per
  family), and the simulation result.
- **Simulation** — Simulate hands the run back to the experiment table, where the
  promotion's row carries a progress bar; its result page opens from that row.
- **Experiment Compare** — tick two or more promotions and press Compare for a
  side-by-side configuration summary, the families they touch, and the metric
  comparison.
- **Promotion Validation** — the logistics volume split across months.
- **Report Hub** — the Promotions and Advanced Analytics tabs.

## Architecture

Three layers, in that order in the file:

1. **`TwinXData`** — the mock dataset: registries (statuses, markets, brands,
   promotion types, families, periods), projects and experiments. Analytics are
   derived from ids through an avalanche hash, so figures are stable across
   renders yet genuinely differ between neighbouring records.
2. **`TwinXService`** — an async, server-shaped API: paging, filtering and
   sorting all happen below this line, and every call returns a promise.
   `mockTransport` is the only thing that knows the data is local. **Swapping
   `transport` for one that calls the real endpoints is the whole backend
   integration** — no component changes.
3. **Components + application** — pure functions that turn service results into
   markup, plus the routing, wiring and motion. No component reads the data
   layer directly.

Nothing is hardcoded in markup: every screen renders from structured data.

## Visual system

A CSS custom-property token layer, with `data-theme` (`dark` | `light` |
`umber`) and `data-accent` (`mono` | `ice` | `amber` | `lime` | `violet`) on
`<html>`. Dark and light are monochrome by design; hue is reserved for meaning —
`--ok`, `--bad`, `--warn`, `--promo`, and `--twinx` for machine-generated
figures. The chart ramp `--c1`…`--c9` inverts between themes. State is carried
by form, not colour.

**Umber** is a warm brand theme: deep brown navigation against warm paper, with
a gold accent. It ships its own accent and semantic hues, so the accent swatches
stand down while it is on. Adding a theme is two things: an entry in the
`THEMES` array and a `:root[data-theme="…"]` block in the token layer. The
choice is picked from the theme control in the top bar and remembered in
`localStorage` under `pt.theme`.

The navigation panel floats — inset from the window on every side, rounded, with
its own surface tokens (`--rail-bg`, `--rail-active`, `--rail-fg`, …) so a theme
can invert it without touching anything else.

## Running it

Open `index.html`, or serve the folder with any static server:

```
python3 -m http.server 8000
```

It also works as a GitHub Pages site straight from the repository root.
