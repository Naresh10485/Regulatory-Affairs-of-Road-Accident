## Objective
* To analyze the causes of road accidents in Million Plus Cities in India.
* Identify patterns in causes and outcomes.
* Visualize the distribution of accidents across different categories.


## library_Used
These are the python libraries that is used to Data Visualisation and Preparartion.
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

## Notebook_Setup
* Since dataset is large and wide, we need to inspect the entire DataFrame, therefore to display all columns and rows without truncation, we make these setups:
```python
pd.set_option("display.max_columns", None)
pd.set_option("display.max_rows", None)
```
* Also, to ignore all the warnings, we setup:
```python
import warnings
warnings.filterwarnings("ignore")
```

## Data_Overview
* **Data Source:** Regulatory Affairs of Road Accident Data 2020 India
* Overview:
    - Focus on major cities in India.
    - Contains information on causes, outcomes, and categories of accidents.


## Data_Preparation
* Data Loading:
```python
df = pd.read_csv("Regulatory Affairs of Road Accident Data 2020 India.csv")
df.head(3)
```

* Initial exploration
```python
print("Data Shape:", df.shape)
print('\n')
print(df.info())
print('\n')
print(df["Outcome of Incident"].value_counts())
```

* Data Columns:
```python
print(df.columns)
```
    - These are the data Columns
        - Million Plus Cities
        - Cause category
        - Cause Subcategory
        - Outcome of Incident
        - Count (i.e. Number of accidents)

* Standardized column names:
```python
df.rename(columns = {"Million Plus Cities" : "million_plus_cities", "Cause category" : "cause_category", 
                     "Cause Subcategory" : "cause_subcategory", "Outcome of Incident" : "outcome_of_incident", 
                     "Count" : "count"}, inplace=True)
df.columns
```
* Null Values and Duplicates:
```python
print(df.isnull().sum())
print('\n')
print(df[df['count'].isnull()])
print('\n')
df[(df['million_plus_cities'] == 'Gwalior') & (df['cause_category'] == 'Impacting Vehicle/Object') & (df['cause_subcategory'] == 'Other Non')]
```
    - These Codes Successfully extract the null values rows.

* Assign 0 to null values:
```python
df.loc[df['count'].isnull(), 'count'] = 0
# Saving dataset into a new csv file
df.to_csv("road_accidents_long.csv", index=False)
```

## Accidents_Cause_category
* To further analysis the dataset, It's essential to know the Main Cause for Accidents. So, We extract unique values of cause category using code-snippet:
```python
df['cause_category'].unique()
```

* Thus, we have Data related to these Categories:
        - Traffic Control
        - Junction
        - Traffic Violation
        - Road Features
        - Impacting Vehicle
        - Weather.

## Outcome_of_Incidents
* Also, we extract the unique values of Outcome of Incidents,
```python
df['outcome_of_incident'].unique()
```

* Thus, Outcome of Incidents are:
        - Greviously Injured
        - Minor Injury
        - Persons Killed
        - Total Injured
        - Total number of Accidents

### So, based on outcome, We will focus our analysis on 3 most important outcomes of incidents: 
        - Total Number of Accidents, Total Injured and Persons Killed.



