# 5. Sources, third-party material and attribution

## Mission and instrument

**CLASS — Chandrayaan-2 Large Area Soft X-ray Spectrometer.** Instrument description, calibration
notes and data product definitions are published by ISRO alongside the archive. The channel-to-line
assignments used in this repository (`k_alphas` in notebooks 01 and 02) follow the instrument's
standard energy calibration.

**PRADAN** is ISRO's science data archive and the source of the Level-1 FITS frames this pipeline
consumes. Access terms are set by ISRO; consult the archive's own conditions before redistributing
any data obtained from it.

## Third-party material reproduced here

### Comparison maps

`figures/comparison_published_maps.png` reproduces published MgO, FeO and Al₂O₃ distribution maps
that are **not** a product of this repository. They are included solely so that the overlays
produced here can be compared against an independently derived result.

> Yang, C., Zhang, X., Bruzzone, L., Benediktsson, J. A., Ren, X., Zhao, H., Liang, Y., Liu, B.,
> Liu, D., Yang, B., Yin, M., Guan, R., Li, C., & Ouyang, Z. (2023).
> *Comprehensive mapping of lunar surface chemistry by adding Chang'e-5 samples with deep
> learning.* **Nature Communications**, 14.
> https://doi.org/10.1038/s41467-023-43358-0

Copyright in those figures remains with their authors and publisher. If you fork this repository
for anything beyond private study, check the article's licence terms before redistributing that
image, and remove it if in doubt — nothing in this pipeline depends on it.

The comparison is worth stating plainly, because it is the strongest external check available
here: that work inverts reflectance spectra using Chang'e-5 sample ground truth and deep learning,
while this pipeline decomposes X-ray fluorescence spectra with Gaussian fitting. Two methods with
almost nothing in common recover the same spatial structure — mafic enrichment across Procellarum,
the aluminium-rich highland ring, iron depletion through the highland terrane.

### Lunar basemap

The equirectangular albedo mosaic used as the background in `figures/`, in
`results/element_overlays/` and in the interactive viewer is **not included in this repository**.
Global lunar mosaics are published by NASA/ASU (LROC WAC) and by other mission teams, generally
into the public domain or under permissive terms, but the specific terms depend on which mosaic
you use. See [`../data/README.md`](../data/README.md) for what the pipeline expects of it.

## Software

The pipeline is built on the scientific Python stack. Each of these carries its own licence, all
permissive:

| Library | Role |
|---|---|
| `astropy` | FITS I/O |
| `numpy`, `scipy` | Arrays, `curve_fit`, `cKDTree` |
| `pandas` | Catalogue and grid tables |
| `plotly` | WebGL rendering of the interactive viewer |
| `matplotlib` | Colormaps, colour bars, figure export |
| `opencv-python`, `Pillow` | Basemap compositing |
| `rasterio` | Raster I/O |

No third-party source code is vendored into this repository. Everything above is installed from
PyPI through `requirements.txt`.

## Scientific background

The geochemical interpretation in the README — the mafic/feldspathic division between mare basalt
and highland anorthosite, iron and magnesium concentrated in ferromagnesian silicates, aluminium
concentrated in plagioclase feldspar — is standard lunar science and is not original to this work.
Any general text on lunar geology covers it; the *Lunar Sourcebook* (Heiken, Vaniman & French,
Cambridge University Press, 1991) remains the usual reference.

The use of silicon as a normalisation reference for orbital XRF abundance ratios is likewise
established practice in the field, adopted here rather than invented.

## Licence

No licence file is included. Without one, default copyright applies and others have no right to
reuse the code. If you intend this repository to be usable by anyone else, add a `LICENSE` file —
MIT or Apache-2.0 are the conventional choices for work of this kind — and note that doing so does
not extend to the third-party figure above, whose terms are its publisher's.
