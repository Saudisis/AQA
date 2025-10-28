# 🌍 Air Quality Analysis (AQA) Library

Welcome to the **Air Quality Analysis (AQA) Library**, an integrated framework designed to automate the download, processing, and harmonization of environmental data from multiple sources.  
This project enables the combination of **ARPA Lombardia ground data**, **Sentinel-5P satellite observations**, and **ERA5 reanalysis** variables to generate daily, spatially consistent air quality indicators.

---

## 🧾 Data Requirements

To execute this notebook successfully, you will need access to the following datasets:

- **ARPA Lombardia (Ground Sensors)**  
  Hourly pollutant concentration measurements (NO₂, SO₂, CO, O₃, PM10, PM2.5, etc.), including `idsensore`, `lat`, `lng`, `provincia`, and timestamp columns.

- **Sentinel-5P (Satellite)**  
  Atmospheric column densities for selected pollutants (`NO2_column_number_density`, `O3_column_number_density`, etc.) accessed through the **Google Earth Engine API**.

- **ERA5 (Reanalysis)**  
  Meteorological parameters (temperature, pressure, wind speed, boundary layer height, radiation, precipitation, etc.) obtained from **CMCC** or **Copernicus Climate Data Store (CDS)**.

---

## ✨ Features

- **Automated download** of ERA5, Sentinel-5P, and ARPA datasets.  
- **Data cleaning** and normalization for all input sources.  
- **Temporal harmonization** to 12:00–15:00 mean values.  
- **Spatial merging** of satellite, reanalysis, and sensor data.  
- **Integrated summary table** for pollutant concentrations and weather variables.  
- **Visualization tools** for pollutant maps and correlation analysis.

---

## ⚙️ Installation

### Option 1 — Conda (Recommended)
```bash
conda env create -f environment.yml
conda activate AQA_DayRange
```

### Option 2 — Pip
```bash
python -m venv .venv
.venv\Scripts\activate   # Windows
source .venv/bin/activate  # macOS/Linux
pip install -r requirements.txt
```

---

## 📘 Main Notebook

All functions and workflow are implemented in the **Jupyter Notebook**:
```
AQA_DayRange.ipynb
```

This notebook contains:
- ARPA data access and cleaning.
- ERA5 and Sentinel-5P integration.
- Spatial grid generation and interpolation.
- Computation of current (`curr_`) and previous (`prev_`) daily means.
- Export of harmonized results to CSV and visualization of pollutant maps.

---

## 🧩 Workflow Overview

### 1. **ARPA Data Processing**
Loads ARPA Lombardia datasets using the API and organizes metadata for sensors, pollutants, and coordinates.

#### Example:
```python
meta = pd.read_csv(meta_url).dropna(subset=["idsensore", "lat", "lng"])
data = requests.get(data_url, headers=headers).json()
```

---

### 2. **ERA5 Variable Extraction**
Downloads meteorological variables (temperature, pressure, wind speed, radiation, BLH, etc.) using CMCC or CDS API and converts them to a harmonized format.

#### Example:
```python
import cdsapi
c = cdsapi.Client()
c.retrieve('reanalysis-era5-single-levels', {...}, 'era5_data.nc')
```

---

### 3. **Sentinel-5P Pollutant Integration**
Retrieves pollutant column density data (e.g., NO₂, O₃, CO, SO₂) via **Google Earth Engine** and scales values for consistency with ground units.

#### Example:
```python
pollutants = {
    "no2": {"collection": "COPERNICUS/S5P/OFFL/L3_NO2", "band": "NO2_column_number_density"}
}
```

---

### 4. **Spatial Harmonization and Merging**
Combines all datasets (ARPA, ERA5, Sentinel) through coordinate matching and averaging based on the AOI grid.

#### Example:
```python
summary = pd.merge(ground_df, sentinel_df, on=["Latitude", "Longitude"])
summary = pd.merge(summary, era5_df, on=["Latitude", "Longitude"])
```

---

### 5. **Visualization and Export**
Generates visual maps and CSV summaries for daily analysis.

#### Example:
```python
plt.scatter(summary["Longitude"], summary["Latitude"], c=summary["NO2"], cmap="inferno")
plt.title("NO₂ Concentration Map")
plt.show()

summary.to_csv("results/ARPA_ERA_SP5_<fecha>.csv", index=False)
```

---

## 🧪 Outputs

- **CSV results**: `results/ARPA_ERA_SP5-<date>.csv`  
- **Maps**: pollutant distributions and temporal evolution plots  
- **Summary tables**: harmonized pollutant and meteorological data  

---

## 📂 Repository Structure

```
AQA/
│
├── AQA_DayRange.ipynb       # Main analysis notebook
├── environment.yml           # Conda environment
├── requirements.txt          # pip dependencies
├── README.md                 # Project documentation
└── results/                  # Output CSVs and visualizations
```

---

## 🧠 Technologies Used
- **Python** (pandas, geopandas, numpy, matplotlib, xarray, requests)
- **Google Earth Engine API**
- **Copernicus CDS / CMCC ERA5**
- **ARPA Lombardia Open Data API**
- **GeoPandas + Matplotlib** for geospatial analysis

---

## 🧪 Testing
The data pipeline has been validated across multiple pollutants and provinces (Milano, Pavia, Brescia).  
Daily mean values were verified against ARPA public datasets for consistency.

---

## 📜 License
This project is licensed under the **MIT License**.  
See the [LICENSE](LICENSE) file for details.

---

## 👤 Author
**Saudisis**  
Politecnico di Milano — Environmental & Geoinformatics Research  
📧 [Contact via GitHub](https://github.com/Saudisis)

---

> Let the **AQA Library** guide you toward cleaner insights and harmonized air-quality data!
