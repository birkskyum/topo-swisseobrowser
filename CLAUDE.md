# swissEO Browser: notes for Claude Code

## Writing style

- **Never use em-dashes** (the long dash, U+2014), in any text: UI strings, layer descriptions, the Info tab, README, code
  comments, commit messages and replies. Use a colon, comma, semicolon, parentheses or a new sentence
  instead. En-dashes in number ranges (1991–2020, −1…1) are fine.
- UI and documentation texts are in English. Highlight stories have at most ten lines and credit their
  source.
- Keep the "Not for operational use" notice and the Claude Code note in the Info tab and the README.

## Repository

- The app is the single file `swisseo-browser.html` (no build, no backend). It belongs together with
  `README.md`, `CLAUDE.md`, `.github/workflows/pages.yml`, the issue forms in `.github/ISSUE_TEMPLATE/` and the
  thumbnails it references in `public/previews/`. Everything else is the upstream Copernicus Browser, kept only as a reference:
  do not modify it.
- **Git: push only to the remote `origin`** (github.com/swisstopo/topo-swisseobrowser); local `main`
  tracks `origin/main`. Never commit for or push to `upstream` (eu-cdse/copernicus-browser); its push URL
  is disabled on purpose. `davidoesch` (github.com/davidoesch/swisseobrowser) is the former repository.
- The upstream `.gitignore` ignores `.github`, so workflow and issue-form changes need `git add -f`.
- Issue forms: every form keeps the required `permalink` field; the Info tab fills it (and `layer`, `browser`) through the
  query of `issues/new?template=…`, so field ids must stay in sync with `feedbackLinks()` in the page.
- A push to `main` that changes the page or the README deploys GitHub Pages
  (https://swisstopo.github.io/topo-swisseobrowser/).

## Conventions

- **Coordinates are LV95 (EPSG:2056)** wherever users see them: the permalink centre
  (`center=2703331.08,1133878.99`, as on map.geo.admin.ch), compare entries, the pixel inspector, the
  area of interest in the link and the import dialog defaults. WGS84 is used only internally (Leaflet
  LatLng) and in GeoJSON downloads, which the GeoJSON standard requires.
- **Reflectance**: ρ = DN × 0.0001 − 0.1 for the swissEO S2-SR bands; negative ρ is clamped to 0 before
  an index is computed. Sentinel Hub custom scripts that work on DN expect 10000 × ρ, i.e.
  `max(0, DN − 1000)` of the swissEO bands.
- **New layers**: add them to `LAYERS`, give them a description in `COP_META`, put them into a
  configuration in `CONFIGS`, and add them unchecked to the README verification checklist (the deploy
  warns about missing entries) and to the README layer lists.
- **Layer verification**: only the user ticks a layer as verified in the README checklist.
- Data that is not in STAC (e.g. NDVIdiff v200 BETA) gets a fixed item list in `SOURCES`.

## Testing

- Syntax-check the inline script after every edit (extract it and run it through QuickJS or
  `node --check`).
- Serve the folder (`python -m http.server`) and open `swisseo-browser.html` in a browser. Headless
  Firefox screenshots work, but a view change after the page has loaded is not repainted: test views
  through permalinks instead.
- Check data reads against an independent rasterio / GDAL read of the same COG where possible.
