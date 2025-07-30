
# Wildfire Prediction Project

## Overview
Wildfires are an escalating threat across the United States, driven by rising temperatures, prolonged dry periods, and shifting weather patterns linked to climate change. Our team developed a **machine learning model** to predict wildfire occurrences at the U.S. county level, enabling policymakers to better anticipate and mitigate risks.

This project showcases months of **data engineering, exploratory analysis, and modeling** on massive real-world datasets. We integrated **historical fire data, climate variables, and land cover information** into a unified pipeline to predict where wildfires are most likely to occur.

> **Team Members:** Charles Sirey, Henry Dare, Nolan Pittman, Adam Murphy (POC)

---

## Data Sources and Visualizations
We worked with several large-scale datasets:

### 1️⃣ Fire History Data ([NIFC](https://data-nifc.opendata.arcgis.com/search?tags=historic_wildlandfire_opendata%2CCategory))
- 900k+ wildfire incidents spanning 40 years.
- Attributes: fire size, location, date.
- After filtering fires under 1 acre, the dataset contained ~200,000 incidents.

![Fire Size Before Log](Images/Fire_before.png)
*Figure 1: Histogram of fire sizes before applying a log scale transformation.*

![Fire Size After Log](Images/Fire-after.png)
*Figure 2: Histogram of fire sizes after double log scaling for clearer visualization.*

---

### 2️⃣ Weather & Climate Data ([PRISM Climate Group](https://prism.oregonstate.edu/))
- High-resolution (2-mile grid) monthly temperature and precipitation data.
- Features: min, max, mean temperature, vapor pressure deficit, dew point.

![Mean Temperature](Images/meantemp.png)
*Figure 3: Mean temperature distribution for July 2023 across U.S. counties.*

![Precipitation](Images/precipitation.png)
*Figure 4: Monthly precipitation levels across U.S. counties in March 2023.*

---

### 3️⃣ Land Cover Data ([USGS NLCD](https://www.mrlc.gov/data/national-land-cover-database-nlcd-2021))
- 27GB zipped, 227GB extracted, `.img` and `.ige` files.
- Provides percentage breakdown of land cover types (forests, grasslands, urban, water) per county.
- Used linear interpolation to fill missing yearly data.

![Land Cover](Images/Land-cover.png)
*Figure 5: Land cover types across the U.S., visualized using updated NLCD classification colors.*

---

### 4️⃣ County Boundaries ([US Census TIGER/Line](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.2023.html#list-tab-790442341))
- Used to merge spatial datasets and assign data to specific counties.

Due to size constraints, raw data is not stored here. Use the links above to access the original datasets.

---

## Repository Structure

```

Wildfire Predictor/ │ ├── README.md ├── Images/                    # All plots and visualizations ├── Notebooks/ │   ├── 01\_data\_cleaning/ │   ├── 02\_data\_merging/ │   ├── 03\_modeling/ │   └── 04\_exploration/ └── Reports/ ├── Proposal.md ├── Final\_report.md └── Checkpoints/

```

---

## ETL Process
### Cleaning
- Parsed `.bil` weather files into time-series summaries.
- Converted `.img` land cover files into proportion-based features per county.
- Filtered out minor fires (<1 acre) to focus on meaningful incidents.
- Imputed missing data and interpolated land cover values between available years.

### Merging
- Joined fire, weather, and land cover datasets using county IDs and shapefiles.
- Created a **binary target variable** (1 = fire occurred, 0 = no fire) for each county-month.
- Final merged dataset: ~1,035,000 rows, 43 features.

### Feature Engineering
- 3-month rolling averages for precipitation and temperature.
- Seasonal anomalies (deviations from long-term norms).
- Threshold flags for high temperature and high vapor pressure deficit.
- Lag features to capture delayed environmental effects.

---

## Modeling Approach
We trained a **Random Forest Classifier** for fire occurrence prediction:
- **Feature Selection:** Recursive Feature Elimination (RFE) → top 19 features.
- **Class Balancing:** SMOTE oversampling + Tomek Links cleaning.
- **Results:**
  - F1: **0.625**
  - Accuracy: **0.970**
  - Precision: **0.611**
  - Recall: **0.640**
  - ROC AUC: **0.962**

![RFE Feature Selection](Images/RFE_feature2.png)
![Feature Importance](Images/feature_importance.png)

A Random Forest Regressor for fire size prediction performed poorly (R² = 0.16), suggesting occurrence is more predictable than size.

---

## Key Findings
- Land cover features (grassland, evergreen forest, deciduous forest) were the strongest predictors of wildfire occurrence.
- Weather features alone were less predictive but improved model performance in combination with land cover.
- Integrated, large-scale datasets allow mapping wildfire risk at a national scale.

---

## Future Work
- Add probability scores instead of binary predictions.
- Integrate human activity data (population density, land usage).
- Use NDVI and vegetation health indices to refine fuel load estimates.
- Increase temporal and spatial resolution (weekly, sub-county level predictions).
- Incorporate real-time data streams (satellite imagery, weather forecasts).

---

## Skills Demonstrated
- Large-scale geospatial data processing and ETL pipeline design.
- Advanced feature engineering for environmental time-series data.
- Building and evaluating machine learning classification models.
- Visualizing geospatial and tabular data for EDA and reporting.
- Team collaboration on a multi-stage data science project.

---

## Explore the Project
1. Browse **Notebooks** for data cleaning, merging, and modeling steps.
2. Read **Final_report.md** for full technical documentation and methodology.
3. Explore **Images/** for EDA plots, data distributions, and model results.
4. Access raw datasets through the official links above to reproduce or extend this work.
```

