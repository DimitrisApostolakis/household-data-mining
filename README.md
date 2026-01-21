# Household Electric Power Consumption Analysis

## Introduction

I implemented data preprocessing and feature engineering on the **Individual Household Electric Power Consumption** dataset. Subsequently, I applied the following methods:

- **Binary Classification**: A model that predicts whether the consumption of the given day is high or low.
- **Regression**: A model that predicts the consumption of the next day.
- **Clustering**: A model that groups the data.
- **Association Rules**: A model that creates rules and correlations between data.

---

## Methodology

### Preprocessing

Since we are dealing with time series data, Ι did not delete rows with missing values because gaps would be created between days when resampling to daily frequency. Therefore, Ι used interpolation to fill in the gaps. Afterward, Ι performed resampling based on the day, rather than the minute, while retaining the measurement units of the original DataFrame.

---

### Feature Engineering

Ι created the following columns to assist the models with new correlations and patterns:

- **Seasons** (one-hot encoded):
  - Season_Winter  
  - Season_Spring  
  - Season_Summer  
  - Season_Autumn  

- **Peak_hour_power**: Maximum consumption value within the day  
- **Daily_weekend_usage**: Consumption during the weekend  
- **Day**: Day of the week  
- **Night_time_usage**: Consumption during the night zone (22:00–06:00)  
- **Is_working_day**: Whether it is a working day or not  

The days and seasons are represented using one-hot encoding.

---

### Classification

Ι created a target column based on the household's mean total consumption. If the day's total consumption is greater than the mean, it is considered **High**.

- Data split:
  - 70% Training
  - 30% Validation

- Features:
  - All columns except:
    - Target
    - Datetime
    - Global_active_power
    - Global_intensity  

This prevents the model from learning a direct formula and encourages prediction based on engineered features.

- Models used:
  - Decision Tree Classifier
  - Random Forest Classifier

---

### Regression

Ι created a target column such that the total consumption of the next day is included in the current day's data, as the models do not have a temporal understanding by default.

Additionally, lag features were created based on consumption from:
- 2 days prior
- 3 days prior
- 7 days prior
- 14 days prior
- 30 days prior

- Model used:
  - Random Forest Regressor

---

### Clustering

Ι created features related to:
- Percentage of consumption per space
- Load factor (to determine whether consumption was stable or if there were daily peaks)

The data was normalized prior to clustering.

- Model used:
  - Agglomerative Clustering

---

### Association Rules

The data was divided into two categories based on consumption:
- High
- Low

- Features used:
  - Weekend
  - Season_Summer
  - Season_Spring
  - Season_Winter
  - Season_Autumn
  - Is_working_day

- Algorithm used:
  - Apriori

---

## Results

### Clusters

![Clusters](https://github.com/user-attachments/assets/359f098c-5492-499d-a5eb-5f6b30bb8303)

---

### Association Rules

- If it is **summer** and a **working day**, consumption is almost certainly low.
- In **winter**, the probability of having high consumption is **85%**.
- On **weekends**, consumption is high regardless of the season.
- **Winter weekends** increase the average consumption.

Therefore:
- **Winter + Weekend → Maximum consumption**

---

## Conclusions

The correlations identified by the **Association Rules** align with the groups created through **Clustering**, allowing us to understand the general household consumption patterns.

### Seasonal Effects
- Minimum electricity consumption is observed during **summer**.
- Maximum consumption occurs during **winter**, with an **85% probability** of high usage.
- **Spring and autumn** exhibit mild consumption levels.

### Day Type Effects
- **Weekends** show higher consumption compared to working days, regardless of the season.

### Extreme Values
- **Highest consumption**: Winter weekends (~39.5 kWh)
- **Lowest consumption**: Summer working days (~16.9 kWh)
