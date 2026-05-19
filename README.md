## Overview

A collection of functions that analyze data files between 2009-2025 provided by the Differential Image Motion Monitor (DIMM), Multi-Aperture Scintillation Sensor (MASS), and Canada-France-Hawaii Telescope (CFHT). 

These functions include: 

  **`make_dat_file(date: str)`**
1. Takes a date input with the string format 'YYYYMMDD', searches for the DIMM and MASS URL that is associated with the date, compares the two files and matches the MASS entries with the closest DIMM entries by time, and creates a data frame with 6 columns of data: <br><br>
a.&nbsp;&nbsp;*midpoint_time* : The time between DIMM and MASS measurements (HST) <br>
b.&nbsp;&nbsp;*dimm_time* : The time the DIMM measurement was taken (HST) <br>
c.&nbsp;&nbsp;*mass_time* : The time the MASS measurement was taken (HST) <br>
d.&nbsp;&nbsp;*dimm_value* : The DIMM measurement taken at the specific DIMM time (arcseconds) <br>
e.&nbsp;&nbsp;*mass_value* : The MASS measurement taken at the specific MASS time (arcseconds) <br>
f.&nbsp;&nbsp;&nbsp;*time_difference* : The time difference between the DIMM and MASS time (seconds) <br>

2. Extracts the year (YYYY) from the original input, searches for the CFHT URL that is accociated with the year, compares the CFHT data with the 6-column data frame, matches the midpoint_time (HST) from the data frame to the closest CFHT measurement, and creates a new data frame with the original 6 columns of data plus 7 more from the CFHT data set. The new 7 columns are: <br><br>
a.&nbsp;&nbsp;*CFHT_datetime* : The closest time the CFHT measurement was taken (HST) <br>
b.&nbsp;&nbsp;*CFHT_windspeed* : The windspeed at that time (m/s) <br>
c.&nbsp;&nbsp;*CFHT_wind_direction* : The wind direction from East to West at that time (dec) <br> 
d.&nbsp;&nbsp;*CFHT_temp* : Temperature at that time (°C) <br>
e.&nbsp;&nbsp;*CFHT_humidity* : Humidity at that time (%) <br>
f.&nbsp;&nbsp;&nbsp;*CFHT_pressure* : Pressure at that time (Pa) <br>
g.&nbsp;&nbsp;*CFHT_time_diff* : The time difference between the midpoint_time and CFHT_datetime (seconds) <br>

3. Removes any rows with NaN values
4. Returns a 13-column data frame with the file name: *'{date}_result.csv'*

*Note* : Must use these imports to run function: <br>
```
import pandas as pd
import numpy as np
from datetime import datetime, timedelta
import requests
```

  **`test`**
 1. Run though each date per year to gather data files (skip the first two - these are test files) (Note: Start and end dates should be datetime objects)
 2. Combine '{date}_results.csv' to one data file labeled '{year}.csv'
 3. Load the original CSV into a data frame
 4. Calculate the new column 'seeing_ground_layer' assuming Kolmogorov turbulence spectra
 5. Save the updated data frame to a new CSV file




 ## OVERVIEW + SUMMARY
 
 
 This project is a data processing and visualization pipeline for atmospheric turbulence analysis using datasets from:
 
 * Differential Image Motion Monitor (DIMM)
 * Multi-Aperture Scintillation Sensor (MASS)
 * Canada-France-Hawaii Telescope (CFHT)
 
 The dataset spans ~2009–2025 and includes atmospheric seeing, turbulence layers, and meteorological measurements.
 
 ### Pipeline stages:
 1. Data acquisition and merging (DIMM + MASS + CFHT)
 2. CSV → FITS conversion
 3. Time-aligned dataset construction
 4. Feature engineering
 5. Statistical visualization
 6. LSTM forecasting model
 
 
 ### 1. make_csv_files.ipynb  
 ---
 
 Main data engineering pipeline that constructs the master dataset.

 FUNCTION: **`make_csv_files(year: string)`**
 
 Takes a year in "YYYY" format (string) and generates daily datasets.
 
 #### WORKFLOW
 
 1. Load CFHT yearly weather data
 2. Iterate over all dates in year
 3. Load DIMM + MASS daily data from URLs
 
 #### Time alignment:

 * Match DIMM ↔ MASS (nearest timestamp, tolerance ~900s)
 * Match result ↔ CFHT (nearest timestamp)

 #### FEATURES

 DIMM:
 * dimm_val

 MASS layers:
 * mass_val
 * 500m
 * 1km
 * 2km
 * 4km
 * 8km
 * 16km
 
 CFHT:
 * wind_speed (m/s)
 * wind_direction (°)
 * temperature (°C)
 * humidity (%)
 * pressure (Pa)
 
 Time differences:
 * dimm_mass_dt (s)
 * dimm_cfht_dt (s)
 
 Derived:
 * ground_layer turbulence (Kolmogorov)
 
 #### OUTPUT
 
 * {date}_result.csv
 *

 ### 2. convertCSVToFits.ipynb
 ---

 Converts merged CSV dataset into FITS format.
 
 #### WORKFLOW:
 * Load daily CSV files
 * Merge into master dataset
 * Convert to FITS for faster I/O
 
 #### OUTPUT:
 * master.fits
 *
 
 ### 3. mass_file_to_csv.ipynb
 ---
 
 Converts raw MASS .mass files into CSV format.
 
 
 #### LINUX DATA DOWNLOAD
 

 Example (using 2018):
 ```
 curl -s https://www.cfht.hawaii.edu/ObsInfo/Weather/mkam/MASSdata/out/ \
 | grep -o '18[^"]*\.mass\.bz2' \
 | while read f; do
     curl -O "https://www.cfht.hawaii.edu/ObsInfo/Weather/mkam/MASSdata/out/$f"
   done
 
 cat 18*.mass.bz2 > ../2018_floating.mass.bz2
 ```
 
 #### PROCESSING
 
 * Convert .mass → CSV
 * Convert timestamps:
 * UTC → HST
 *
 * FUNCTION: read_mass_file(filename)
 *
 * Extracts:
 * - floating layer: heights + strengths
 * - fixed layer: heights + strengths
 * - grouped by timestamp
 */

/**
 * ============================================================
 * 4. plot_functions.ipynb
 * ============================================================
 *
 * Shared visualization utility library.
 *
 * FUNCTIONS:
 *
 * - doanes_rule_bins
 * - cdf_even_odd
 * - cdf_comparison
 * - doane_hist
 * - plot_median_per_bin_doane
 * - month_year_boxplot
 *
 * PURPOSE:
 * - Standardized statistical plotting
 * - CDF comparisons
 * - Doane binning
 * - Seasonal/yearly analysis
 * - Median-per-bin trends
 */

/**
 * ============================================================
 * 5. VISUALIZATION NOTEBOOKS
 * ============================================================
 */

/**
 * box_plots.ipynb
 * - month/year boxplots
 */

/**
 * CDFs.ipynb
 * - even/odd comparisons
 * - first/second half comparisons
 */

/**
 * CFHT_histograms.ipynb
 * - histogram + CDF analysis using Doane rule
 */

/**
 * comparison_graphs.ipynb
 * - fixed vs floating density plots
 * - KDE-based comparisons
 */

/**
 * correlation_matrix.ipynb
 * - correlation between MASS layers, DIMM, CFHT
 * - identifies unexpected correlations
 */

/**
 * floating_layer_plots.ipynb
 * - MASS floating layer processing
 */

/**
 * histogram_choice.ipynb
 * - compares Sturges, Rice, Doane
 * - selects Doane’s rule
 */

/**
 * layer_strengths.ipynb
 * - histogram + CDF per MASS layer
 * - 500m → 16km
 * - stats: mean, median, mode, std
 */

/**
 * median_per_bin_plots.ipynb
 * - median-per-bin using Doane bins
 * - trend extraction
 */

/**
 * ============================================================
 * 6. forecast_model.ipynb
 * ============================================================
 *
 * LSTM forecasting model trained on full dataset.
 *
 * PURPOSE:
 * - Predict next-day atmospheric conditions
 *
 * INPUT:
 * - 30+ parameters across multiple years
 *
 * PIPELINE:
 * - preprocessing
 * - scaling
 * - sequence building
 * - LSTM training
 * - prediction
 *
 * CONCEPT:
 * "Given past atmospheric behavior, predict tomorrow."
 */

/**
 * ============================================================
 * SUMMARY
 * ============================================================
 *
 * Raw telescope + atmospheric data
 * → cleaned merged dataset
 * → statistical analysis
 * → visualization suite
 * → machine learning forecasting (LSTM)
 */