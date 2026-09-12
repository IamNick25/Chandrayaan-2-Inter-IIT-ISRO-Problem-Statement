# 1. The instrument and the archive

## What CLASS measures

CLASS is a soft X-ray spectrometer, and it is useful to be precise about what that means for the
data it produces. It is not a camera. It does not resolve a scene. What it produces, roughly
twice a second, is a histogram: how many photons arrived, sorted by how energetic they were.

The detection chain is a swept-charge device. An incoming X-ray photon ionises the semiconductor
bulk, liberating a number of electron-hole pairs proportional to the photon's energy. That charge
packet is swept out and read as a voltage pulse, whose amplitude is digitised into one of 2048
channels. Channel number is therefore a proxy for photon energy, and the height of the histogram
at a given channel is a photon count.

The physical process that makes this useful for mapping is X-ray fluorescence. When a solar flare
raises the incident X-ray flux at the lunar surface, atoms in the uppermost tens of micrometres of
regolith are ionised in their inner shells. Relaxation follows immediately, and the emitted photon
carries an energy fixed by the element's shell structure. Those emission energies are known
constants, so each element produces a peak at a predictable channel, and the photon count in that
peak scales with how much of the element is present in the illuminated patch.

The consequence that shapes this entire project is that **fluorescence requires a flare**. Outside
flare conditions the lines are indistinguishable from the detector's own noise floor. The usable
archive is therefore a small, unevenly distributed subset of total observing time, which is why
map coverage is patchy and why some regions are sampled dozens of times while others are barely
sampled at all.

## Line positions

The pipeline assumes the following channel assignments, and they are hard-coded as `k_alphas` in
notebooks 01 and 02:

| Line | Channel |
|---|---|
| O K-alpha | 38 |
| Fe L | 53 |
| Na K-alpha | 77 |
| Mg K-alpha | 92 |
| Al K-alpha | 110 |
| Si K-alpha | 128 |
| Ca K-alpha | 273 |
| Ti K-alpha | 334 |
| Mn K-alpha | 436 |
| Fe K-alpha | 474 |

Iron appears twice. Its L-shell feature near channel 53 and its K-shell line near channel 474 are
fitted independently and their areas summed into a single `Fe_area` in the catalogue.

Channels above roughly 800 contain nothing of interest for these elements and are discarded at the
start of the triage step, which keeps the fitting problem an order of magnitude smaller than the
full 2048-channel frame.

## Frame layout

Each FITS file holds one exposure. The pipeline reads three things from extension 1:

| Field | Location | Meaning |
|---|---|---|
| `COUNTS` | Data column | 2048-element photon count array |
| `V0_LAT` … `V3_LON` | Header keywords | Latitude and longitude of the four corners of the observed footprint |
| `MID_UTC` | Header keyword | Mid-exposure timestamp, `%Y-%m-%dT%H:%M:%S.%f` |

The footprint is a quadrilateral because the detector's field of view projects onto a curved
surface along a moving ground track; it is not axis-aligned and consecutive footprints do not tile
cleanly. Reconciling that geometry with a rectangular map is the problem notebook 03 exists to
solve.

Nothing else from the header is used. No solar-flux record, pointing solution or calibration
product is consulted — the amplitude gates described in the next document are the only thing
separating usable frames from quiet ones.

## Archive layout expected by the pipeline

Notebooks 01 and 02 walk a directory tree and expect one subdirectory per month, named `cla 2`
through `cla 12`:

```
data/raw/class_fits/
├── cla 2/
│   └── *.fits
├── cla 3/
│   └── *.fits
└── ...
```

Traversal is sorted at both the directory and file level, and this matters more than it looks.
Co-addition sums eight *consecutive* files on the assumption that consecutive on disk means
consecutive along the ground track. Reorder or interleave the archive and the batches will
straddle unrelated patches of surface, producing rows whose averaged footprint corners describe
nowhere in particular.

Adjust `base_dir` — or set `CLASS_FITS_ROOT` — if the archive is laid out differently, and adjust
the loop bounds if the naming convention differs.

Source and download instructions are in [`../data/README.md`](../data/README.md).
