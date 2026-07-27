# Soft-Nexis-Internship-Task-1
# Spotify Streaming History Data Cleaning & Preprocessing

This repository contains an end-to-end **data cleaning and preprocessing pipeline** applied to a raw Spotify Streaming History dataset.

The main goal of this workflow is to transform raw streaming logs into a **standardized, clean, and analysis-ready dataset** by handling character encoding issues, missing values, duplicates, formatting inconsistencies, and data type conversions.

---

## Table of Contents

- [Features & Workflow Steps](#features--workflow-steps)
- [Dataset Transformations Schema](#dataset-transformations-schema)
- [Requirements & Installation](#requirements--installation)
- [Usage](#usage)
- [Summary of Results](#summary-of-results)

---

# Features & Workflow Steps

The workflow follows a structured data quality pipeline:

## 1. Imports & Data Encoding
- Loads raw Spotify streaming data using `latin1` encoding.
- Ensures correct parsing of special characters present in track, artist, and album names.

## 2. Deduplication
- Identifies and removes duplicate records.
- Ensures accurate streaming metrics and prevents repeated observations.

## 3. Column Management
- Removes unnecessary identifier columns such as `spotify_track_uri`.
- Renames columns for better readability and consistency.

## 4. Missing Value Handling
- Handles missing values in playback reason fields:
  - `start_reason`
  - `end_reason`
- Missing categorical values are replaced using the statistical mode.

## 5. Data Type Conversion & Feature Engineering
- Converts timestamps from string format into `datetime64`.
- Converts playback duration into numeric format.
- Creates a new feature:

```
minutes_played = milliseconds_played / (1000 * 60)
```

## 6. Format Standardization
- Removes unwanted leading and trailing whitespace from text fields.
- Converts platform names into lowercase format for consistency.

## 7. Export
- Saves the final cleaned dataset as:

```
cleaned_spotify_data.csv
```

---

# Dataset Transformations Schema

| Raw Column Name | Cleaned Column Name | Data Type | Transformation Applied |
|---|---|---|---|
| spotify_track_uri | Dropped | — | Removed as unnecessary identifier |
| ts | timestamp | datetime64[ns] | Parsed from string format (`%d-%m-%Y %H:%M`) |
| platform | platform | object (str) | Whitespace removed and converted to lowercase |
| ms_played | milliseconds_played | int64 | Converted into numeric format |
| N/A | minutes_played | float64 | New feature calculated from milliseconds played |
| track_name | track_name | object (str) | Leading/trailing whitespace removed |
| artist_name | artist_name | object (str) | Leading/trailing whitespace removed |
| album_name | album_name | object (str) | Leading/trailing whitespace removed |
| reason_start | start_reason | object (str) | Missing values replaced using mode |
| reason_end | end_reason | object (str) | Missing values replaced using mode |
| shuffle | is_shuffle | bool | Renamed for clarity |
| skipped | is_skipped | bool | Renamed for clarity |

---

# 💻 Requirements & Installation

## Requirements

Make sure Python 3.x is installed.

Install required dependencies:

```bash
pip install pandas
```

Additional libraries may be installed depending on the analysis environment.

---

# Usage

The pipeline can be executed in a Python environment or Jupyter Notebook.

## 1. Load Dataset

```python
import pandas as pd

df = pd.read_csv(
    "path/to/Spotify streaming history.csv",
    encoding="latin1"
)
```

---

## 2. Remove Duplicates

```python
df.drop_duplicates(inplace=True)
```

---

## 3. Drop Unnecessary Columns & Rename

```python
df.drop(
    columns=["spotify_track_uri"],
    errors="ignore",
    inplace=True
)

df.rename(
    columns={
        "ts": "timestamp",
        "ms_played": "milliseconds_played",
        "reason_start": "start_reason",
        "reason_end": "end_reason",
        "shuffle": "is_shuffle",
        "skipped": "is_skipped"
    },
    inplace=True
)
```

---

## 4. Handle Missing Values

```python
df["start_reason"].fillna(
    df["start_reason"].mode()[0],
    inplace=True
)

df["end_reason"].fillna(
    df["end_reason"].mode()[0],
    inplace=True
)
```

---

## 5. Convert Data Types & Create Feature

```python
df["timestamp"] = pd.to_datetime(
    df["timestamp"],
    format="%d-%m-%Y %H:%M"
)

df["milliseconds_played"] = pd.to_numeric(
    df["milliseconds_played"]
)

df["minutes_played"] = (
    df["milliseconds_played"] / (1000 * 60)
)
```

---

## 6. Text Formatting

```python
string_cols = [
    "platform",
    "track_name",
    "artist_name",
    "album_name",
    "start_reason",
    "end_reason"
]

for col in string_cols:
    df[col] = (
        df[col]
        .astype(str)
        .str.strip()
    )

df["platform"] = df["platform"].str.lower()
```

---

## 7. Save Cleaned Dataset

```python
df.to_csv(
    "cleaned_spotify_data.csv",
    index=False
)
```

---

# Summary of Results

| Metric | Result |
|---|---:|
| Initial Dataset Size | 149,860 rows × 11 columns |
| Duplicate Records Removed | 1,782 records |
| Final Dataset Size | 148,078 rows |
| Output File | `cleaned_spotify_data.csv` |

---

# Conclusion

This preprocessing pipeline successfully transformed raw Spotify streaming history data into a clean and structured dataset suitable for further analysis and visualization.

The workflow improved data quality by removing duplicates, handling missing values, standardizing formats, converting data types, and introducing meaningful derived features such as total minutes played.
