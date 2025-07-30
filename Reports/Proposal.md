# Predicting Wildfire Risks: A Model Incorporating new Factors

### Team

Charles Sirey, Henry Dare, Adam Murphy (POC)

# Introduction

We aim to develop a machine learning model that predicts wildfire risks across the United States based on evolving climate change factors. Specifically, we will forecast not only current wildfire hotspots but also identify new regions becoming susceptible to wildfires over time. Our approach integrates environmental factors such as temperature, humidity, wind speed, and vegetation health to create a more comprehensive prediction system. By focusing on vegetation as the primary variable, we will analyze how changes in plant cover and health contribute to increased fire risks in different areas. This model will help us understand how shifts in climate and ecosystem dynamics are driving new fire-prone regions at a national scale.

**What is new in our approach?** Currently models only consider weather, temperature, wind speed, climate. Our model will incorporate the same factors but will also include vegetation patterns to see if certain areas are more suceptible to wildfire risks.

**Who cares?** Because our Model will be predicting fire danger in the future, it greatly broadens the amount of stakeholders who can benefit from our study. The most important stakeholders though, are policymakers who are capable of implementing preventative measures based on our data, as well as those living in areas that may be deemed as high risk in the future.

# Literature Review

Most current wildfire prediction models are **region-specific**, focusing on localized environmental conditions and risk factors. For instance, NASA’s **Wildfire Digital Twin** is designed to simulate real-time wildfire spread and smoke dispersion, but it primarily focuses on high-resolution, specific regions in the U.S. ([NASA Science](https://science.nasa.gov/science-research/science-enabling-technologynasa-wildfire-digital-twin-pioneers-new-ai-models-and-streaming-data-techniques-for-forecasting-fire-and-smoke/)). Similarly, models like the **Probability of Fire (PoF)** use machine learning to predict fire risks but largely depend on weather data and fail to incorporate more comprehensive factors like vegetation health or human impact on a broader scale ([ECMWF](https://www.ecmwf.int/en/about/media-centre/science-blog/2024/machine-learning-ignites-wildfire-forecasting)). These models are effective but often limited by their geographic scope and the range of factors they consider.

# Data

We are using data from three main sources: **fire history**, **climate history**, and **vegetation**. Our datasets cover the years **2000 to 2024**, though this range may change based on data availability. Each dataset includes essential features for building our wildfire prediction model.

### 1. Fire History Data
- **Source**: [National Interagency Fire Center (NIFC)](https://data-nifc.opendata.arcgis.com/search?tags=historic_wildlandfire_opendata%2CCategory)
- **Summary**: The dataset includes longitude and latitude coordinates of wildfire occurrences, the size of the fire, the date it started, and where and when it was stopped. These features will help us identify high-risk areas and track fire trends over time.
- **Provenance**: This data is provided by the NIFC, which is a reliable and authoritative source for fire incident data in the U.S. It includes metadata for context on how the data was collected and verified.

### 2. Vegetation Data
- **Source**: [USDA Forest Service - FIA DataMart](https://research.fs.usda.gov/products/dataandtools/tools/fia-datamart)
- **Summary**: This dataset contains information on the types of vegetation that grow in different regions, as well as specific statistics related to vegetation health and types. This will allow us to assess vegetation susceptibility to wildfires.
- **Provenance**: The data is collected and maintained by the USDA Forest Service, ensuring its reliability and accuracy for monitoring forest conditions. Metadata is available for this dataset, detailing its collection methods.

### 3. Climate/Weather History Data
- **Source**: [PRISM Climate Group](https://prism.oregonstate.edu/)
- **Summary**: This dataset provides spatial climate data for the U.S., including temperature, precipitation, and drought information. This is critical for understanding how weather conditions influence wildfire risk.
- **Provenance**: The PRISM Climate Group is a reputable source for climate data in the U.S., offering high-resolution climate information with comprehensive metadata on data collection methods.

### Data Summary
- **Fire History**: Key features include location (latitude/longitude), fire size, start and end dates.
- **Vegetation**: Includes vegetation types, coverage, and health statistics.
- **Climate**: Features temperature, precipitation, and drought indices across the U.S.
  
Each dataset has been sourced from reputable organizations and includes metadata for validation. More datasets may be incorporated as needed, depending on the specific requirements of the model during the development phase.


# Methods

We will preprocess the data by cleaning and transforming it to retain only the relevant variables, such as temperature, humidity, wind speed, and vegetation health. The data will be aligned by time (year, month) and geographical coordinates to ensure consistency across multiple datasets. Feature engineering will be used to derive meaningful insights, such as cumulative rainfall and vegetation dryness, which are critical for understanding wildfire risks. 

We plan to apply general machine learning techniques, such as decision trees, random forests, and gradient-based models, to capture the relationships between climate variables, vegetation, and wildfire occurrence. As the data is processed, other algorithms like support vector machines or ensemble methods may be explored based on model performance. Initially, we will evaluate model accuracy, precision, recall, and F1 score, though these metrics and approaches may evolve as we refine the data and adapt to stakeholder needs.

# Project Plan
Period|Activity|Milestone
|--|--|--|
|10/4-10/11|Stakeholder analysis and data collection|Identified stakeholders and started gathering relevant data
|10/12-10/18|Data preprocessing and exploration|Cleaned data, initial EDA, identified key factors for the model
|10/19-10/25|Begin exploring the use of human activity data|Implement human impact into our model
|10/26-11/2|Model refinement, testing on national grid|Refined model, implement national visualization representing different areas.
|11/3-11/10|Model tuning, final evaluation, potential expansion to global grid|Initial national grid ready and finalized. Exploration and experimentation of global grid
11/11-11/18|Final report preparation, model interpretation|Finalized model and report, integrated feedback.

### Risks
Our risk includes the factors that may be present but aren’t being inputted in the model. One of these factors is the wildlife. Many animals may play a role in reducing fires in an ecosystem, or in starting them as a natural cycle. With changing temperatures and manmade activities, the ranges of different animals are changing. We cannot track factors like invasive species or how the change in temperature may influence the habits of native species. However, we don’t feel that these factors will necessarily influence our results, or skew our data in any major measurable way. It is true that human activity often causes forest fires, but humans are not doing more or less fire-starting activity based on the probability of a fire occurring. The idea that this behavior is more likely to cause a fire in areas we designate as high-risk is if anything providing validation to our research, not working against it.

For climate data, the datasets we are using already have modeled data, which should be accurate enough for our purposes but aren’t truly recorded data, which is a risk that should be considered. As fire data gets more recent, the records of fires get a lot better. Older data is a lot more sparse as many fires may have not been recorded. This naturally means the newer data is much more thorough than our older data