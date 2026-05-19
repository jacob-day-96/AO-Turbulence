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
 <br>
 
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
 <br>

 ### 2. convertCSVToFits.ipynb
 ---

 Converts merged CSV dataset into FITS format.
 
 #### WORKFLOW:
 * Load daily CSV files
 * Merge into master dataset
 * Convert to FITS for faster I/O
 
 #### OUTPUT:
 * master.fits
 <br>

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
 
 Convert .mass → CSV
 Convert timestamps:
 UTC → HST
 <br>

 FUNCTION: **`read_mass_file(filename)`**
 <br>

 Extracts:
 * floating layer: heights + strengths
 * fixed layer: heights + strengths
 * grouped by timestamp
 <br>

 ### 4. plot_functions.ipynb
 ---
 
 Shared visualization utility library.
 
 FUNCTIONS:
 
 * **`doanes_rule_bins`**
 * **`cdf_even_odd`**
 * **`cdf_comparison`**
 * **`doane_hist`**
 * **`plot_median_per_bin_doane`**
 * **`month_year_boxplot`**
 
 PURPOSE:
 * Standardized statistical plotting
 * CDF comparisons
 * Doane binning
 * Seasonal/yearly analysis
 * Median-per-bin trends
 <br>

 ### 5. VISUALIZATION NOTEBOOKS
 ---

 i. **`box_plots.ipynb`**<br>
 → month/year boxplots
 <br>

 ii. **`CDFs.ipynb`**<br>
 → even/odd comparisons<br>
 → first/second half comparisons
 <br>

 iii. **`CFHT_histograms.ipynb`**<br>
 → histogram + CDF analysis using Doane rule
 <br>

 iv. **`comparison_graphs.ipynb`**<br>
 → fixed vs floating density plots<br>
 → KDE-based comparisons
 <br>

 v. **`correlation_matrix.ipynb`**<br>
 → correlation between MASS layers, DIMM, CFHT<br>
 → identifies unexpected correlations
 <br>

 vi. **`floating_layer_plots.ipynb`**<br>
 → MASS floating layer processing
 <br>

 vii. **`histogram_choice.ipynb`**<br>
 → compares Sturges, Rice, Doane<br>
 → selects Doane’s rule
 <br>

 viii. **`layer_strengths.ipynb`**<br>
 → histogram + CDF per MASS layer<br>
 → 500m → 16km<br>
 → stats: mean, median, mode, std
 <br>

 ix. **`median_per_bin_plots.ipynb`**<br>
 → median-per-bin using Doane bins<br>
 → trend extraction<br>
 <br>

 ### 6. forecast_model.ipynb
 ---
 
 LSTM forecasting model trained on full dataset.
 
 #### PURPOSE:
 Predict next-day atmospheric conditions
 
 #### INPUT:
 30+ parameters across multiple years
 
 #### PIPELINE:
 * preprocessing
 * scaling
 * sequence building
 * LSTM training
 * prediction
 
 #### CONCEPT:
 "Given past atmospheric behavior, predict tomorrow."
 <br>

 ## SUMMARY
 
 * Raw telescope + atmospheric data<br>
   → cleaned merged dataset<br>
   → statistical analysis<br>
   → visualization suite<br>
   → machine learning forecasting (LSTM)