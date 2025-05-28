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
