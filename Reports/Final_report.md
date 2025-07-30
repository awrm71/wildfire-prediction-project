# Predicting Wildfire Risks: A Model Incorporating new Factors

### Team

Charles Sirey, Henry Dare, Nolan Pittman, Adam Murphy (POC)

### Introduction

Our project focuses on addressing the growing threat of wildfires in the United States, a pressing issue exacerbated by climate change. Rising temperatures, prolonged dry periods, and shifts in weather patterns have not only made wildfires more frequent but have also expanded their reach into previously unaffected areas. This presents significant challenges for policymakers tasked with safeguarding communities and natural resources. Our primary stakeholder, policymakers, require tools to anticipate and mitigate wildfire risks, enabling them to allocate resources and prepare for potential disasters effectively.

To meet this need, we have developed a machine learning model that predicts the likelihood of wildfires at the county level. Our model incorporates climate, weather, and land cover data, offering an innovative approach to identifying at-risk areas. By analyzing land cover, we can pinpoint regions where specific land types may contribute to increased wildfire susceptibility, going beyond conventional factors such as temperature and precipitation. This insight empowers policymakers to take proactive measures, such as targeted resource allocation and land management strategies, to address emerging fire risks.

### Literature Review

Most current wildfire prediction models are **region-specific**, focusing on localized environmental conditions and risk factors. For instance, NASA’s **Wildfire Digital Twin** is designed to simulate real-time wildfire spread and smoke dispersion, but it primarily focuses on high-resolution data for specific regions in the U.S. ([NASA Science](https://science.nasa.gov/science-research/science-enabling-technologynasa-wildfire-digital-twin-pioneers-new-ai-models-and-streaming-data-techniques-for-forecasting-fire-and-smoke/)). Similarly, models like the **Probability of Fire (PoF)** use machine learning to predict fire risks but largely depend on weather data, limiting their ability to incorporate diverse factors such as land cover changes on a broader scale ([ECMWF](https://www.ecmwf.int/en/about/media-centre/science-blog/2024/machine-learning-ignites-wildfire-forecasting)). These models are effective but often constrained by their narrow geographic scope and the specific datasets they use.

Recognizing these limitations, our approach integrates land cover data alongside climate and weather information to provide a more comprehensive view of wildfire risks. By analyzing broader environmental patterns, we aim to predict wildfire occurrences not just in high-risk regions but also in areas that could become vulnerable in the future due to changing land use or environmental shifts. This methodology addresses the gap left by existing models and offers policymakers a more actionable tool for long-term wildfire mitigation strategies.

### Data and Methods

#### Data  

Our project leverages a combination of datasets designed to provide comprehensive insights into wildfire risks across the United States. The data includes climate conditions, precipitation, land cover types, and fire history, each sourced from reputable organizations known for their reliability and accuracy. These datasets offer both the breadth and depth necessary for developing a robust machine learning model to predict wildfire-prone areas at the county level.  

### 1. Fire History Data  
- **Source**: [National Interagency Fire Center (NIFC)](https://data-nifc.opendata.arcgis.com/search?tags=historic_wildlandfire_opendata%2CCategory)  
- **Summary**: This dataset includes detailed records of wildfire incidents across the U.S., spanning over 40 years. Features include fire sizes, locations, and dates of occurrence.  
- **Data Characteristics**: The dataset consists of over 900,000 entries, with attributes such as the geographic location of the fire, the size in acres, and the time of occurrence. This breadth of data allows for a comprehensive understanding of historical wildfire patterns across different regions.  
- **Visualization**
  ![Original Fire Data](../Images/Fire_before.png)
  
  *Figure 1: Histogram of fire sizes before applying a log scale transformation.*
  
  ![Post Logged Fire Data](../Images/Fire-after.png)
  
  *Figure 2: Histogram of fire sizes after applying a double log scale transformation to better visualize the distribution.*

### 2. Climate and Weather Data  
- **Source**: [PRISM Climate Group](../Images/https://prism.oregonstate.edu/)  
- **Summary**: This dataset provides high-resolution climate data, including average monthly temperatures and precipitation levels. The data is geospatially resolved to the nearest 2 miles, offering exceptional granularity for our analysis.  
- **Data Characteristics**: Key features include minimum, maximum, and mean temperatures, along with monthly precipitation totals, enabling us to understand climate trends and their influence on wildfire risks.  
- **Visualization**:  
  ![Mean Temperature](../Images/meantemp.png)  

  *Figure 3: Mean temperature distribution for July 2023 across U.S. counties.*

  ![Precipitation](../Images/precipitation.png)  

  *Figure 4: Monthly precipitation levels across the U.S. in March 2023.*  

### 3. Land Cover Data  
- **Source**: [US Geological Survey (NLCD)](../Images/https://www.mrlc.gov/data/national-land-cover-database-nlcd-2021)  
- **Summary**: This dataset details the types of land cover across the U.S., including forests, grasslands, urban areas, and water bodies. This information helps identify areas where land cover types may contribute to wildfire risks.  
- **Data Characteristics**: The data was provided as a zip folder containing multiple `.img` files for various land cover categories. The zip file was initially 27 GB and expanded to 227 GB after extraction. These files had to be collected and processed to prepare the data for use in our machine learning model.  
- **Visualization**:  
  ![Land Cover](../Images/Land-cover.png)  

  *Figure 5: Land cover types across the U.S., visualized using updated NLCD colors.*  

### 4. County and State Boundaries  
- **Source**: [US Census TIGER/Line Shapefiles](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.2023.html#list-tab-790442341)  
- **Summary**: Boundary shapefiles are used to assign fire, climate, and land cover data to specific counties and states, ensuring proper spatial organization for analysis.  

These datasets provide a solid foundation for our wildfire prediction model. In the next section, we will detail the methods used to preprocess this data and integrate it into our machine learning pipeline.  

### Methods  

Our methods involved extensive data preprocessing, feature engineering, and experimentation with machine learning techniques to predict wildfire occurrences in U.S. counties. The steps below outline our process, organized logically to demonstrate the work completed and the rationale behind our decisions.  

### Weather Data Preprocessing  
To incorporate weather data into our model, we converted `.bil` files into time-series DataFrames for each weather category, including:  
- Precipitation (mm)  
- Vapor pressure deficit (min and max)  
- Mean dew point temperature  
- Minimum, maximum, and mean temperatures  

For each county, we averaged these weather metrics across different data points within the county, resulting in a monthly summary table for every county. Independent cities not tied to specific counties were addressed by averaging the weather data of adjacent counties. Afterward, we combined all weather categories into a single table containing the following columns:  
- Date  
- County and State identifiers  
- Weather categories (e.g., temperature, precipitation)  

The processing steps were implemented in `weather_combining_cleaning.ipynb`.  

### Land Cover Data Preprocessing  
Our land cover data, provided as `.img` files (with accompanying `.ige` files), was processed to determine the proportion of each land cover type within every county. For example:  
- Evergreen forest: 29%  
- Mixed forest: 11%  
- Grassland/herbaceous: 4%  

These proportions, summed to 1 for each county, enabled us to represent land cover as a set of percentage-based features.  

Since land cover data was not available for every year, we applied linear interpolation between years to fill in missing data. This decision was justified by the minimal changes in land cover over short periods. The processed data captured subtle shifts in land types, such as gradual forest encroachment or land use changes, which are critical for wildfire predictions.  

Example land cover proportions:  
- Evergreen forest in Abbeville County, SC (2001): 27.6%  
- Evergreen forest (2024): 28.2%  

Processed land cover data was included in `landcover_cleaning.ipynb`.  

### Fire History Data  
The fire history dataset included fire size, location, and occurrence date for over 900,000 recorded incidents spanning 40 years. Once simplified to only include fire over an acre, the dataset included a little under 200,000 incidents. Due to the significant skew in fire size (most fires were under 5 acres), we initially attempted to model fire size but encountered poor results. Instead, we shifted our focus to predicting whether a fire occurred within a county in a given month.  

To achieve this, we transformed the fire history data into a binary format:  
- `1`: Fire occurred in a county during a specific month  
- `0`: No fire occurred  

This target variable was calculated for all counties across the 22-year period (2001-2023). The resulting dataset combined fire history, land cover percentages, and weather features to enable temporal and spatial wildfire predictions.  

The processing and cleaning of the fire data can be found in `fire_cleaning.ipynb`.

### Feature Engineering  
We implemented extensive feature engineering to capture critical patterns in the data:  

1. **Rolling Averages and Deficits**  
   - Features: `precip_3mo_mean`, `meantemp_3mo_mean`, `precip_deficit_3mo`, `vpdmax_3mo_mean`  
   - Method: 3-month rolling averages and deficits calculated within each `state_id` and `county_id`.  
   - Value: Captures short-term trends and drought conditions influencing fire risk.  

2. **Seasonal Anomalies**  
   - Features: `precip_anomaly`, `meantemp_anomaly`  
   - Method: Differences between current values and long-term monthly averages within `state_id` and `county_id`.  
   - Value: Highlights unseasonably dry or hot months that can trigger wildfires.  

3. **High Threshold Indicators**  
   - Features: `high_vpd`, `high_vpd_3mo_count`, `high_temp`  
   - Method: Binary flags for vapor pressure deficit (VPD) and temperature exceeding the 90th percentile. Rolling counts tracked high VPD months over a 3-month window.  
   - Value: Identifies extreme or sustained conditions that elevate fire risk.  

4. **Lagged Features**  
   - Features: `precip_lag1`, `meantemp_lag2`  
   - Method: Shifted precipitation (1 month) and mean temperature (2 months) values within groups.  
   - Value: Accounts for recent weather effects influencing fire likelihood.  

These engineered features added temporal context, highlighted extreme events, and captured anomalous conditions, improving our model’s ability to detect fire risk patterns.  

### Modeling Approach  
Our primary goal was to predict whether a wildfire would occur within a given county during a specific month. We employed a **Random Forest Classifier** for this task due to its robustness with both numerical and categorical data.  

#### Initial Model Testing  
- **Initial Results**: An early iteration of our model included incomplete vegetation data and lacked land cover inputs, yielding artificially high results (F1-score: 0.88).  
- **Corrected Approach**: Once we incorporated land cover data and corrected errors, the model’s performance stabilized.  

#### Final Modeling Steps  
- **Binary Target**: Fire occurrence (`1`) or no fire (`0`).  
- **Feature Selection**: Recursive Feature Elimination (RFE) identified the top 19 features for prediction.  
- **Balancing Data**:  
   - SMOTE (Synthetic Minority Over-sampling Technique) to balance fire occurrence classes.  
   - Tomek Links to clean overlapping samples.  

**Model File**: `fire_predicting_model.ipynb`  

#### Techniques Attempted but Not Pursued  
We experimented with unsupervised clustering techniques, but they proved ineffective:  
- K-Means and Mini-Batch K-Means  
- Gaussian Mixture Models  
- Silhouette scores indicated optimal clusters of 2, which lacked meaningful separation.  

These attempts were documented but ultimately excluded due to their limited utility.  

### Results


#### Fire Occurrence Prediction

We tested whether a fire occurred (`1`) or not (`0`) using a random forest classifier. After cleaning, imputing, and merging the data, the final dataset included:  
- **43 columns**  
- **~1,035,000 rows**

To optimize the model, we applied RFE to identify the most relevant columns. This process involved multiple trials as we experimented with defining the dependent variable. Ultimately, we finalized the dependent variable as fire occurrence, where **`1` represents "fire occurred"** and **`0` represents "no fire."**

Several iterations of RFE were run to maximize the F1 score. Each random forest classifier iteration took approximately 30 minutes, requiring significant computation time. After addressing initial challenges with data leakage and overfitting, the final model produced the following metrics:

- **F1 Score:** `0.625`  
- **Accuracy:** `0.970`  
- **Precision:** `0.611`  
- **Recall:** `0.640`  
- **ROC AUC Score:** `0.962`  

   ![RFE Feature Selection](../Images/RFE_feature2.png)  
  *Figure 6: Line chart showing optimized amount of Features using RFE. F1 scores are lower as the model uses the base Random Forest Model instead of the optimized one.*

Results saved in: `fire_predicting_model.ipynb`

The most important features identified were:  
- **Grassland/Herbaceous**  
- **Evergreen Forest**  
- **Deciduous Forest**

The table of features is below

![RFE Feature Importance](../Images/feature_importance.png)  
  *Figure 7: Table rankings the catagories used by their importance*



#### Fire Size Prediction

We also tested for the size of the fire using a random forest regressor with RFE feature selection. The results were as follows:

- **Mean Squared Error (MSE):** `0.2290`  
- **R-squared:** `0.1640`  
- **Root Mean Squared Error (RMSE):** `0.4785`  

Results saved in: `fire(size)_landcover_weather_model.ipynb`

While the MSE appears favorable, the fires were double log-scaled, meaning the RMSE would be much higher when brought back to its original unit. The R-squared value of `0.16` suggests that much of the variability remains unexplained by the model. As such, the test for fire size prediction was not as successful as the test for fire occurrence.


#### Conclusion

The results suggest that there is a stronger relationship between land cover, weather, and fire occurrence, rather than the size of the fire. Our model has demonstrated the ability to predict fire occurrence with a reasonable degree of accuracy. Furthermore, it indicates that land cover characteristics play a more significant role in predicting fire occurrence than weather conditions.




### Discussion  

Our model demonstrates a significant step forward in large-scale wildfire prediction, as it successfully integrates land cover data, climate features, and fire history. Unlike the region-specific models we reviewed in the literature, our approach leverages national-scale data while incorporating land cover proportions, which act as a proxy for vegetation conditions. This comprehensive integration makes our model robust in its design and scope.  

While we achieved a respectable F1-score of 0.625, reflecting our model's ability to predict wildfire occurrences at a county level on a monthly basis, we acknowledge that there is still considerable room for improvement. Our initial goals included predicting not only the likelihood of a fire occurring but also the size and frequency of fires within a given timeframe. However, due to constraints such as limited access to specific datasets, time restrictions, and challenges with highly skewed fire size distributions, we were unable to fully realize these goals.  

From a stakeholder perspective, our work partially addresses the needs of county-level policymakers. By predicting the occurrence of wildfires on a monthly basis, our model provides an early indication of risk, which can help allocate resources and prepare for potential events. However, predicting fire occurrence alone may not be sufficient for stakeholders to implement precise preventative measures. For instance, knowing that a fire might occur within a month in a specific county is useful, but the wide temporal and spatial scales make targeted action challenging.  

To better meet stakeholder needs, future work should focus on refining predictions to include the probability of fire occurrence at more granular locations and timeframes, such as weekly or daily intervals. Additionally, incorporating fire size predictions would be critical in distinguishing between minor events, such as small brush fires, and large-scale wildfires that pose significant threats to communities and ecosystems. This would require testing various thresholds for fire size different from our current level (>1 acre) to determine which events are most important to predict and prevent.  

We also explored alternative modeling approaches, such as predicting fire size directly, but these models performed poorly and produced results that lacked sufficient accuracy to be actionable. Similarly, clustering methods and other unsupervised techniques were attempted but yielded no meaningful patterns. These limitations suggest that while our current approach is a strong starting point, additional data, such as real-time satellite imagery or higher-resolution fire data, would be needed to further improve predictive performance.  

Overall, our model achieves a foundational goal: it predicts whether a fire will occur in a given county on a monthly basis with reasonable accuracy. While this is a significant achievement, particularly given the scale of the project, future iterations must address the need for greater precision in both fire size and location to fully meet stakeholder requirements. By building on this work and overcoming current limitations, we believe the model can become a more actionable tool for policymakers striving to mitigate wildfire risks.  

### Limitations  

Our project, while providing a strong framework for wildfire prediction, faces several limitations primarily related to data size, availability, and time constraints. These challenges impacted both the data preparation process and our ability to fully optimize the model.  

1. **Data Size and Format**  
   - The sheer size of the data presented significant challenges during the project. Downloading, unpacking, running, and manipulating datasets required substantial computational resources and time.  
   - A large portion of the data was not in CSV format (e.g., `.img` and `.bil` files), which required extensive parsing and formatting to make the data usable for our model. This additional preprocessing consumed valuable time and resources, limiting our ability to experiment with alternative approaches or further refine our methods.  

2. **Tree Data Limitations**  
   - While land cover data was used as a replacement, the initial tree data we intended to use was too incomplete and messy for practical use. It would have required excessive cleaning and manipulation, which proved infeasible within our time constraints. This omission may have limited the model's ability to capture certain vegetation-related risk factors.  

3. **Missing Data**  
   - Weather data for a few counties was incomplete and required imputation. While imputation methods provided reasonable estimates, the absence of actual observed data could introduce uncertainty into the results.  
   - Land cover data was only updated every three years (2003, 2006, 2009, etc.). To fill the gaps, we applied linear interpolation and extrapolation, which, while practical, may introduce inaccuracies when merging land cover data with other sources.  

4. **Time Constraints**  
   - Time limitations were one of the most significant barriers in this project. Any advanced modeling or preprocessing steps required substantial time to execute. For example, performing Recursive Feature Elimination (RFE) to select 10 features could take anywhere from 15 to 30 minutes, depending on the number of features constructed. This made iterative model tuning and feature selection a time-consuming process.  
   - Due to the dataset's size and complexity, all machine learning models had to be run on smaller batched datasets rather than the full dataset, even after feature selection. Running models on the complete dataset would have taken several hours, which was impractical given our constraints. As a result, our ability to test and optimize model performance was limited.  
   - Data transformation and clustering techniques also proved computationally expensive. Methods such as observing silhouette scores to determine the optimal number of clusters or evaluating clustering models became virtually unfeasible. These steps required extensive iterations that our current computational resources and timeline could not accommodate.  
   - The cumulative time spent waiting for data to load, be imputed, and run through the model significantly hindered our progress. Many alternative approaches and refinements could not be explored, as they would have required hours just to test a single iteration.  

Despite these constraints, our work demonstrates the potential of integrating climate, weather, and land cover data for wildfire prediction. With more time and computational resources, we could explore advanced modeling techniques, conduct thorough feature selection, and evaluate additional methods to further improve our results.  

### Future Work  

Our current model predicts whether a wildfire will occur in a given county on a monthly basis. Moving forward, we aim to enhance the precision of our predictions by incorporating a probability score, giving policymakers a percent chance of wildfire occurrence. This would provide a more actionable tool, enabling stakeholders to assess varying levels of risk and prioritize resources more effectively.  

One significant area for improvement lies in the inclusion of vegetation data. While land cover data served as a valuable substitute, we were unable to integrate more detailed tree or vegetation health data due to high levels of null values and feasibility issues at a national scale. Future iterations of the model could explore alternative sources of vegetation data, such as satellite-derived indices (e.g., NDVI) or more localized forest health datasets. This would allow the model to better capture the influence of vegetation on wildfire risk, especially as changes in plant health are often early indicators of fire susceptibility.  

Additionally, expanding the scope of the model to include human factors could significantly enhance its predictive power. Wildfires are frequently ignited by human activities, such as unattended campfires or discarded cigarettes. Incorporating population density, land usage, and proximity to urban areas could help identify regions where human influence increases wildfire risk. Although we had initially considered this direction, it was ultimately set aside due to the already large scale of the project. Revisiting this idea with additional time and resources could provide valuable insights for stakeholders.  

Future work could also focus on improving the spatial and temporal resolution of predictions. Currently, our model operates at a monthly and county level, which, while useful, may lack the granularity needed for targeted intervention. Refining predictions to a weekly or daily timescale, combined with higher-resolution spatial data, could make the model far more actionable for county officials and policymakers.  

Finally, we see significant potential in integrating real-time data sources, such as live satellite imagery, weather station updates, and fire detection systems, as well as predictive models for future weather and vegetation changes. By combining historical trends with live and forecasted data, our model could predict wildfire risks further into the future, giving stakeholders earlier and more actionable insights. This extended prediction window would allow policymakers to prepare in advance and implement preventative measures with greater confidence.  

In summary, the next steps for this project include:  
1. Generating probability scores for wildfire occurrence.  
2. Incorporating more detailed vegetation data, such as NDVI or forest health metrics.  
3. Exploring human activity factors, including population density and land use.  
4. Increasing temporal and spatial resolution to weekly or daily predictions.  
5. Integrating live and predictive data sources to extend the forecast window and improve risk assessment.  

By addressing these areas, we believe our model can evolve into a dynamic and forward-looking tool, enabling policymakers to make proactive decisions and implement long-term strategies to mitigate wildfire risks.  

