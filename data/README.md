# Data directory

This directory ships empty. Nothing in it is versioned, and the `.gitignore` at the repository root
keeps it that way.

The reason is size. The catalogue runs to hundreds of megabytes, the regridded grid table to around
a gigabyte before compression, and the rendered viewer to tens of megabytes on its own. GitHub
warns above 50 MB and rejects any single file above 100 MB, so versioning these would make the
repository unusable to clone and would still not survive a push. Everything here is either
downloadable from its source or regenerable from the notebooks.

## Expected layout

```
data/
├── raw/
│   └── class_fits/          CLASS Level-1 FITS frames
│       ├── cla 2/
│       ├── cla 3/
│       └── ...
├── interim/
│   ├── coadded_catalogue.csv    Notebook 02 output, concatenated
│   └── grid_ratios.csv          Notebook 03 output
└── basemap/
    └── lunar_albedo_equirect.png
```

Every path is configurable, so nothing forces this layout:

| Variable | Default | Used by |
|---|---|---|
| `CLASS_FITS_ROOT` | `../data/raw/class_fits` | Notebooks 01, 02 |
| `CATALOGUE_CSV` | `../data/interim/coadded_catalogue.csv` | Notebooks 03, 05 |
| `GRID_CSV` | `../data/interim/grid_ratios.csv` | Notebooks 03, 04 |
| `LUNAR_BASEMAP` | `../data/basemap/lunar_albedo_equirect.png` | Notebooks 04, 05 |
| `VIEWER_HTML` | `../results/composition_viewer.html` | Notebook 04 |
| `OVERLAY_DIR` | `../results/element_overlays` | Notebook 05 |

## 1. Source data — CLASS FITS frames

Obtain Level-1 CLASS spectral products from ISRO's PRADAN science data archive. The pipeline was
developed against observations spanning 2021–2024 and expects one subdirectory per month, named
`cla 2` … `cla 12`; adjust `base_dir` and the loop bounds in notebook 02 if your layout differs.

Each frame must carry, in extension 1, a `COUNTS` column of 2048 values plus `V0_LAT`–`V3_LON` and
`MID_UTC` header keywords. See [`../docs/01-instrument-and-data.md`](../docs/01-instrument-and-data.md).

Sorted traversal matters: co-addition assumes eight consecutive files on disk are consecutive along
the ground track. Do not interleave or rename the archive.

## 2. Basemap

Any equirectangular global lunar mosaic works, provided it spans −180° to +180° longitude and −90°
to +90° latitude with no cropping or offset. The projection code reads the image's dimensions from
the file, so resolution is free to vary; the figures in this repository were produced against a
7500 × 3750 albedo mosaic.

Higher resolution costs nothing in the viewer, where the basemap is a single background image, but
notebook 05 rasterises every footprint outline into it, so very large mosaics make that notebook
slow and memory-hungry.

Global mosaics are published by NASA/ASU from LROC Wide Angle Camera data, among other sources.
Check the terms attached to whichever you use.

## 3. Intermediates

Both are regenerable, and neither should be treated as an archival product:

| File | Produced by | Approximate size |
|---|---|---|
| `coadded_catalogue.csv` | Notebook 02, concatenated across directories | a few hundred MB |
| `grid_ratios.csv` | Notebook 03 | ~1 GB uncompressed |

Notebook 02 writes one CSV per source directory. Concatenate them before running notebook 03:

```bash
python - <<'PY'
import glob, pandas as pd
parts = sorted(glob.glob("coadded_catalogue_*.csv"))
pd.concat((pd.read_csv(p) for p in parts), ignore_index=True) \
  .to_csv("data/interim/coadded_catalogue.csv", index=False)
PY
```

Notebook 03 materialises the full grid before filtering and comfortably wants 16 GB of RAM. Below
that, slice the catalogue, regrid each slice, write the partials as `grid_part_*.csv` into
`data/interim/`, and use the concatenation cell provided in that notebook.

## 4. Rendered outputs

`results/composition_viewer.html` is a self-contained page with the entire dataset inlined — tens
of megabytes, and also excluded from version control. Regenerate it with notebook 04 rather than
committing it; if you need to share it, host it as a release asset or on static hosting instead.

The static overlay PNGs are small and **are** versioned, under `results/element_overlays/`.

## If you want any of this in Git anyway

Use Git LFS rather than committing directly:

```bash
git lfs install
git lfs track "*.csv" "data/**" "results/*.html"
git add .gitattributes
```

Note that LFS storage and bandwidth quotas apply, and that the 100 MB per-file limit does not — but
a fresh clone will then pull gigabytes.
