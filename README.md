# Open-Source Synthetic Electrochemistry Datasets

This directory contains standardized, publication-ready copies of synthetic
chronoamperometry (CA), chronopotentiometry (CP), and cyclic voltammetry (CV)
datasets.

## Dataset Size

| Dataset | Standard Curves | Raw-Style Curves | Metadata Records | Added Samples |
| --- | ---: | ---: | ---: | ---: |
| CA | 40 CSV files | — | 40 | 10 |
| CP | 40 CSV files | — | 40 | 0 |
| CV | 405 CSV files | 405 TXT files | 405 | 210 |

The directory is approximately 38 MB. All `dataset_id` values and filenames are
unique within their respective datasets.

## Directory Structure

```text
Electrochemical_synthetic_datasets/
├── CA/
│   ├── curves/          # 40 standardized CA curves
│   └── metadata.jsonl
├── CP/
│   ├── curves/          # 40 standardized CP curves
│   └── metadata.jsonl
├── CV/
│   ├── curves/          # 405 standard CV curves
│   ├── raw/             # 405 noisy, CHI-style curves
│   └── metadata.jsonl
└── README.md
```

## Standardized Fields

### CA and CP

| Field | Description | Unit |
| --- | --- | --- |
| `time_s` | Time | s |
| `potential_V` | Potential | V |
| `current_A` | Current | A |

### Standard CV Curves

| Field | Description | Unit or Value |
| --- | --- | --- |
| `voltage_V` | Scanned potential | V |
| `current_mA` | Current | mA |
| `time_s` | Scan time | s |
| `scan_direction` | Scan direction | `1` for forward, `-1` for reverse |

The CV files under `raw/*.txt` use the fields `Potential/V, Current/A`. They
retain synthetic baseline drift, simulated power-line interference, or both.
Each TXT file contains exactly 1,000 numerical data points, and its scan-rate
header has been corrected to match the corresponding metadata.

## Metadata

Each line in a `metadata.jsonl` file describes one standard curve. Common fields
include:

- `dataset_id`: a unique identifier within the dataset;
- `file_name`: the curve path relative to its CA, CP, or CV directory;
- `source_file`: the original repository path, or `null` for newly generated samples;
- `source_group` or `physical_truth_status`: the sample source and label reliability;
- `n_points`, physical parameters, or operating conditions specific to the experiment type.

CV metadata also includes `raw_file`, `condition`, `raw_type`, `ground_truth`,
`empirical_features`, and `llm_base_params`. For the 15 legacy curves that had
no original metadata index, fields that could not be recovered are set to
`null`, while empirical extrema were recalculated from the standard curves.

## Reading the Data

```python
from pathlib import Path
import json
import pandas as pd

root = Path("Electrochemical_synthetic_datasets")

ca = pd.read_csv(next((root / "CA/curves").glob("*.csv")))
cp = pd.read_csv(next((root / "CP/curves").glob("*.csv")))
cv = pd.read_csv(next((root / "CV/curves").glob("*.csv")))

with (root / "CV/metadata.jsonl").open(encoding="utf-8") as handle:
    cv_metadata = [json.loads(line) for line in handle if line.strip()]
```

Some filenames contain spaces, parentheses, or the `µ` character. Use
`pathlib.Path` for path handling and always read metadata as UTF-8.

## Usage and Data-Splitting Recommendations

- All records are synthetic and must not be described as results from physical
  instrument experiments.
- CV data may be grouped by material or complete operating condition when
  splitting the dataset to evaluate generalization to unseen materials or
  conditions.
