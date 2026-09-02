# Reproducible GIWAXS processing workflow

This repository contains a self-contained Jupyter notebook for converting I07 Pilatus GIWAXS detector stacks into reciprocal-space images, directional radial profiles and intensity maps against actual elapsed scan time.

The main workflow is [`GIWAXS_reproducible_processing.ipynb`](GIWAXS_reproducible_processing.ipynb).

## Acknowledgement and provenance

This workflow is adapted from `i07_data_processing_clean.ipynb`, supplied by Dr Daniel Toolan. The accompanying `environment.yml` was also supplied by Dr Daniel Toolan and has subsequently been extended for this project, including the addition of `openpyxl` for Excel metadata export. These original materials provided the core I07 processing framework and software environment, including the Python/pyFAI/pyGIX setup, PONI and detector-mask loading, Pilatus HDF5 access, reciprocal-space transformation and frame-by-frame processing pattern. This contribution is gratefully acknowledged.

The present project reorganised and extended that framework. Project-specific additions include:

- a restart-and-run-all notebook with one documented settings cell;
- input and detector/mask compatibility checks;
- corrected positive-Qr orientation;
- explicit FULL/FR, IP and OOP sectors;
- mean-intensity radial binning with invalid bins retained as missing values;
- consistent scan-level colour scales and black masked regions;
- representative time-labelled line cuts and paired IP/OOP plots;
- Q-versus-time maps using actual NeXus per-frame timestamps;
- automatic linear-intensity Q-versus-time CSV matrices for FULL/FR, IP and OOP;
- automatic per-frame reciprocal-space CSV matrices with masked regions retained as missing values;
- CSV/Excel frame–time–temperature tables and temperature trajectory plots;
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
- a `Metadata` folder containing frame/time/temperature CSV and Excel tables;
- temperature-versus-time, temperature-versus-frame and dual-axis temperature plots;
- a `Numeric_Exports/Q_Time` folder containing raw linear FULL/FR, IP and OOP
  matrices with Q rows and actual elapsed-time columns;
- a `Numeric_Exports/Reciprocal_Space` folder containing linear reciprocal-space
  matrices with Qz rows, Qr columns and blank cells for masked or uncovered bins;
- a JSON record of source files, calibration, settings and package versions.

Temperature values are taken directly from `entry/instrument/temp3/value`; they are not smoothed or interpolated. If a scan has no finite temperature values, its metadata tables are still written and only the three temperature plots are skipped.

The numerical CSV exports are generated for every scan listed in `SCAN_NUMBERS` during the normal processing run; no scan-specific export cells are required. Q-time values are azimuthal mean intensities on a linear scale. Reciprocal-space values are also linear, after the configured geometry transformation, detector mask and corrections. Neither export contains the `log10` display transformation.

Every-frame reciprocal-space CSV export is intentionally controlled by `EXPORT_RECIPROCAL_CSV` in the settings cell because text matrices are large. With the eight kinetic scans currently listed in the notebook, these files require approximately 3.1 GB in addition to the plotted images and other outputs.
