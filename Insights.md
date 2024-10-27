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
import plotly.express as px
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
1. Total Number of Accidents
2. Total Injured and
3. Persons Killed.

## Analysis:Total_number_of_Accidents
* To analysis the total number of accidents, we extract and assign them into a new variable and further proceed:
```python
total_accidents_mask = df[df['outcome_of_incident']=="Total number of Accidents"]
total_accidents_mask['count'].sum()
```
* Output: 352416.0
    - Shows a large number of total accidents across all cities.

* To show, number of accidents across cities and further visualise them using bar chart, we grouped the total number of accidents across all million plus cities. Here is the code-
```python
cat_grp_accidents = total_accidents_mask.groupby(['million_plus_cities']).agg('sum')[['count']]\
    .sort_values(by='count', ascending=False)

# Create an Bar chart
fig = px.bar(data_frame=cat_grp_accidents, y = 'count' , title="Total Number of Accidents in Million Plus Cities", template ='plotly_white',text_auto= True, color='count', color_continuous_scale='plasma', height= 600)
fig.update_layout(xaxis_title = 'City Name', yaxis_title = 'Total Number of Accidents', yaxis = dict(tickformat = '.2f'))

fig.show()
```
![Bar Chart distribution of total number of accidents across cities](/Visuals/Total%20accidents.png)

* Percentage distribution of total number of accidents across cities:
```python
cat_grp_accidents['percent'] = cat_grp_accidents.transform(lambda x: (x/x.sum()) * 100)
cat_grp_accidents
```

* **Study with respect to  each Cause Category and Sub-category**

```python
accidents_subcat_labels = total_accidents_mask['cause_subcategory'].unique()


# Create a list of the unique cause categories for the outcome : 'Total number of Accidents'
accidents_cat_labels = total_accidents_mask['cause_category'].unique()

plt.figure(figsize=(50, 20))
plt.subplots_adjust(hspace=0.5, wspace = 0.3)

# For each cause category create plots
for label in accidents_cat_labels:
    # Create a figure for each cause category with 2 axes
    fig, axes = plt.subplots(1, 2, figsize=(20, 6))
    # Add title to the main figure
    plt.suptitle("Accidents due to "+label, fontsize=25, horizontalalignment="center")
    
    # Create a mask to filter out the data for each cause category
    label_mask = total_accidents_mask[total_accidents_mask['cause_category']==label]
    
    # Group by the locations and create a bar chart based on each cause category 
    cat_accidents = label_mask.groupby(['million_plus_cities']).agg('sum')[['count']].sort_values(by='count', ascending=False)   
    cat_accidents.plot(kind='bar', legend=False, title="Total Number of Accidents in cities due to "+label, ax=axes[0], xlabel="City Names", ylabel="Number of Accidents")
    
    # Improve spacing of labels in the plotted bar chart
    plt.tick_params(pad=5, labelsize=6)
    
    # Group by related subcategories of the cause category and create a pie chart of the cause sub-category
    subcat_labels_grp = label_mask.groupby(['cause_subcategory']).agg('sum')[['count']]
    subcat_labels_grp.plot(kind='pie', subplots=True, ylabel='', labeldistance=None, ax=axes[1], autopct='%1.1f%%')
    
    # Adjust spacing of legend for the pie chart
    plt.legend(bbox_to_anchor=(1.8, .75), loc="upper right")
    
    plt.show()
```
Thus,
* Accidents Due to Traffic Control
![Traffic Control](/Visuals/Accidents-Traffic%20Control.png)

* Accidents Due to Junction
![Junction](/Visuals/Accidents-Junction.png)

* Accidents Due to Traffic Violation:
![Traffic violation](/Visuals/Accidents-Traffic%20Control.png)

* Accidents Due to Road- Features:
![Road-Features](/Visuals/Accidents-%20Road%20Features.png)

* Accidents Due to Impacting Vehicle:
![Impacting Vehicles](/Visuals/Impacting%20Vehicles.png)

* Accidents Due to Weather:
![Weather](/Visuals/Accident-Weather.png)

* **List of cause sub-categories with top 3 cities:**
We extract top 3 cities where number of accidents is maximum relating to cause subcategory,

```python
accidents_cause_city_df = pd.DataFrame(columns = ['cause_sub_category', 'top_cities'])

for label in accidents_subcat_labels:
    label_mask = total_accidents_mask[total_accidents_mask['cause_subcategory']==label]
    
    subcat_label_mask = total_accidents_mask[total_accidents_mask['cause_subcategory']==label]
    subcat_accidents = subcat_label_mask.groupby(['million_plus_cities']).agg('sum')[['count']].sort_values(by='count', ascending=False)
    # top 3 cities in subcategory
    top_3_cities = subcat_accidents.head(3).reset_index()[['million_plus_cities']]
    accidents_cause_city_row = top_3_cities.apply(", ".join)

    data = pd.DataFrame({
            'cause_sub_category' : [label],
            'top_cities' : accidents_cause_city_row['million_plus_cities']
    })
    accidents_cause_city_df = pd.concat([accidents_cause_city_df, data])

accidents_cause_city_df
```
* This will provide the data set with top three cities with highest number of Total Accidents with respect to Cause subcategory.

* We have also Visualise the total number of accidents across million plus cities with respect to each subcategory. This will also provide valuable insights:
    - Code snippet to create Bar chart for each SubCategory across Cities
```python
plt.figure(figsize=(30, 12))
plt.subplots_adjust(hspace=0.5, wspace = 0.3)

for label in injured_subcat_labels:
    
    # Create a mask to filter out the data for each cause subcategory
    label_mask = total_injured_mask[total_injured_mask['cause_subcategory']==label]
    
    subcat_label_mask = total_injured_mask[total_injured_mask['cause_subcategory']==label]
    subcat_injured = subcat_label_mask.groupby(['million_plus_cities']).agg('sum')[['count']].sort_values(by='count', ascending=False)
    
    subcat_injured.plot(kind='bar', title="City-wise distribution of Total Injured in accidents due to "+ label, xlabel="City Names", ylabel="Number of people injured", legend=False)

    plt.tick_params(pad=5, labelsize=6)
    plt.show()

```


## Insights_from_total_accidents:

- `Chennai` tops the list in highest `Total number of Accidents`, followed by `Delhi`, `Bangaluru`, `Jabalpur`, and `Indore`.

- `Amritsar` recorded the least number of accidents across all categories.

- Second and third *least* number of accidents occur in `Jamshedpur` and `Chandigarh`.

- `Total Number of Accidents` count reported is the highest on `Sunny/Clear` weather days.

- Accidents occur most frequently on `Straight Roads` and most commonly for `Two Wheelers` as the `Impacting Vehicle`.

- `Over`(overspeeding) takes up a big chunk of space as the cause of accidents due to `Traffic Violation`.

- `T`, `Staggered Junction`, and `Four arm Junction` have almost equal distributions in count of people injured due to accidents at `Junctions`.
<bt>

* **City-wise observations of causes of accidents (Top 3 Cities)**

- `Delhi` topped the list of cities in most number of accidents due to : `Pedestrian` and `Driving on Wrong side`. Some more reasons were : `Bicycles`, `Bridge`, `Straight Road`, `Curved Road`, `Jumping Red Light`, `Traffic Light Signal`, `Flashing Signal/Blinker`, `Stop Sign`, `T` junction, `Sunny/Clear`, `Rainy` weather, and `Two Wheelers`.

- `Chennai` topped the list of cities in most number of accidents due to 11 such causes/sub-categories : `Rainy`, `Foggy/Misty`, weather, vehicles such as `Buses`, `Bicycles`, `Auto Rickshaws`, `Steep Grade`, `Culvert`, `Curved Road`, `Bridge`, due to `Use of Mobile Phone`, `Drunken Driving/ Consumption of alcohol and drug`, `Jumping Red Light`, `Y` Junction, `Round about Junction`, `Police Controlled`, and `Flashing Signal/Blinker`. 

- Most common reasons for accidents in `Allahabad(Prayagraj)` were : `Hail/Sleet` and `Pot Holes`. Some of the other reasons were `Drunken Driving/ Consumption of alcohol and drug`, `Buses`, roads with `Ongoing Road Works/Under Construction`, `Steep Grade`, `Culvert`, `Bridge`, due to `Drunken Driving/ Consumption of alcohol and drug`, `Jumping Red Light`, `Driving on Wrong Side` and `Flashing Signal/Blinker`.

- `Mumbai` topped the list of most accidents due to : `Y Junction`, `Uncontrolled` Traffic. Some of the other reasons : `Pedestrian`.

- Common reason for accidents in `Raipur` were : `T Junction`, `Rainy` weather.

- Common reason for accidents in `Jaipur` were : `Truck Lorries`.

- `Kanpur` topped list of most accident due to : `Truck Lorries`,` Ongoing Road Works,/Under Construction`. Some of the other reasons were `Foggy/Misty` weather.

- Most common reason for accidents in `Indore` were : `Over` and `Four arm Junction`. Some of the other reasons were `Sunny/Clear` weather, `Steep Grade`, `Culvert`, `Y` and `T` junction, `Pedestrian`, `Straight Road` and `Round about Junction`.

- Most common reasons for accidents in `Bengaluru` were : `Sunny/Clear` weather and `Straight Road`. Some of the other reasons were : `Two Wheelers`, `Over`, `Four arm Junction`, `Traffic Light Signal` and `Police Controlled`.

- Common reason for accidents in `Rajkot` : `Hail/Sleet`.

- Most common reason for accidents in `Jabalpur` were : `Two Wheelers` and `T Junction`. Some other reasons were : `Trucks/Lorries`, `Cars, Taxis, Vans and LMV`, `Auto Rickshaws`, `Drunken Driving/ Consumption of alcohol and drug`, `Y` junction and `Uncontrolled` traffic.

- Most common reason for accidents in `Lucknow` were : `Hail/Sleet`, `Foggy/Misty` weather and `Potholes`.

- Common reason for accidents in `Mallapuram` were : `Cars, Taxis, Vans and LMV` and `Auto Rickshaws`

- Common reason for accidents in `Gwalior` were : `Buses` and `Curved Roads`.

- Most common reasons for accidents in `Ludhiana` were : `Bicycles`.

- Most common reasons for accidents in `Hyderabad` were : `Staggered Junction`, `Round about Junction` and `Uncontrolled` traffic.

- Common reason for accidents in `Bhopal` were : `Stop sign`

- Common reason for accidents in `Kochi` were : `Police Controlled` traffic.

## Analysis:Total_Injured_and_Total_Killed
* Based on Previous method for `Total Number of Accidents`, we continued our study to Total Injured and Total Killed across all cities.
* The Analysis and Code can be checked in my **Jupyter Notebook** at 
![Jupyter Notebook](/Road%20Accidents%20Project.ipynb)

* Here is Insights from both Cause:

## Insights_from_Total_Injured:

- `Chennai` tops the list again in `Total Injured`, followed by `Delhi`, `Jabalpur`, `Bangaluru`, and `Indore`.

- `Amritsar` recorded the least number in `Total Injured`.

- Second and third positions in terms of *least* number of people injured belongs to `Jamshedpur` and `Dhanbad`.

- While `Bangaluru` had more number of accidents than `Jabalpur`, they seem to swap places in terms of `Total Injured` in the same year. More people were injured in `Jabalpur` than in `Bangaluru`.

- Again `Sunny/Clear` weather days see most number of people injured due to accidents, and most commonly on `Straight Roads`.

- `Over`(overspeedig) seems to be a big `Traffic Violation` cause, for people being injured due to accidents.

- `T`, `Staggered Junction`, and `Four arm Junction` have almost equal distributions in count of people injured due to accidents at `Junctions`.

* **City-wise observations of accident injury causes (Top 3 Cities)**

- Some of the main reasons for accident injuries in `Delhi` are : `Rainy`, `Foggy/Misty` weathers, `Straight Road`, `Curved Road`, `Bridge`, due to `Jumping Red Light`, `Driving on Wrong Side`, `Traffic Light Signal`, and `Flashing Signal/Blinker`. `Pot Hole`, `Stop Sign` and `Drunken Driving/ Consumption of alcohol and drug` is another relatively important cause of Delhi accidents.

- `Chennai` topped the list of cities in most number of people injured in accidents due to most of the causes/sub-categories. Some of the main causes are `Rainy`, `Foggy/Misty` weather, on roads with `Steep Grade`, `Pot Holes`, `Curved Road`, `Culvert`, `Bridge`, due to `Use of Mobile Phone`, `Jumping Red Light`, `Drunken Driving/consumption of alcohol and drug`, `Driving on Wrong Side`, in `Y`, `Round about Junction`, `Traffic Light Signal`, `Police Controlled`, and `Flashing Signal/Blinker`. Some of the other reasons are `Ongoing Road Works/Under Construction`, `Staggered Junction`, `T` and `Four arm Junction`. 

- `Allahabad/Prayagraj` topped the list of most accident injuries due to : `Hail sleet`. Some of the other reasons are : roads with `Pot Holes`, `Steep Grade`, `Ongoing Road Works/Under Construction`, `Culvert`, `Bridge`, due to `Use of Mobile Phone`, `Jumping Red Light`, and `Driving on Wrong Side`.

- Most common reasons for accident injuries in `Mumbai` were : `Staggered Junction`, `Uncontrolled` Traffic.

- Some common reasons for accident injuries in `Indore` were : `Four arm Junction`, `Y` junction, `Round about Junction`, `Over` `Sunny/Clear` weather, and `Straight Road`.

- Common reason for accident injuries in `Rajkot` were : `Hail/Sleet`.

- Common reason for accident injuries in `Bengaluru` were : `Sunny/Clear` weather and `Straight Road`. Some of the other reasons are `Four arm Junction`, `Traffic Light Signal`, `Police Controlled`.

- Common reason for accident injuries in `Mallapuram` were : `Rainy` weather, `Curved Road`.

- Most common reason for accident injuries in `Jabalpur` were : `Over`, in `T` junctions, due to `Uncontrolled` traffic. Some other reasons were : `Drunken Driving/ Consumption of alcohol and drug`, and `Sunny/Clear` weather.

- Most common reason for accident injuries in `Kollam` were : in `T` junctions and due to `Flashing Signal/Blinker`.

- Most common reason for accident injuries in `Kochi` were : `Police Controlled` traffic.

- Most common reason for accident injuries in `Bhopal` were : due to `Stop Sign`.

- Most common reason for accident injuries in `Hyderabad` were : in `Staggered Junction`, `Round about Junction`, and due to `Uncontrolled` traffic.

- Most common reason for accident injuries in `Kanpur` were : `Ongoing Road Works/Under Construction`, and `Foggy/Misty` weather.

- Most common reason for accident injuries in `Lucknow` were : `Hail/Sleet`.


## Insights_from_Total_Killed


- Fatality, if measured by number of `Persons Killed`, accidents in `Delhi` seems to be the most fatal, followed by `Chennai`, `Bangaluru`, `Jaipur` and `Kanpur`.

- While `Jaipur` was 8th in `Total Number of Accidents` and 11th in terms of `Total Injured`, accidents in `Jaipur` seem to be highly fatal as it ranked 4th w.r.t `Persons Killed` due to accidents.

- Accidents in `Kanpur` seems to be highly fatal as it rose to 5th position in terms of `People Killed` due to accidents. `Kanpur` had ranked 18th in `Total Number of Accidents` and 21st in terms of `Total Injured`. 

- In terms of fatality, accidents in `Srinagar` seems to be the least fatal. 

- Second and third positions in terms of *least* fatal accidents belong to `Jamshedpur` and `Chandigarh`.

- `Two Wheelers` and `Over`(overspeeding) seem to be the most common causes of fatal accidents.

- Most fatal accidents occur on `Sunny/Clear` weather conditions and on `Straight Roads`. Most persons were killed due to such conditions in `Delhi`, `Jaipur` and `Bangaluru`.

* **City-wise Observations of accident death causes (Top 3 Cities)**

- `Delhi` topped the list of cities in most number of accident deaths due to these causes : `Sunny/Clear` weather, vehicles such as `Two Wheelers`, `Pedestrian`, `Straight Road`, `Curved Road`, `Four arm Junction`, `Traffic Light Signal`. Some of the other reasons were : `Flashing Signal/Blinker`, `Uncontrolled` traffic, `Drunken Driving/ Consumption of alcohol and drug`, `Jumping Red Light`, `Bicycles`, `Bridge`, `Y`, `T`, `Staggered Junction` and `Stop Sign`.

- `Chennai` topped the list of cities in most number of accident deaths due to causes/sub-categories : `Rainy` weather, vehicles such as `Cars, Taxis, Vans and LMV`, `Bicycles`, `Bridge`, `Staggered Junction`, `Round about Junction`, `Stop Sign`, `Police Controlled`, `Over` and `Flashing Signal/Blinker`. Some of the other reasons were : `Foggy/Misty` weather, `Y` and `Four arm junction`, `Traffic Light Signal`, `Curved Road`, `Ongoing Road Works/Under Construction` and `Two Wheelers`.

- `Allahabad/Prayagraj` topped the list of most deaths due to : `Hail sleet`, `Auto Rickshaws`, `Steep Grade`, `Pot Holes`, `Culvert`, `Use of Mobile Phone`, `Jumping Red Light`, `Drunken Driving/consumption of alcohol and drug`, `Driving on Wrong Side`. Some other causes were : `Buses`, `Ongoing Road Works/Under Construction`, `Round about Junction`, `Stop Sign`, `Rainy`, `Truck/Lorries`, and `Four arm junction`.

- `Mumbai` topped the list of most deaths due to : `Y Junction`, `Uncontrolled` Traffic.

- `Raipur` topped list of most accident deaths due to : `T Junction`.

- `Jaipur` topped list of most accident deaths due to : `Truck Lorries`.

- `Kanpur` topped list of most accident deaths due to : `Foggy and Misty`, `Other Non`,` Ongoing Road Works,/Under Construction`

- Most common reason for accident deaths in `Ludhiana` were : due to `Bridge`, `Bicycles`, `Auto Rickshaws`, `Drunken Driving/ Consumption of alcohol and drug`, and `Flashing Signal/Blinker`.

-  Most common reason for accident deaths in `Bengaluru` were : `Two Wheelers`, `Straight Road`, `Over`, `Pedestrian`. Some of the other reasons were on `Sunny/Clear` days and `Police Controlled` traffic.

- Most common reason for accident deaths in `Rajkot` were : `Hail/Sleet`.

- Most common reason for accident deaths in `Lucknow` were : `Buses`. Some other reasons were : roads with `Pot Holes`, `Culvert`, `Round about Junction` due to `Driving on Wrong Side`, `Use of Mobile Phone` and `Jumping Red Light` in `Hail/sleet`, `Foggy/Misty` weather.

- Most common reason for accident deaths in `Agra` were : roads with `Steep Grade` and `Pot Holes`.

- Most common reason for accident deaths in `Jabalpur` were : in `T Junction` due to `Truck Lorries`.

- Most common reason for accident deaths in `Ahmedabad` were : `Pedestrian`.

- Most common reason for accident deaths in `Ghaziabad` were : `Auto Rickshaws`

- Most common reason for accident deaths in `Raipur` were : `T Junction`.

- Most common reason for accident deaths in `Hyderabad` were : `Staggered Junction`, and `Uncontrolled` traffic.


## Transforming_into_Wide_Data

* We further arrange the dataset in wide format with categories and subcategories as columns for each city. 
```python
# Group by category, sub category and outcome features, then sum their counts
grouped = df.groupby(['million_plus_cities', 'cause_category', 'cause_subcategory', 'outcome_of_incident']).agg('sum')['count'].reset_index()

# Pivot the DataFrame
df_wide = grouped.pivot(index='million_plus_cities', columns=['cause_category', 'cause_subcategory', 'outcome_of_incident'], values='count').reset_index()

#df_wide.columns = df_wide.loc[0:2].fillna("").apply(' : '.join)
df_wide.to_csv("road_accidents_wide.csv", index=False)
```

* This wide data set can be checked here,
![Wide Data](/road_accidents_wide.csv)

## Conclusion
* Road accidents in India have multiple causes with varied outcomes.
* Data-driven insights can guide effective policy decisions.
* Regulatory actions can significantly reduce accident rates.
