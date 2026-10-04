# Frontier Graph

A standalone, framework-free people graph of the corpus: one point per researcher,
colored by lab, connected by co-authored work (solid lines) and, faintly, by topical
similarity (dashed). No build step, no backend. It loads d3 v7 from cdnjs and one
local data file, nothing else. Design: `docs/frontier-graph-design.md`.

## Run locally

From this folder:

```sh
python3 -m http.server 8765
# open http://localhost:8765/index.html
```

or simply open `index.html` in a browser (works on `file://` because the data is
loaded through a `<script>` tag). All paths are relative, so the folder also works
under any sub-path.

If `data/graph.js` and `data/graph.json` are both missing, the page falls back to
`data/graph.sample.js` (a small hand-made fixture) and shows a "sample data" notice in
the footer. The browser console will log 404s for the two missing files in that case.

## Regenerate the data

From the repo root:

```sh
python3 scripts/build_graph_data.py --corpus-dir data/deep-research --out apps/frontier-graph/data
```

This reads `corpus.sqlite` and writes `data/graph.json` and `data/graph.js` (same payload,
as `window.FRONTIER_GRAPH = ...`). Never hand-edit them. Re-run it after any
`build_corpus_index.py`. `data/graph.sample.*` is a fixture for developing the UI and is
not generated.

## URL parameters

| Param         | Effect                                                                                                                                                 |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `?embed=1`    | Hides the header title and the footer stats; the canvas fills the frame. Use for iframes.                                                              |
| `?data=<url>` | Load graph data from `<url>` (same origin only) instead of `data/graph.js`. A `.json` URL is fetched; a `.js` URL must define `window.FRONTIER_GRAPH`. |

## URL hash state

The hash is written on every change and read on load (and on `hashchange`), so a link
reproduces the view. Keys: `p=<person id>`, `labs=a,b` (highlighted labs), `solo=1` (with a
single lab in `labs`: show only that lab), `kinds=coauthor,topical`, `years=2019-2024`,
`view=bridges`, `view=connectors` or `view=flows`, `w=<min shared works>`, `mech=<tag>`, `iso=1`
(hide people with no visible edges), `from=<org id>` / `to=<org id>` (Career filters),
`rings=0` (career rings off), `undated=0` (Flows view: exclude undated moves). Example:
`index.html?embed=1#p=anthropic:neilhoulsby&years=2020-2026`

## Controls

Click a person: ego view plus detail drawer (click a collaborator to jump to them).
Click an edge: pair view with the shared works. Lab chips: click toggles highlight,
shift-click shows only that lab, clicking the sole active chip resets. `/` focuses search,
`Esc` clears, left/right arrows walk the collaborator list while the drawer is open.
Drag a node to pin-and-reheat; scroll or pinch to zoom.

## Employment history views

Everything below is driven by optional fields (`orgs`, `people[].history`, `flows`, `meta.employment`;
see `docs/employment-history-contract.md` §5-§6). With none of them in the data the app renders exactly as
before, the Career block is hidden and the Flows view shows "No employment history in this dataset".

- **Flows** (header button, `#view=flows`; code in `flows.js`): replaces the force graph with a directed
  chord diagram of moves between orgs. Groups are the lab orgs plus the largest external orgs by total flow
  count, 8 groups at most; the rest are merged into "Other". Ribbons are coloured by the org moved from; hover
  shows `A → B · N people (years)`. Click a ribbon for the people who made that move (lab dot, year or
  "undated", confidence); click an arc for "Moved in" / "Moved out". Clicking a person returns to the graph
  with them selected. The Years slider filters by the year of the new role; "Include undated moves"
  (default on) controls undated flows. The edge-kind, min-works, mechanism and career filters are hidden here;
  lab chips emphasise ribbons touching that lab. Footer: `Moves N · People M · Undated K`. Leaving the view
  (Reset or another preset) shows the existing force graph again; the simulation is not re-run.
- **Career rings** (filter rail, default on, `rings=0` to hide): thin outer arcs around a node, one per prior
  org in that org's colour, oldest first clockwise from 12 o'clock, at most 4 (oldest dropped). The current
  org is never drawn. They dim together with the node.
- **Career section** in the person drawer (above Collaborators): stints in order with org chip, raw text if it
  differs from the label, dates (`2022 – 2025-07`, `since 2025-07`, `undated`), role, a confidence pill
  (high / medium / low) and a "current" flag.
- **Career filters** (graph view): "Came from" (any prior org) and "Went to" (any org that follows a lab
  stint), with counts. They highlight matching people the same way the mechanism select does and combine with
  it and with each other.

## Integration

**github.io (al-folio / Jekyll)**: copy this folder to `assets/frontier-graph/`. al-folio runs
`jekyll-terser` over every `.js` file that does not end in `.min.js` and ignores exclude
settings, so rename the scripts in the copy and patch the references:

```sh
cd assets/frontier-graph
mv app.js app.min.js; mv flows.js flows.min.js; mv data/graph.js data/graph.min.js; mv data/graph.sample.js data/graph.sample.min.js
sed -i '' -e 's#data/graph\.js#data/graph.min.js#g' -e 's#data/graph\.sample\.js#data/graph.sample.min.js#g' -e 's#"app\.js"#"app.min.js"#g' -e 's#"flows\.js"#"flows.min.js"#g' index.html app.min.js
```

Also add `assets/frontier-graph/*` and `assets/frontier-graph/data/*` to `jekyll-minifier.exclude`
in `_config.yml` so the HTML is served untouched. Then add
`_pages/frontier-graph.md` with a full-width iframe to
`/assets/frontier-graph/index.html?embed=1`, and add a nav entry. For example:

```html
<iframe
  src="{{ '/assets/frontier-graph/index.html?embed=1' | relative_url }}"
  style="width:100%;height:85vh;border:0"
  loading="lazy"
  title="Frontier Graph"
></iframe>
```

**personal-dashboard (Next.js)**: copy this folder to `frontend/public/frontier-graph/`
and add a protected route that renders an iframe to
`/frontier-graph/index.html?embed=1`. That repository has its own PR evidence gates, so
do this as a separate PR there, not from this repo.

**Refresh**: re-run `scripts/build_graph_data.py` after any `build_corpus_index.py`, then
re-copy the `data/` folder to each target.
