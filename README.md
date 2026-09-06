# ECG-ID Database

[![License: ODC-By 1.0](https://img.shields.io/badge/License-ODC--By%201.0-green)](https://opendatacommons.org/licenses/by/1-0/)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-green?logo=creativecommons&logoColor=white)](https://creativecommons.org/licenses/by/4.0/)
[![Access: public](https://img.shields.io/badge/access-public-0e8a16.svg)](https://physionet.org/content/ecgiddb/1.0.0/)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-3776ab.svg)](https://www.python.org/downloads/)

Short **Lead I** ECG recordings from **90** volunteers for **biometric identification**, mirrored from PhysioNet open access (`ecgiddb` v1.0.0).

- **Dataset repository**: https://github.com/biometric-community/MIT-BIH-ECG-ID-Database
- **Catalog id (tbiom)**: `ecg-id`
- **Category**: `physio`
- **Access**: `public`
- **Upstream source**: https://physionet.org/content/ecgiddb/1.0.0/
- **DOI**: https://doi.org/10.13026/C2J01F
- **Original format**: PhysioNet WFDB (`.hea` / `.dat` / `.atr`) under `extracted/`
- **License (data)**: [Open Data Commons Attribution License v1.0](https://opendatacommons.org/licenses/by/1-0/) (PhysioNet)
- **License (helpers / docs)**: CC BY 4.0 (see [`LICENSE`](LICENSE))

## TL;DR

- **Task**: ECG biometric identification / subject recognition
- **Modality**: Single-lead ECG (Lead I), time series
- **Platform**: PhysioNet / WFDB records
- **Real/Synthetic**: Real volunteers
- **Subjects / records**: **90** persons / **310** recordings (verified under `extracted/`)
- **Demographics**: 44 male / 46 female; ages 13–75 (median 23)
- **Sampling**: **500 Hz**, 12-bit; **20 s** per record (`10000` samples)
- **Channels**: raw `ECG I` + `ECG I filtered`
- **Annotations**: per-record `.atr` — unaudited automated R- and T-wave peak marks (~10 beats/record)
- **Records per subject**: mostly 2–5; observed range **1–22** in this extract (see [Known Issues](#known-issues-and-caveats))
- **License**: data ODC-By 1.0; docs CC BY 4.0 (see `LICENSE`)
- **Citation**: See [Citation](#citation)

## Table of contents

- [Download](#download)
- [Dataset Structure](#dataset-structure)
- [Annotation Schema](#annotation-schema)
- [Stats and Splits](#stats-and-splits)
- [Quick Start](#quick-start)
  - [Using WFDB](#using-wfdb)
  - [Dependencies](#dependencies)
- [Datasheet (Data Card)](#datasheet-data-card)
- [Known Issues and Caveats](#known-issues-and-caveats)
- [License](#license)
- [Citation](#citation)
- [Contact](#contact)

## Download

- **This repository**: WFDB records under [`extracted/ecg-id-database-1.0.0/`](extracted/ecg-id-database-1.0.0/) (`Person_01` … `Person_90`).
- **Clone** (standalone):

```bash
git clone git@github.com:biometric-community/MIT-BIH-ECG-ID-Database.git
```

- **tbiom submodule / helper** (from monorepo root):

```bash
bash projects/datasets/scripts/download_ecg_id.sh
```

Or manually from PhysioNet:

```bash
wget -O projects/datasets/ecg-id/ecg-id-database-1.0.0.zip \
  https://physionet.org/content/ecgiddb/get-zip/1.0.0/
unzip -d projects/datasets/ecg-id/extracted \
  projects/datasets/ecg-id/ecg-id-database-1.0.0.zip
```

Project configs typically set `data.ecg_id_root` to `projects/datasets/ecg-id/extracted`.

## Dataset Structure

```text
ecg-id/
├── README.md
├── LICENSE
├── ecg-id-database-1.0.0.zip          # PhysioNet zip (optional if extracted)
└── extracted/
    └── ecg-id-database-1.0.0/
        ├── README                     # upstream dataset notes
        ├── RECORDS                    # 310 record paths
        ├── ANNOTATORS
        ├── Person_01/ … Person_90/
        │   ├── rec_N.hea              # header + age / sex / date comments
        │   ├── rec_N.dat              # two-channel signal samples
        │   └── rec_N.atr              # R/T peak annotations
        └── …
```

- **Splits**: Upstream does not ship fixed train/val/test lists; protocols are study-defined (often subject-disjoint enrollment vs probe).
- **Layout notes**: One folder per subject (`Person_XX`); record count varies by subject.

## Annotation Schema

### WFDB header (`Person_XX/rec_N.hea`)

- **Record line**: name, number of signals (`2`), sampling frequency (`500`), samples (`10000`)
- **Signal lines**: filename, format, gain, ADC resolution, baseline, first value, checksum, description
- **Comments**: `# Age`, `# Sex`, `# ECG date`

Example (`Person_01/rec_1.hea`):

```text
rec_1 2 500 10000
rec_1.dat 16 200 12 0 -17 17532 0 ECG I
rec_1.dat 16 200 12 0 -23 2004 0 ECG I filtered

# Age: 25
# Sex: male
# ECG date: 07.12.2004
```

### Signal channels (`*.dat`)

| Index | Name | Description |
|------:|------|-------------|
| 0 | `ECG I` | Raw Lead I |
| 1 | `ECG I filtered` | Filtered Lead I |

### Beat annotations (`*.atr`)

- **Annotator** (from `ANNOTATORS`): unaudited R- and T-wave peaks from an automated detector
- **Coverage**: one `.atr` per recording (**310** files)
- Read with WFDB `rdann(..., "atr")`

### Record index (`RECORDS`)

One relative path per line, e.g. `Person_01/rec_1`.

## Stats and Splits

Verified under `extracted/ecg-id-database-1.0.0/` (filesystem counts).

| Quantity | Count |
|----------|------:|
| Subjects (`Person_*`) | 90 |
| Recordings (`.hea` / `.dat` / `.atr`) | 310 |
| Male / female subjects | 44 / 46 |
| Age range (years) | 13–75 |
| Samples per record @ 500 Hz | 10000 (20.0 s) |

### Records per subject

| Records / subject | Subjects |
|------------------:|---------:|
| 1 | 1 |
| 2 | 48 |
| 3 | 16 |
| 4 | 5 |
| 5 | 13 |
| 6 | 2 |
| 7 | 1 |
| 8 | 1 |
| 11 | 1 |
| 20 | 1 (`Person_01`) |
| 22 | 1 (`Person_02`) |

Upstream prose often states “2 to 20” records per person; this extract also includes `Person_74` (1) and `Person_02` (22).

## Quick Start

### Using WFDB

```bash
pip install wfdb
```

```python
from pathlib import Path
import wfdb

root = Path("projects/datasets/ecg-id/extracted/ecg-id-database-1.0.0")
record_path = root / "Person_01" / "rec_1"

rec = wfdb.rdrecord(str(record_path))
ann = wfdb.rdann(str(record_path), "atr")

print(rec.sig_name, rec.fs, rec.p_signal.shape)  # ['ECG I', 'ECG I filtered'], 500, (10000, 2)
print(len(ann.sample), set(ann.symbol))
```

Within tbiom paper code, loaders resolve `data.ecg_id_root` (default `projects/datasets/ecg-id/extracted`) and read Lead I via WFDB.

### Dependencies

**Required** (to read signals):

- Python 3.10+
- [`wfdb`](https://pypi.org/project/wfdb/)

**Optional**:

- NumPy / SciPy for custom filtering or beat cropping

```bash
pip install wfdb numpy
```

## Datasheet (Data Card)

### Motivation

Provide a public, subject-labeled Lead I ECG corpus for research on **ECG biometrics** (human identification from heart signals), originally collected for Tatiana Lugovaya’s master’s thesis and hosted on PhysioNet.

### Composition

Each recording is a 20-second two-channel Lead I ECG (raw + filtered) with demographic comments and automated R/T peak annotations. Subjects are volunteers (students, colleagues, friends of the author).

### Collection Process

Signals were digitized at 500 Hz with 12-bit resolution. Multiple sessions per person may span a single day or up to about six months. Annotations are automated and unaudited.

### Preprocessing

This mirror does not reprocess signals. Channel 1 is the upstream filtered lead; channel 0 is raw. Downstream projects may apply their own band-pass / beat segmentation.

### Distribution

Hosted on PhysioNet under **ODC-By 1.0**. This folder is a local convenience mirror for tbiom experiments; redistributors must keep attribution and license terms.

### Maintenance

Download / extract via `projects/datasets/scripts/download_ecg_id.sh`. Layout follows PhysioNet `ecgiddb` v1.0.0. No train/val/test regeneration scripts ship in this directory.

## Known Issues and Caveats

- **Record-count range**: Upstream description says 2–20 records per person; this extract has **1** (`Person_74`) and **22** (`Person_02`).
- **Noisy raw channel**: Upstream notes substantial high- and low-frequency noise; prefer filtered channel or apply your own filtering for biometrics.
- **Unaudited annotations**: `.atr` peaks are automated and not clinically audited — unsuitable as a gold-standard diagnostic beat database.
- **No official split**: Identification protocols (enrollment/probe, same-day vs cross-session) must be defined by the experiment.
- **Not redistributable beyond license**: Keep ODC-By attribution when publishing derived work or mirrors.

## License

- **Signal / annotation files** (PhysioNet ECG-ID): **ODC-By 1.0** — https://opendatacommons.org/licenses/by/1-0/
- **Helpers and documentation** in this repository: **CC BY 4.0** — see [`LICENSE`](LICENSE)

Upstream page: https://physionet.org/content/ecgiddb/1.0.0/

## Citation

When using this resource, cite the original thesis and PhysioNet:

```bibtex
@mastersthesis{lugovaya2005ecgid,
  title        = {Biometric human identification based on electrocardiogram},
  author       = {Lugovaya, Tatiana S.},
  school       = {Electrotechnical University ``LETI''},
  address      = {Saint-Petersburg, Russian Federation},
  year         = {2005},
  month        = jun
}

@misc{ecgiddb,
  title        = {{ECG-ID} Database},
  author       = {Lugovaya, Tatiana S.},
  year         = {2014},
  howpublished = {PhysioNet},
  version      = {1.0.0},
  doi          = {10.13026/C2J01F},
  url          = {https://physionet.org/content/ecgiddb/1.0.0/},
  note         = {ODC-By 1.0}
}
```

Also include the current PhysioNet / Nature Health citation required on the dataset page when publishing.

## Contact

- **Dataset repository**: https://github.com/biometric-community/MIT-BIH-ECG-ID-Database
- **Upstream / PhysioNet**: https://physionet.org/content/ecgiddb/1.0.0/
- **Original contributor**: Tatiana Lugovaya (`ts.lugovaya@gmail.com`, as listed in the upstream `README`)
- **tbiom issues**: https://github.com/SJTU-YONGFU-RESEARCH-GRP/tbiom/issues
