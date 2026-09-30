# Project: Climate Data Processing for Forestry Applications
# Author: Tien Do
#Description: Basic processing pipeline for gridded netCDF climate data.
Read netCDF file (Templerature data)
Calculate annual spatial mean
Plot temperature trends
Export results (CSV & PNG)
------------------------------------------------------------

#Import Librarires
import xarray as xr
import pandas as pd
import numpy as np
import  matplotlib.pyplot as plt
import os

print("Libraries loaded successfully.")

==============================================================
#Step 1: Load NETCDF data
==============================================================
file_path = 'data/tas_monthly_sample.nc'

try:
    ds = xr.open_dataset(file.path)
    print(f"Dataset loaded. Dimensions: {ds.dims}")
except FileNotFoundError:
    print("File netCDF")

================================================================
#Step 2: Basic Statistical Processing
================================================================
Goal: Data transfer annual mean of the months and spatial mean for the study stie

2.1. Annual everage temperature
annual_temp = ds ['tas'].groupby('time.year').mean(dim='time')

2.2. Spatial everaging (aggregating latitudes/longtitudes into a single time series)
annual_spatial_mean = annual_temp.mean(dim=['lat', 'lon'])

#Convert from Kelvin to Celcius (if the original data is in Kelvin, common in CMIP6)
annual_spatial_mean_celsius = annual_spatial_mean - 273.15

print("Data processing completed.")

# ==========================================
# STEP 3: VISUALIZATION (DATA STORYTELLING)

#Plotting a temperature trend graph - Science communication skills
plt.figure(figsize=(10, 5))
annual_spatial_mean_celsius.plot.line(
    marker='o', 
    color='#d95f02', 
    linewidth=2, 
    markersize=5
)

#Academic-standard chart configuaration
plt.title('Annual Mean Surface Temperature Trend', fontsize=14, fontweight='bold')
plt.xlabel('Year', fontsize=12)
plt.ylabel('Temperature (°C)', fontsize=12)
plt.grid(True, linestyle='--', alpha=0.7)
plt.tight_layout()

#Display chart
plt.show()

# ==========================================
# STEP 4: EXPORT RESULTS
# ==========================================

os.makedirs('output', exist_ok=True)

# 4.1 Saving the Chart (Visualization)
plt.savefig('output/temperature_trend.png', dpi=300)

# 4.2 Export statistical data to a CSV file (easy to share with non-technical teams)
#Convert xarray DataArray to pandas DataFrame before exporting
df_results = annual_spatial_mean_celsius.to_dataframe(name='Mean_Temperature_C')
df_results.to_csv('output/processed_temperature_data.csv')
print("Results successfully exported to the 'output' folder.")
