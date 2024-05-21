---
title: "Earthquake Analysis"
author: "Prachi Patel"
date: "2022-09-28"
excerpt: "This project aims to analyze earthquake data to understand patterns, trends, and characteristics of seismic activity. By examining factors such as location, magnitude, and depth of earthquakes over time, we seek to gain insights into seismic behavior and potential risk areas. Through data visualization and statistical analysis, we aim to contribute to earthquake preparedness and mitigation efforts."
output:
  html_document:
    df_print: paged
  pdf_document: default
editor_options: 
  markdown: 
    wrap: sentence

---

### Introduction

This project aims to analyze earthquake data to understand patterns, trends, and characteristics of seismic activity. By examining factors such as location, magnitude, and depth of earthquakes over time, we seek to gain insights into seismic behavior and potential risk areas. Through data visualization and statistical analysis, we aim to contribute to earthquake preparedness and mitigation efforts.

### Data Dictionary


|Variable |Description |
|:--------|:-----------|
|         |            |

### Import Library

This section loads and attaches all the necessary libraries for this project.


```r
library(reticulate)
```



### Import python Libraries

Adding the necessary python libraries for this project.


```python
import numpy as np
import pandas as pd
import matplotlib as mpl
import matplotlib.pyplot as plt
```

### Import Dataset


```python
df1 = pd.read_csv("earthquakes-dataset.csv")
df1.head()
```

```
##          Date      Time  Latitude  ...  Location Source Magnitude Source     Status
## 0  01/02/1965  13:44:18    19.246  ...           ISCGEM           ISCGEM  Automatic
## 1  01/04/1965  11:29:49     1.863  ...           ISCGEM           ISCGEM  Automatic
## 2  01/05/1965  18:05:58   -20.579  ...           ISCGEM           ISCGEM  Automatic
## 3  01/08/1965  18:49:43   -59.076  ...           ISCGEM           ISCGEM  Automatic
## 4  01/09/1965  13:32:50    11.938  ...           ISCGEM           ISCGEM  Automatic
## 
## [5 rows x 21 columns]
```

I add the `errors` argument with the value `coerce` to make sure that if there's any problem parsing the data, it will just set that bad value to null instead of stopping everything. 

This way, the process keeps going smoothly even if there are a few mistakes in the data.


```python
df1['Datetime'] = pd.to_datetime(df1['Date']+' '+df1['Time'],errors='coerce')
df1.head()
```

```
##          Date      Time  ...     Status            Datetime
## 0  01/02/1965  13:44:18  ...  Automatic 1965-01-02 13:44:18
## 1  01/04/1965  11:29:49  ...  Automatic 1965-01-04 11:29:49
## 2  01/05/1965  18:05:58  ...  Automatic 1965-01-05 18:05:58
## 3  01/08/1965  18:49:43  ...  Automatic 1965-01-08 18:49:43
## 4  01/09/1965  13:32:50  ...  Automatic 1965-01-09 13:32:50
## 
## [5 rows x 22 columns]
```

Set the `Datetime` field as the index to enable more efficient and meaningful data analysis.


```python
df1 = pd.read_csv("earthquakes-dataset.csv",index_col=0, parse_dates=[['Date','Time']])
df1.index = pd.to_datetime(df1.index, errors='coerce')
df1.head()
```

```
##                      Latitude  Longitude  ... Magnitude Source     Status
## Date_Time                                 ...                            
## 1965-01-02 13:44:18    19.246    145.616  ...           ISCGEM  Automatic
## 1965-01-04 11:29:49     1.863    127.352  ...           ISCGEM  Automatic
## 1965-01-05 18:05:58   -20.579   -173.972  ...           ISCGEM  Automatic
## 1965-01-08 18:49:43   -59.076    -23.557  ...           ISCGEM  Automatic
## 1965-01-09 13:32:50    11.938    126.427  ...           ISCGEM  Automatic
## 
## [5 rows x 19 columns]
```

Data type of the `Depth` field is currently set to float, I have changed it to int.


```python
df1['Depth'] = df1['Depth'].astype(int)
df1.dtypes
```

```
## Latitude                      float64
## Longitude                     float64
## Type                           object
## Depth                           int32
## Depth Error                   float64
## Depth Seismic Stations        float64
## Magnitude                     float64
## Magnitude Type                 object
## Magnitude Error               float64
## Magnitude Seismic Stations    float64
## Azimuthal Gap                 float64
## Horizontal Distance           float64
## Horizontal Error              float64
## Root Mean Square              float64
## ID                             object
## Source                         object
## Location Source                object
## Magnitude Source               object
## Status                         object
## dtype: object
```

Drop all the columns with the null values.


```python
df1 = df1.dropna(axis = 1)
df1.head()
```

```
##                      Latitude  Longitude  ... Magnitude Source     Status
## Date_Time                                 ...                            
## 1965-01-02 13:44:18    19.246    145.616  ...           ISCGEM  Automatic
## 1965-01-04 11:29:49     1.863    127.352  ...           ISCGEM  Automatic
## 1965-01-05 18:05:58   -20.579   -173.972  ...           ISCGEM  Automatic
## 1965-01-08 18:49:43   -59.076    -23.557  ...           ISCGEM  Automatic
## 1965-01-09 13:32:50    11.938    126.427  ...           ISCGEM  Automatic
## 
## [5 rows x 10 columns]
```

Verify if any null value is available or not.


```python
df1.info() # As we see no null values are available.
```

```
## <class 'pandas.core.frame.DataFrame'>
## DatetimeIndex: 23412 entries, 1965-01-02 13:44:18 to 2016-12-30 20:08:28
## Data columns (total 10 columns):
##  #   Column            Non-Null Count  Dtype  
## ---  ------            --------------  -----  
##  0   Latitude          23412 non-null  float64
##  1   Longitude         23412 non-null  float64
##  2   Type              23412 non-null  object 
##  3   Depth             23412 non-null  int32  
##  4   Magnitude         23412 non-null  float64
##  5   ID                23412 non-null  object 
##  6   Source            23412 non-null  object 
##  7   Location Source   23412 non-null  object 
##  8   Magnitude Source  23412 non-null  object 
##  9   Status            23412 non-null  object 
## dtypes: float64(3), int32(1), object(6)
## memory usage: 1.9+ MB
```

Created a new dataset having important columns such as latitude, longitude, type, depth and magnitude.


```python
df2 = df1[['Latitude','Longitude','Type','Depth','Magnitude']]
```

Sample is used to get the random records based on provided number of records.


```python
df2.sample(5)
```

```
##                      Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                             
## 2005-06-09 14:00:50    51.554   -131.125  Earthquake     10        5.8
## 1971-10-23 23:01:19   -57.320    -25.523  Earthquake     25        5.7
## 1971-07-27 07:35:43    -5.813    151.604  Earthquake     40        5.8
## 1974-02-03 18:45:46    -5.133    133.848  Earthquake     33        5.7
## 1978-06-03 04:57:13   -57.096   -140.888  Earthquake     33        6.0
```

Now, let's check biggest earthquake in the last fifty years.


```python
df2[df2.Type == 'Earthquake'].Magnitude.max()
```

```
## 9.1
```

```python
df2[df2.Type == 'Earthquake'].Depth.max()
```

```
## 700
```

```python
df2[df2.Type == 'Earthquake'].Depth.min()
```

```
## -1
```

let's narrow down the dataset to include only the records corresponding to the largest earthquake event.


```python
df2.loc[df2['Magnitude'] == 9.1]
```

```
##                      Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                             
## 2004-12-26 00:58:53     3.295     95.982  Earthquake     30        9.1
## 2011-03-11 05:46:24    38.297    142.373  Earthquake     29        9.1
```

```python
df2.loc[df2['Depth'] == 700]
```

```
##                      Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                             
## 1985-10-22 19:14:02   -20.158   -179.163  Earthquake    700        5.5
```

```python
df2.loc[df2['Depth'] == -1]
```

```
##                      Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                             
## 1992-06-28 12:00:45    34.131   -116.408  Earthquake     -1       5.77
```

Added a visualization for the distribution of earthquake magnitudes in the dataset, as it will provides insights into the frequency and range of earthquake magnitudes.


```python
plt.hist(df2['Magnitude'], bins=20, edgecolor='black')
plt.xlabel('Magnitude')
plt.ylabel('Number of Earthquakes')
plt.title('Distribution of Earthquake Magnitudes (1965-2016)')
plt.grid(True)
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-1.png" width="672" />

*Bins are the buckets into which data is grouped. Typically, the number of bins is chosen between 5 and 10, but the optimal number can vary depending on the specifics of your dataset.*


Based on the graph we can see that higher magnitudes are not the highest one.



-------------------------------------------------------------------------------------

To determine how many earthquakes occur per year, we need to use unique indexes. I have used bar plots for this analysis as they allow us to compare the number of earthquakes on a year-by-year basis.

-----------------------------------------------------------------------------------

Let's check the data frame to identify if any data is causing an error or not.


```python
# Check for missing years in the index
missing_years = df2.index.year[df2['Type'] == 'Earthquake'].value_counts().sort_index()

# Check if there are any missing years
if missing_years.empty:
    print("There are no missing years in the earthquake data.")
else:
    df2.loc[df2.index.year.isnull()]
```

```
##            Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                   
## NaT           8.017    124.075  Earthquake    623        5.6
## NaT         -32.998    -71.766  Earthquake     33        5.6
## NaT          36.344    142.344  Earthquake     10        5.8
```

These three records are causing an error because we have set the date_time column as the index, and these records have improperly formatted values. Therefore, we need to delete these records and then recreate the index for the date_time column.


```python
df2 = df2.reset_index().dropna().set_index('Date_Time')
plt.bar(df2.index.year.unique(),df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index())
plt.title('Earthquake per year')
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-18-5.png" width="672" />

Let's check how many Nuclear Explosions we have per year?


```python
plt.bar(df2[df2.Type == 'Nuclear Explosion'].index.year.unique(),
        df2.index.year[df2.Type == 'Nuclear Explosion'].value_counts().sort_index())
plt.title('Nukes per Year')
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-19-7.png" width="672" />

Scatter Plots - to see relationships and co-relation between different values.


```python
# Filter earthquake data and select relevant columns
earthquake_data = df2[df2['Type'] == 'Earthquake']
earthquake_data = earthquake_data[['Latitude', 'Longitude', 'Depth', 'Magnitude']]

# Create scatter plot
plt.figure(figsize=(10, 6))
plt.scatter(earthquake_data['Longitude'], earthquake_data['Latitude'], c=earthquake_data['Depth'], cmap='viridis', alpha=0.7)
plt.colorbar(label='Depth')
```

```
## <matplotlib.colorbar.Colorbar object at 0x0000029988451150>
```

```python
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.title('Earthquake Locations and Depths')
plt.grid(True)
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-20-9.png" width="960" />


```python
# Define the size of the figure
plt.figure(figsize=(12, 8))

# Create the scatter plot
plt.scatter(df2['Longitude'], df2['Latitude'], 
            s=df2['Magnitude'] * 50, 
            c=df2['Depth'], cmap='viridis', alpha=0.7)

# Add color bar to represent depth
plt.colorbar(label='Depth (km)')
```

```
## <matplotlib.colorbar.Colorbar object at 0x0000029989E7B590>
```

```python
# Set labels and title
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.title('Earthquake Locations')

# Show the plot
plt.grid(True)
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-21-11.png" width="1152" />

Depth of Each Earthquakes


```python
# Filter earthquake data
earthquake_data = df2[df2['Type'] == 'Earthquake']

# Define figure size
plt.figure(figsize=(12, 8))

# Create scatter plot
plt.scatter(earthquake_data.index.year, earthquake_data['Depth'], 
            s=earthquake_data['Magnitude'] * 10, 
            c='green', alpha=0.7)

# Add labels and title
plt.xlabel('Year')
plt.ylabel('Depth (km)')
plt.title('Depth of Earthquakes Over Time')

# Add legend for magnitude
for area in [10, 50, 100]:
    plt.scatter([], [], c='green', s=area, label=str(area))
plt.legend(scatterpoints=1, title='Magnitude')

# Show plot
plt.grid(True)
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-22-13.png" width="1152" />

