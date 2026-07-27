# Soft-Nexis-Internship-Task-1
Spotify Streaming History Data Cleaning & PreprocessingThis repository contains an end-to-end data cleaning and preprocessing pipeline applied to a raw Spotify Streaming History dataset. The main goal of this workflow is to transform raw streaming logs into a standardized, clean, and analysis-ready format by handling character encodings, missing values, duplicates, formatting inconsistencies, and data type conversions.  📋 Table of ContentsFeatures & Workflow StepsDataset Transformations SchemaRequirements & InstallationUsageSummary of Results🛠️ Features & Workflow StepsThe workflow follows a structured data quality pipeline:  Imports & Data Encoding: Loads raw streaming data using latin1 encoding to correctly parse special characters present in track, artist, and album names.  Deduplication: Identifies and eliminates duplicate records to ensure accurate streaming metrics.  Column Management: Drops non-essential identifier columns (e.g., spotify_track_uri) and renames remaining headers for clarity.  Missing Value Imputation: Imputes missing categorical playback reason fields (start_reason, end_reason) using the statistical mode.  Data Type Casting & Feature Engineering: Parses raw string timestamps into datetime64 objects, ensures correct numeric typing, and creates a calculated feature minutes_played.  Format Standardization: Strips unwanted leading and trailing whitespace across text fields and standardizes platform names to lowercase.  Export: Saves the final sanitized dataset to cleaned_spotify_data.csv.  📊 Dataset Transformations SchemaRaw Column NameCleaned Column NameData TypeTransformation Appliedspotify_track_uriDropped—Column removed as an unnecessary identifier  tstimestampdatetime64[ns]Parsed from string (%d-%m-%Y %H:%M)  platformplatformobject (str)Whitespace stripped and converted to lowercase  ms_playedmilliseconds_playedint64Explicitly cast to numeric  N/Aminutes_playedfloat64New Feature: Calculated as ms_played / (1000 * 60)  track_nametrack_nameobject (str)Leading/trailing whitespace stripped  artist_nameartist_nameobject (str)Leading/trailing whitespace stripped  album_namealbum_nameobject (str)Leading/trailing whitespace stripped  reason_startstart_reasonobject (str)Missing values imputed using mode; whitespace stripped  reason_endend_reasonobject (str)Missing values imputed using mode; whitespace stripped  shuffleis_shuffleboolRenamed for clarity  skippedis_skippedboolRenamed for clarity  💻 Requirements & InstallationTo run this pipeline locally, make sure you have Python 3.x installed along with the required dependencies:  Bashpip install pandas
🚀 UsageExecute the pipeline in a Python environment or Jupyter Notebook environment:  Pythonimport pandas as pd

# 1. Load Dataset
df = pd.read_csv("path/to/Spotify streaming history.csv", encoding='latin1')

# 2. Deduplicate
df.drop_duplicates(inplace=True)

# 3. Drop Unnecessary Columns & Rename
df.drop(columns=['spotify_track_uri'], errors='ignore', inplace=True)
df.rename(columns={
    'ts': 'timestamp',
    'ms_played': 'milliseconds_played',
    'reason_start': 'start_reason',
    'reason_end': 'end_reason',
    'shuffle': 'is_shuffle',
    'skipped': 'is_skipped'
}, inplace=True)

# 4. Handle Missing Values
df["start_reason"].fillna(df["start_reason"].mode()[0], inplace=True)
df["end_reason"].fillna(df["end_reason"].mode()[0], inplace=True)

# 5. Convert Data Types & Create Feature
df['timestamp'] = pd.to_datetime(df['timestamp'], format='%d-%m-%Y %H:%M')
df['milliseconds_played'] = pd.to_numeric(df['milliseconds_played'])
df['minutes_played'] = df['milliseconds_played'] / (1000 * 60)

# 6. Text Formatting
string_cols = ['platform', 'track_name', 'artist_name', 'album_name', 'start_reason', 'end_reason']
for col in string_cols:
    df[col] = df[col].astype(str).str.strip()
df['platform'] = df['platform'].str.lower()

# 7. Save Cleaned Dataset
df.to_csv("cleaned_spotify_data.csv", index=False)
📈 Summary of ResultsInitial Dataset Size: 149,860 rows × 11 columns  Duplicates Removed: 1,782 records  Final Output Rows: 148,078 records  Output File: cleaned_spotify_data.csv
