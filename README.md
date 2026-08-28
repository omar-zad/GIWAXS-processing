# Reproducible GIWAXS processing workflow

This repository contains a self-contained Jupyter notebook for converting I07 Pilatus GIWAXS detector stacks into reciprocal-space images, directional radial profiles and intensity maps against actual elapsed scan time.

The main workflow is [`GIWAXS_reproducible_processing.ipynb`](GIWAXS_reproducible_processing.ipynb).

## Acknowledgement and provenance

This workflow is adapted from `i07_data_processing_clean.ipynb` and uses the accompanying `environment.yml`, both supplied by Dr Daniel Toolan. These original materials provided the core I07 processing framework and software environment, including the Python/pyFAI/pyGIX setup, PONI and detector-mask loading, Pilatus HDF5 access, reciprocal-space transformation and frame-by-frame processing pattern. This contribution is gratefully acknowledged.

The present project reorganised and extended that framework. Project-specific additions include:

- a restart-and-run-all notebook with one documented settings cell;
- input and detector/mask compatibility checks;
- corrected positive-Qr orientation;
- explicit FULL/FR, IP and OOP sectors;
- mean-intensity radial binning with invalid bins retained as missing values;
- consistent scan-level colour scales and black masked regions;
- representative time-labelled line cuts and paired IP/OOP plots;
- Q-versus-time maps using actual NeXus per-frame timestamps;
- memory-safe multi-scan processing and machine-readable processing manifests.

The reorganisation and project-specific implementation were developed with assistance from OpenAI Codex.

## Inputs

For each scan, the notebook expects:

- `pilatus2-<scan>.hdf5` containing `/entry/data/data`;
- `i07-<scan>.nxs` containing per-frame scan timing;
- a pyFAI `.poni` detector-geometry calibration;
- a compatible EDF detector mask.

Raw beamline data and sample results are not included in this repository. Users must supply authorised data and edit the paths in the notebook settings cell.

## Processing defaults

- incident angle: 0.12°;
- Q range: 0.01–2.50 Å⁻¹;
- reciprocal map: 500 × 500 bins;
- radial profiles: 400 bins;
- FULL/FR sector: 0–90°;
- IP sector: 0–20°;
- OOP sector: 70–90°;
- chi definition: `atan2(Qz, Qr)`;
- solid-angle correction enabled;
- polarization factor recorded as 1.0;
- no background subtraction or monitor/exposure normalization.

The settings recorded in each scan's generated JSON manifest, rather than this README, are authoritative for a particular processed dataset.

## Running

1. Open `GIWAXS_reproducible_processing.ipynb` using the same Python environment used for the original I07 processing workflow.
2. Edit only the documented settings cell.
3. Restart the kernel.
4. Select **Run All**.
5. Confirm that the input-audit cell succeeds before the long processing stage begins.

## Outputs

Each scan receives a deterministic output folder containing:

- one reciprocal-space PNG for every selected detector frame;
- linear-intensity FULL/FR, IP and OOP profile CSV files;
- representative radial line plots;
- paired IP/OOP plots;
- FULL/FR, IP and OOP Q-versus-elapsed-time maps;
- frame/time/temperature metadata;
- a JSON record of source files, calibration, settings and package versions.
