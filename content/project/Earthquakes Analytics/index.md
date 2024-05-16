---
title: "Earthquake Analysis"
author: "Prachi Patel"
date: "2022-09-28"
output: pdf_document
---


```r
library(reticulate)
```

```
## Warning: package 'reticulate' was built under R version 4.2.3
```


```python
# !pip install matplot
```



```python
import numpy as np
import pandas as pd
```



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

if you add errors argument as coerce then it will make sure that invalid parsing will resulting as a null value for that specific record rather than just failing.


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


```python
df1['Datetime'][0]
```

```
## Timestamp('1965-01-02 13:44:18')
```

a Weekday as Sun, Mon

%A Weekday as full name as Sunday, Monday
%w Weekday as decimal no as 0,1,2...
%d Day of month as 01,02
%b Months as Jan, Feb
%B Months as January, February
%m Months as 01,02
%y Year without century as 11,12,13
%Y Year with century 2011,2012
%H 24 Hours clock from 00 to 23
%I 12 Hours clock from 01 to 12
%p AM, PM
%M Minutes from 00 to 59
%S Seconds from 00 to 59
%f Microseconds 6 decimal numbers


```python
df1['Datetime'][0].strftime('%A or %a and in %B')
```

```
## 'Saturday or Sat and in January'
```


```python
df1 = df1.set_index(['Datetime'])
df1.head()
```

```
##                            Date      Time  ...  Magnitude Source     Status
## Datetime                                   ...                             
## 1965-01-02 13:44:18  01/02/1965  13:44:18  ...            ISCGEM  Automatic
## 1965-01-04 11:29:49  01/04/1965  11:29:49  ...            ISCGEM  Automatic
## 1965-01-05 18:05:58  01/05/1965  18:05:58  ...            ISCGEM  Automatic
## 1965-01-08 18:49:43  01/08/1965  18:49:43  ...            ISCGEM  Automatic
## 1965-01-09 13:32:50  01/09/1965  13:32:50  ...            ISCGEM  Automatic
## 
## [5 rows x 21 columns]
```


```python
df1.dtypes
```

```
## Date                           object
## Time                           object
## Latitude                      float64
## Longitude                     float64
## Type                           object
## Depth                         float64
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


```python
df1['Depth'] = df1['Depth'].astype(int)
df1.dtypes
```

```
## Date                           object
## Time                           object
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

Read csv function has additional arguments like parse date, make an index.
Steps we have done above this to merge date and time column and make that column as a index column.
We can do the same process in the line of code while adding the csv file in to the jupyter notebook.


```python
df1 = pd.read_csv("earthquakes-dataset.csv",index_col=0, parse_dates=[['Date','Time']])
```

```
## <string>:1: FutureWarning: Support for nested sequences for 'parse_dates' in pd.read_csv is deprecated. Combine the desired columns with pd.to_datetime after parsing instead.
```

```python
df1.head()
```

```
##                      Latitude  Longitude  ... Magnitude Source     Status
## Date_Time                                 ...                            
## 01/02/1965 13:44:18    19.246    145.616  ...           ISCGEM  Automatic
## 01/04/1965 11:29:49     1.863    127.352  ...           ISCGEM  Automatic
## 01/05/1965 18:05:58   -20.579   -173.972  ...           ISCGEM  Automatic
## 01/08/1965 18:49:43   -59.076    -23.557  ...           ISCGEM  Automatic
## 01/09/1965 13:32:50    11.938    126.427  ...           ISCGEM  Automatic
## 
## [5 rows x 19 columns]
```


```python
df1.index
```

```
## Index(['01/02/1965 13:44:18', '01/04/1965 11:29:49', '01/05/1965 18:05:58',
##        '01/08/1965 18:49:43', '01/09/1965 13:32:50', '01/10/1965 13:36:32',
##        '01/12/1965 13:32:25', '01/15/1965 23:17:42', '01/16/1965 11:32:37',
##        '01/17/1965 10:43:17',
##        ...
##        '12/24/2016 03:58:55', '12/25/2016 14:22:27', '12/25/2016 14:32:13',
##        '12/27/2016 23:20:56', '12/28/2016 08:18:01', '12/28/2016 08:22:12',
##        '12/28/2016 09:13:47', '12/28/2016 12:38:51', '12/29/2016 22:30:19',
##        '12/30/2016 20:08:28'],
##       dtype='object', name='Date_Time', length=23412)
```


```python
df1.index = pd.to_datetime(df1.index, errors='coerce')
df1.index
```

```
## DatetimeIndex(['1965-01-02 13:44:18', '1965-01-04 11:29:49',
##                '1965-01-05 18:05:58', '1965-01-08 18:49:43',
##                '1965-01-09 13:32:50', '1965-01-10 13:36:32',
##                '1965-01-12 13:32:25', '1965-01-15 23:17:42',
##                '1965-01-16 11:32:37', '1965-01-17 10:43:17',
##                ...
##                '2016-12-24 03:58:55', '2016-12-25 14:22:27',
##                '2016-12-25 14:32:13', '2016-12-27 23:20:56',
##                '2016-12-28 08:18:01', '2016-12-28 08:22:12',
##                '2016-12-28 09:13:47', '2016-12-28 12:38:51',
##                '2016-12-29 22:30:19', '2016-12-30 20:08:28'],
##               dtype='datetime64[ns]', name='Date_Time', length=23412, freq=None)
```

info function to check the null values we have.


```python
df1.info()
```

```
## <class 'pandas.core.frame.DataFrame'>
## DatetimeIndex: 23412 entries, 1965-01-02 13:44:18 to 2016-12-30 20:08:28
## Data columns (total 19 columns):
##  #   Column                      Non-Null Count  Dtype  
## ---  ------                      --------------  -----  
##  0   Latitude                    23412 non-null  float64
##  1   Longitude                   23412 non-null  float64
##  2   Type                        23412 non-null  object 
##  3   Depth                       23412 non-null  float64
##  4   Depth Error                 4461 non-null   float64
##  5   Depth Seismic Stations      7097 non-null   float64
##  6   Magnitude                   23412 non-null  float64
##  7   Magnitude Type              23409 non-null  object 
##  8   Magnitude Error             327 non-null    float64
##  9   Magnitude Seismic Stations  2564 non-null   float64
##  10  Azimuthal Gap               7299 non-null   float64
##  11  Horizontal Distance         1604 non-null   float64
##  12  Horizontal Error            1156 non-null   float64
##  13  Root Mean Square            17352 non-null  float64
##  14  ID                          23412 non-null  object 
##  15  Source                      23412 non-null  object 
##  16  Location Source             23412 non-null  object 
##  17  Magnitude Source            23412 non-null  object 
##  18  Status                      23412 non-null  object 
## dtypes: float64(12), object(7)
## memory usage: 3.6+ MB
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


```python
df1.info()
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
##  3   Depth             23412 non-null  float64
##  4   Magnitude         23412 non-null  float64
##  5   ID                23412 non-null  object 
##  6   Source            23412 non-null  object 
##  7   Location Source   23412 non-null  object 
##  8   Magnitude Source  23412 non-null  object 
##  9   Status            23412 non-null  object 
## dtypes: float64(4), object(6)
## memory usage: 2.0+ MB
```

Creaded a new dataset having improtant columns such as latitude, longitude, type, depth and magnitude.


```python
df2 = df1[['Latitude','Longitude','Type','Depth','Magnitude']]
```

Sample is used to get the random records based on provided number of records.


```python
df2.sample(5)
```

```
##                      Latitude  Longitude        Type   Depth  Magnitude
## Date_Time                                                              
## 2005-07-13 00:29:30   10.3510    92.9030  Earthquake   49.60        5.5
## 1993-09-27 04:43:56   30.6780   132.1210  Earthquake   37.90        5.6
## 1974-05-17 20:55:11   -6.5130   106.8370  Earthquake  131.00        6.0
## 2016-11-13 14:01:02  -28.8598   -67.4611  Earthquake  108.78        5.7
## 2005-05-31 09:06:55  -62.8270   155.7760  Earthquake   10.00        5.7
```

Get the unique values for specific columns.


```python
df2.Type.unique()
```

```
## array(['Earthquake', 'Nuclear Explosion', 'Explosion', 'Rock Burst'],
##       dtype=object)
```

Biggest Earthquack in the last fifty years.


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
## 700.0
```


```python
df2[df2.Type == 'Earthquake'].Depth.min()
```

```
## -1.1
```

Value count for each value present in the type column.


```python
df2.Type.value_counts()
```

```
## Type
## Earthquake           23232
## Nuclear Explosion      175
## Explosion                4
## Rock Burst               1
## Name: count, dtype: int64
```

Average range of magnitude for the specified period of time.


```python
df2['Magnitude'][df2.Type == 'Earthquacke'].resample('1D').mean()
```

```
## Series([], Freq: D, Name: Magnitude, dtype: float64)
```

Check stand deviation.


```python
df2['Magnitude'][df2.Type == 'Earthquacke'].resample('1M').std()
```

```
## <string>:1: FutureWarning: 'M' is deprecated and will be removed in a future version, please use 'ME' instead.
## Series([], Freq: ME, Name: Magnitude, dtype: float64)
```


```python
df2.loc[df2['Magnitude'] == 9.1]
```

```
##                      Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                             
## 2004-12-26 00:58:53     3.295     95.982  Earthquake   30.0        9.1
## 2011-03-11 05:46:24    38.297    142.373  Earthquake   29.0        9.1
```


```python
df2.loc[df2['Depth'] == -1.1]
```

```
##                      Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                             
## 1992-06-28 12:00:45    34.131   -116.408  Earthquake   -1.1       5.77
```


```python
df2.index.year[df2.Type == 'Nuclear Explosion'].value_counts().sort_index()
```

```
## Date_Time
## 1966.0     1
## 1968.0     2
## 1969.0     1
## 1970.0     1
## 1973.0     6
## 1974.0     6
## 1975.0     9
## 1976.0    10
## 1977.0     8
## 1978.0    15
## 1979.0    13
## 1980.0    13
## 1981.0     8
## 1982.0     6
## 1983.0    10
## 1984.0    14
## 1985.0     6
## 1986.0     1
## 1987.0    16
## 1988.0    10
## 1989.0     7
## 1990.0     4
## 1991.0     1
## 1992.0     1
## 1993.0     1
## 1994.0     2
## 1995.0     2
## 1996.0     1
## Name: count, dtype: int64
```

To Install packages in Jupyter Notebook you can use conda or pip install.
Ex.,
conda install matplotlib
or
pip install matplotlib
or
!pip install matplotlib


```python
import matplotlib as mpl
import matplotlib.pyplot as plt
```

inline is used to enable static images of the plots into the notebook environment.


```python
'%matplotlib inlin'
```

```
## '%matplotlib inlin'
```


```python
plt.hist(df2['Magnitude'])
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-31-1.png" width="672" />


```python
plt.hist(df2['Magnitude'])
plt.xlabel('Magnitude')
plt.ylabel('Number of Earthquackes')
plt.title('1965-2016 Earthquackes')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-32-3.png" width="672" />

bins are the vuckets where the data is relied into.
Normally choose bins size between 5 to 10. But it also depends upon your database as well.


```python
plt.hist(df2['Magnitude'], bins = 10, edgecolor = 'black')
plt.xlabel('Magnitude')
plt.ylabel('Number of Earthquackes')
plt.title('1965-2016 Earthquackes')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-33-5.png" width="672" />


```python
plt.hist(df2['Magnitude'], bins=[6,6.1,6.2,6.3,6.4,6.5,6.6,6.7,6.8,6.9,7], edgecolor = 'black')
plt.xlabel('Magnitude')
plt.ylabel('Number of Earthquackes')
plt.title('1965-2016 Earthquackes')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-34-7.png" width="672" />


```python
plt.hist(df2['Magnitude'], bins=10, edgecolor = 'black', range = [6,7])
plt.xlabel('Magnitude')
plt.ylabel('Number of Earthquackes')
plt.title('1965-2016 Earthquackes')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-35-9.png" width="672" />

If you want the same graph with different bins value then you can create bins function and just change the value of bins using function instead of writing whole line of code again and again.


```python
def myplot(bins):
    plt.hist(df2['Magnitude'], bins = bins, edgecolor = 'black')
    plt.xlabel('Magnitude')
    plt.ylabel('Number of Earthquackes')
    plt.title('1965-2016 Earthquackes')
    
myplot(10)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-36-11.png" width="672" />


```python
myplot(20)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-37-13.png" width="672" />

Line Chart


```python
plt.plot(df2['Magnitude'][df2.Type == 'Earthquake'].resample('1Y').max())
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-38-15.png" width="672" />


```python
plt.plot(df2['Magnitude'][df2.Type == 'Earthquake'].resample('6M').max())
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-39-17.png" width="672" />

How many earthquakes we have per year?
For this we have to use unique indexes and here we are using bar plots because we are comparing number year by year.


```python
# plt.bar(df2.index.year.unique(),df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index())
# plt.title('Earthquake per year')
```

Let's check the data frame that which data is causing this error.


```python
df2.index.year.unique().shape
```

```
## (53,)
```


```python
df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index().shape
```

```
## (52,)
```


```python
# f2.index.year.isnull().sum()
```


```python
df2.loc[df2.index.year.isnull()]
```

```
##            Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                   
## NaT           8.017    124.075  Earthquake  623.0        5.6
## NaT         -32.998    -71.766  Earthquake   33.0        5.6
## NaT          36.344    142.344  Earthquake   10.1        5.8
```


```python
df2.loc[df2.index.year.isnull()]
```

```
##            Latitude  Longitude        Type  Depth  Magnitude
## Date_Time                                                   
## NaT           8.017    124.075  Earthquake  623.0        5.6
## NaT         -32.998    -71.766  Earthquake   33.0        5.6
## NaT          36.344    142.344  Earthquake   10.1        5.8
```

These three records are causing an error because we have make date time column as index and these three records have not proper formatted value.
so, first delete these values and then once again create an index for date_time column.


```python
df2 = df2.reset_index().dropna().set_index('Date_Time')
plt.bar(df2.index.year.unique(),df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index())
plt.title('Earthquake per year')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-46-19.png" width="672" />

How many Nuclear Explosions we have per year?


```python
plt.bar(df2[df2.Type == 'Nuclear Explosion'].index.year.unique(),
        df2.index.year[df2.Type == 'Nuclear Explosion'].value_counts().sort_index())
plt.title('Nukes per Year')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-47-21.png" width="672" />

Scatter Plots - to see relationships and co-relation between different values.


```python
#plt.scatter(df2[df2.Type == 'Earthquake'].index.year.unique(),
#        df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index())
#plt.title('Earthquake per Year')
```


```python
# plt.scatter(df2[df2.Type == 'Earthquake'].index.year.unique(),
#         df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index(), df2['Depth'], 'green', alpha=0.7)
```


```python
plt.scatter(df2['Magnitude'],df2['Depth'])
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-50-23.png" width="672" />


```python
plt.figure(figsize=(19,10))
plt.scatter(df2['Longitude'],df2['Latitude'],df2['Magnitude']*10,df2['Depth'])
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-51-25.png" width="1824" />

Depth of Each Earthquakes


```python
# plt.scatter(df2[df2.Type == 'Earthquake'].index.year.unique(),
#            df2.index.year[df2.Type == 'Earthquake'].value_counts().sort_index(),df2['Depth'],'green',alpha = 0.7)
# area = df2['Depth']
# for area in [10,50,100]:
#     plt.scatter([],[],c='k',s=area,label=str(area))
# plt.ledgend(scatterpoints=1, title='I am legend', loc = 'lower center')
```

