# Results

## `element_overlays/`

Static abundance overlays produced by `notebooks/05_render_element_overlays.ipynb`. Each file is
the lunar albedo basemap with every catalogue footprint stroked in a viridis colour keyed to that
element's share of total fitted flux.

Filenames follow `<element>_overlay_op<opacity>.png`, where the opacity suffix is the overlay
strength as a percentage — `op40` keeps the underlying geography legible, `op70` foregrounds the
data.

| Element | Files | What to look for |
|---|---|---|
| Mg | `mg_overlay_op40.png`, `mg_overlay_op70.png` | Elevated across Oceanus Procellarum and the nearside mare basins — ferromagnesian silicates |
| Fe | `fe_overlay_op40.png`, `fe_overlay_op70.png` | Nearly co-spatial with Mg; same mineralogy |
| Al | `al_overlay_op70.png` | The inverse pattern: peaks in the far-side highlands, depleted in the maria |
| Ca | `ca_overlay_op40.png`, `ca_overlay_op70.png` | Diffuse — calcium occurs in both plagioclase and pyroxene |
| Si | `si_overlay_op40.png`, `si_overlay_op70.png` | Close to featureless, consistent with its use as the normalisation reference |
| Na | `na_overlay_op40.png`, `na_overlay_op70.png` | Low abundance, weak structure |
| Ti | `ti_overlay_op40.png`, `ti_overlay_op70.png` | Confined to ilmenite-rich basalts; a subset of the mare regions |

**Aluminium has no 40% variant.** Re-run notebook 05 with `ELEMENT = "Al"` and `OPACITY = 0.4` to
generate it.

### Reading these correctly

- Each map is normalised over **its own** observed range, so colours are not comparable between two
  different elements. Only spatial structure within one figure carries meaning.
- Values are shares of total fitted flux, **not** weight percentages — no matrix or
  self-absorption correction is applied.
- Oxygen is excluded from the totals because its fit is the least reliable of the set. There is no
  oxygen overlay for the same reason.
- The woven texture is real. Footprints are stroked as outlines rather than filled, so overlapping
  orbital tracks stay individually visible and each map doubles as a coverage plot.

## `composition_viewer.html`

Not present here, and excluded by `.gitignore`. The rendered viewer inlines the entire dataset and
runs to tens of megabytes, past the point where versioning it is reasonable. Regenerate it with
`notebooks/04_render_composition_viewer.ipynb`, or host it as a release asset if you need to share
it.
