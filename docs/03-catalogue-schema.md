# 3. Catalogue schema

Notebook 02 writes one CSV per source directory. Every row is one co-added batch of eight frames —
one patch of lunar surface, at one moment, with its spectrum already decomposed.

## Columns

### Identity and geometry

| Column | Type | Description |
|---|---|---|
| `Date-Time` | string | Mean acquisition time of the batch, `%Y-%m-%dT%H:%M:%S` |
| `V0_lat`, `V0_lon` | float | First footprint corner, degrees |
| `V1_lat`, `V1_lon` | float | Second corner |
| `V2_lat`, `V2_lon` | float | Third corner |
| `V3_lat`, `V3_lon` | float | Fourth corner |

The four corners are averaged across the eight frames in the batch. Latitude runs −90 to +90 and
longitude −180 to +180. The quadrilateral is not axis-aligned and successive rows do not tile
cleanly — see [`04-gridding-and-rendering.md`](04-gridding-and-rendering.md).

### Line areas

| Column | Description |
|---|---|
| `O_area` | Oxygen K-alpha |
| `Fe_area` | Iron — **L-shell and K-shell areas summed** |
| `Na_area` | Sodium K-alpha |
| `Mg_area` | Magnesium K-alpha |
| `Al_area` | Aluminium K-alpha |
| `Si_area` | Silicon K-alpha — the normalisation reference |
| `Ca_area` | Calcium K-alpha |
| `Ti_area` | Titanium K-alpha |
| `Mn_area` | Manganese K-alpha |

Each value is the closed-form integral of that line's fitted Gaussian, in counts·channel. These
are **relative abundance proxies, not weight percentages**: no matrix correction, self-absorption
correction or radiative-transfer modelling is applied, and absolute magnitude depends on the
strength of the driving flare.

`NaN` means the fit did not converge, or converged on a non-positive area. Nothing is imputed.

### Ratio uncertainties

| Column | Description |
|---|---|
| `O/Si_uncertainty` | Spectral-overlap uncertainty on O/Si |
| `Fe/Si_uncertainty` | … on Fe/Si |
| `Na/Si_uncertainty` | … on Na/Si |
| `Mg/Si_uncertainty` | … on Mg/Si |
| `Al/Si_uncertainty` | … on Al/Si |
| `Ca/Si_uncertainty` | … on Ca/Si |
| `Ti/Si_uncertainty` | … on Ti/Si |
| `Mn/Si_uncertainty` | … on Mn/Si |

Absolute (not fractional) uncertainties on the corresponding ratios, floored at
`(A_E / A_Si) · 10⁻⁵`. Derivation in [`02-spectral-pipeline.md`](02-spectral-pipeline.md#25-areas-ratios-and-uncertainty).

**These quantify spectral confusion between neighbouring lines only.** Counting statistics,
calibration error and instrumental systematics are not included.

There is no silicon self-uncertainty column, since Si/Si is not a meaningful quantity here.

## Deriving ratios

The catalogue stores areas, not ratios. Consumers compute them:

```python
ratio = df["Mg_area"] / df["Si_area"]
```

Notebook 03 does this while regridding; notebook 05 uses a different normalisation entirely
(share of total fitted flux across all elements, oxygen excluded) because it is drawing relative
spatial contrast rather than a quantitative ratio.

## A spelling inconsistency to watch for

The catalogue writes `*_uncertainty`. The regridded grid table produced by notebook 03 writes
`*_uncertainity` — a typo that is load-bearing, because downstream cells select columns by
matching that exact substring. Both spellings are preserved here deliberately: correcting one
without the other silently produces empty column selections rather than an error.

## Scale

Around 315,000 rows for the co-added 2022 archive. File size runs to a few hundred megabytes,
which is why the catalogue is not versioned in this repository — see
[`../data/README.md`](../data/README.md).
