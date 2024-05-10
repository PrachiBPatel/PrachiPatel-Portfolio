---
title: "Salary data based on country and race Analysis"
author: "Prachi Patel"
date: "2024-03-28"
output: pdf_document
editor_options: 
  markdown: 
    wrap: sentence
# layout options: single or single-sidebar
layout: single
categories:
  - Theme Features
  - R
  - package
---





DataSet Reference: https://www.kaggle.com/datasets/sudheerp2147234/salary-dataset-based-on-country-and-race?resource=download
Importing library


```python
import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt
import warnings
sns.set_theme(color_codes=True)
warnings.filterwarnings('ignore')
```

Import Dataset


```python
df = pd.read_csv('Salary_Data_Based_country_and_race.csv')
```

Read Dataset


```python
df.head()
```

```
##    Unnamed: 0   Age  Gender  ...    Salary Country      Race
## 0           0  32.0    Male  ...   90000.0      UK     White
## 1           1  28.0  Female  ...   65000.0     USA  Hispanic
## 2           2  45.0    Male  ...  150000.0  Canada     White
## 3           3  36.0  Female  ...   60000.0     USA  Hispanic
## 4           4  52.0    Male  ...  200000.0     USA     Asian
## 
## [5 rows x 9 columns]
```

Database Information


```python
print('\033[92m'+'*' * 28)
```

```
## [92m****************************
```

```python
print('\033[92m\033[1m'+'.: Imported Dataset Info :.'+'\033[0m')
```

```
## [92m[1m.: Imported Dataset Info :.[0m
```

```python
print('\033[92m'+'*' * 28)
```

```
## [92m****************************
```

```python
print('Total Rows:', df.shape[0])
```

```
## Total Rows: 6704
```

```python
print('Total Columns:', df.shape[1])
```

```
## Total Columns: 9
```

Dataset Details


```python
print('\033[0m\033[92m'+'*' * 30+'\033[0m')
```

```
## [0m[92m******************************[0m
```

```python
print('\033[92m\033[1m'+'.: Dataset Details :.'+'\033[0m')
```

```
## [92m[1m.: Dataset Details :.[0m
```

```python
print('\033[0m\033[92m'+'*' * 30+'\033[0m')
```

```
## [0m[92m******************************[0m
```

```python
df.info()
```

```
## <class 'pandas.core.frame.DataFrame'>
## RangeIndex: 6704 entries, 0 to 6703
## Data columns (total 9 columns):
##  #   Column               Non-Null Count  Dtype  
## ---  ------               --------------  -----  
##  0   Unnamed: 0           6704 non-null   int64  
##  1   Age                  6702 non-null   float64
##  2   Gender               6702 non-null   object 
##  3   Education Level      6701 non-null   object 
##  4   Job Title            6702 non-null   object 
##  5   Years of Experience  6701 non-null   float64
##  6   Salary               6699 non-null   float64
##  7   Country              6704 non-null   object 
##  8   Race                 6704 non-null   object 
## dtypes: float64(3), int64(1), object(5)
## memory usage: 471.5+ KB
```

As per the details mentioned above we can say that thre are 9 columns and 6704 records.
Also, there are couple of null values available in the dataset.

Drop the unnamed column from the dataset.


```python
df.drop(columns=df.columns[0], axis=1,  inplace=True)
df.head()
```

```
##     Age  Gender Education Level  ...    Salary  Country      Race
## 0  32.0    Male      Bachelor's  ...   90000.0       UK     White
## 1  28.0  Female        Master's  ...   65000.0      USA  Hispanic
## 2  45.0    Male             PhD  ...  150000.0   Canada     White
## 3  36.0  Female      Bachelor's  ...   60000.0      USA  Hispanic
## 4  52.0    Male        Master's  ...  200000.0      USA     Asian
## 
## [5 rows x 8 columns]
```

Check unique values available for each column.


```python
print('\033[92m'+'*' * 42)
```

```
## [92m******************************************
```

```python
print('\033[92m\033[1m'+'.: Unique value count for each columns :.'+'\033[0m')
```

```
## [92m[1m.: Unique value count for each columns :.[0m
```

```python
print('\033[92m'+'*' * 42)
```

```
## [92m******************************************
```

```python
df.select_dtypes(include='object').nunique()
```

```
## Gender               3
## Education Level      7
## Job Title          193
## Country              5
## Race                10
## dtype: int64
```

Unique Job Title List


```python
df['Job Title'].unique()
```

```
## array(['Software Engineer', 'Data Analyst', 'Senior Manager',
##        'Sales Associate', 'Director', 'Marketing Analyst',
##        'Product Manager', 'Sales Manager', 'Marketing Coordinator',
##        'Senior Scientist', 'Software Developer', 'HR Manager',
##        'Financial Analyst', 'Project Manager', 'Customer Service Rep',
##        'Operations Manager', 'Marketing Manager', 'Senior Engineer',
##        'Data Entry Clerk', 'Sales Director', 'Business Analyst',
##        'VP of Operations', 'IT Support', 'Recruiter', 'Financial Manager',
##        'Social Media Specialist', 'Software Manager', 'Junior Developer',
##        'Senior Consultant', 'Product Designer', 'CEO', 'Accountant',
##        'Data Scientist', 'Marketing Specialist', 'Technical Writer',
##        'HR Generalist', 'Project Engineer', 'Customer Success Rep',
##        'Sales Executive', 'UX Designer', 'Operations Director',
##        'Network Engineer', 'Administrative Assistant',
##        'Strategy Consultant', 'Copywriter', 'Account Manager',
##        'Director of Marketing', 'Help Desk Analyst',
##        'Customer Service Manager', 'Business Intelligence Analyst',
##        'Event Coordinator', 'VP of Finance', 'Graphic Designer',
##        'UX Researcher', 'Social Media Manager', 'Director of Operations',
##        'Senior Data Scientist', 'Junior Accountant',
##        'Digital Marketing Manager', 'IT Manager',
##        'Customer Service Representative', 'Business Development Manager',
##        'Senior Financial Analyst', 'Web Developer', 'Research Director',
##        'Technical Support Specialist', 'Creative Director',
##        'Senior Software Engineer', 'Human Resources Director',
##        'Content Marketing Manager', 'Technical Recruiter',
##        'Sales Representative', 'Chief Technology Officer',
##        'Junior Designer', 'Financial Advisor', 'Junior Account Manager',
##        'Senior Project Manager', 'Principal Scientist',
##        'Supply Chain Manager', 'Senior Marketing Manager',
##        'Training Specialist', 'Research Scientist',
##        'Junior Software Developer', 'Public Relations Manager',
##        'Operations Analyst', 'Product Marketing Manager',
##        'Senior HR Manager', 'Junior Web Developer',
##        'Senior Project Coordinator', 'Chief Data Officer',
##        'Digital Content Producer', 'IT Support Specialist',
##        'Senior Marketing Analyst', 'Customer Success Manager',
##        'Senior Graphic Designer', 'Software Project Manager',
##        'Supply Chain Analyst', 'Senior Business Analyst',
##        'Junior Marketing Analyst', 'Office Manager', 'Principal Engineer',
##        'Junior HR Generalist', 'Senior Product Manager',
##        'Junior Operations Analyst', 'Senior HR Generalist',
##        'Sales Operations Manager', 'Senior Software Developer',
##        'Junior Web Designer', 'Senior Training Specialist',
##        'Senior Research Scientist', 'Junior Sales Representative',
##        'Junior Marketing Manager', 'Junior Data Analyst',
##        'Senior Product Marketing Manager', 'Junior Business Analyst',
##        'Senior Sales Manager', 'Junior Marketing Specialist',
##        'Junior Project Manager', 'Senior Accountant', 'Director of Sales',
##        'Junior Recruiter', 'Senior Business Development Manager',
##        'Senior Product Designer', 'Junior Customer Support Specialist',
##        'Senior IT Support Specialist', 'Junior Financial Analyst',
##        'Senior Operations Manager', 'Director of Human Resources',
##        'Junior Software Engineer', 'Senior Sales Representative',
##        'Director of Product Management', 'Junior Copywriter',
##        'Senior Marketing Coordinator', 'Senior Human Resources Manager',
##        'Junior Business Development Associate', 'Senior Account Manager',
##        'Senior Researcher', 'Junior HR Coordinator',
##        'Director of Finance', 'Junior Marketing Coordinator', nan,
##        'Junior Data Scientist', 'Senior Operations Analyst',
##        'Senior Human Resources Coordinator', 'Senior UX Designer',
##        'Junior Product Manager', 'Senior Marketing Specialist',
##        'Senior IT Project Manager', 'Senior Quality Assurance Analyst',
##        'Director of Sales and Marketing', 'Senior Account Executive',
##        'Director of Business Development', 'Junior Social Media Manager',
##        'Senior Human Resources Specialist', 'Senior Data Analyst',
##        'Director of Human Capital', 'Junior Advertising Coordinator',
##        'Junior UX Designer', 'Senior Marketing Director',
##        'Senior IT Consultant', 'Senior Financial Advisor',
##        'Junior Business Operations Analyst',
##        'Junior Social Media Specialist',
##        'Senior Product Development Manager', 'Junior Operations Manager',
##        'Senior Software Architect', 'Junior Research Scientist',
##        'Senior Financial Manager', 'Senior HR Specialist',
##        'Senior Data Engineer', 'Junior Operations Coordinator',
##        'Director of HR', 'Senior Operations Coordinator',
##        'Junior Financial Advisor', 'Director of Engineering',
##        'Software Engineer Manager', 'Back end Developer',
##        'Senior Project Engineer', 'Full Stack Engineer',
##        'Front end Developer', 'Developer', 'Front End Developer',
##        'Director of Data Science', 'Human Resources Coordinator',
##        'Junior Sales Associate', 'Human Resources Manager',
##        'Juniour HR Generalist', 'Juniour HR Coordinator',
##        'Digital Marketing Specialist', 'Receptionist',
##        'Marketing Director', 'Social M', 'Social Media Man',
##        'Delivery Driver'], dtype=object)
```

Changing Job Title value from large segment to smaller unique values.


```python
def categorize_job_title(job_title):
    job_title = str(job_title).lower()  # Convert to lowercase for case-insensitivity
    if 'software' in job_title or 'developer' in job_title:
        return 'Software/Developer'
    elif 'data' in job_title or 'analyst' in job_title or 'scientist' in job_title:
        return 'Data/Analyst/Scientist'
    elif 'manager' in job_title or 'director' in job_title or 'vp' in job_title:
        return 'Manager/Director/VP'
    elif 'sales' in job_title or 'representative' in job_title:
        return 'Sales'
    elif 'marketing' in job_title or 'social media' in job_title:
        return 'Marketing/ Social Media'
    elif 'product' in job_title or 'designer' in job_title:
        return 'Product/Designer'
    elif 'hr' in job_title or 'human resources' in job_title:
        return 'HR/Human Resources'
    elif 'financial' in job_title or 'accountant' in job_title:
        return 'Financial/Accountant'
    elif 'project manager' in job_title:
        return 'Project Manager'
    elif 'it' in job_title or 'support' in job_title:
        return 'IT/Technical Support'
    elif 'operations' in job_title or 'supply chain'in job_title:
        return 'Operations/Supply Chain'
    elif 'cusotmer service' in job_title or 'receptionist' in job_title:
        return 'Customer Service/Receptionist'
    else:
        return 'Other'

df['Job Title'] = df['Job Title'].apply(categorize_job_title)
```


```python
df.head()
```

```
##     Age  Gender Education Level  ...    Salary  Country      Race
## 0  32.0    Male      Bachelor's  ...   90000.0       UK     White
## 1  28.0  Female        Master's  ...   65000.0      USA  Hispanic
## 2  45.0    Male             PhD  ...  150000.0   Canada     White
## 3  36.0  Female      Bachelor's  ...   60000.0      USA  Hispanic
## 4  52.0    Male        Master's  ...  200000.0      USA     Asian
## 
## [5 rows x 8 columns]
```

Check the unique values doe Race field.


```python
print('\033[92m'+'*' * 35)
```

```
## [92m***********************************
```

```python
print('\033[92m\033[1m'+'.: Race field unique value list :.'+'\033[0m')
```

```
## [92m[1m.: Race field unique value list :.[0m
```

```python
print('\033[92m'+'*' * 35)
```

```
## [92m***********************************
```

```python
df['Race'].unique()
```

```
## array(['White', 'Hispanic', 'Asian', 'Korean', 'Chinese', 'Australian',
##        'Welsh', 'African American', 'Mixed', 'Black'], dtype=object)
```

Number of data for each Race value.


```python
print('\033[92m'+'*' * 42)
```

```
## [92m******************************************
```

```python
print('\033[92m\033[1m'+'.: Number of data for each Race value :.'+'\033[0m')
```

```
## [92m[1m.: Number of data for each Race value :.[0m
```

```python
print('\033[92m'+'*' * 42)
```

```
## [92m******************************************
```

```python
df['Race'].value_counts()
```

```
## Race
## White               1968
## Asian               1603
## Korean               457
## Australian           452
## Chinese              444
## Black                437
## African American     354
## Mixed                334
## Welsh                333
## Hispanic             322
## Name: count, dtype: int64
```

Creating a new dataframe to see the persentage of each race data in out database.


```python
race = df['Race'].value_counts().to_frame()
#race['Percentage'] = round((race['Race']/df.shape[0])*100,2)
print('\033[92m'+'*' * 38)
```

```
## [92m**************************************
```

```python
print('\033[92m\033[1m'+'.: Race value count with percentage :.'+'\033[0m')
```

```
## [92m[1m.: Race value count with percentage :.[0m
```

```python
print('\033[92m'+'*' * 38)
```

```
## [92m**************************************
```

```python
race
```

```
##                   count
## Race                   
## White              1968
## Asian              1603
## Korean              457
## Australian          452
## Chinese             444
## Black               437
## African American    354
## Mixed               334
## Welsh               333
## Hispanic            322
```

Now, we will add the percentage(%) symbol into the percentage field.
First converted numeric datatype into the string.
After that, I have added the percentage(%) symbol into the percentage field.


```python
#race['Percentage'] = race['Percentage'].astype(str)
#race['Percentage'] = race['Percentage']+'%'
print('\033[92m'+'*' * 38)
```

```
## [92m**************************************
```

```python
print('\033[92m\033[1m'+'.: Race value count with percentage :.'+'\033[0m')
```

```
## [92m[1m.: Race value count with percentage :.[0m
```

```python
print('\033[92m'+'*' * 38)
```

```
## [92m**************************************
```

```python
race
```

```
##                   count
## Race                   
## White              1968
## Asian              1603
## Korean              457
## Australian          452
## Chinese             444
## Black               437
## African American    354
## Mixed               334
## Welsh               333
## Hispanic            322
```

Making the Pie Plot for the race percentage data.
autopct='%.2f' is used to add the two decimal value in the field and add the percentage symbol into the Percentage Field.
y=0.94 is used to adjust the fine-tune the vertical alignment of the title.


```python
df['Race'].value_counts().plot(kind = 'pie',figsize = (12,6), autopct = '%.2f')
plt.title('Percentage of each Race', fontweight = 'bold', y = 0.94)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-17-1.png" width="1152" />

Salary distribution based on the Race.
Based on the box plot we can see that there is not much difference with the distribution of all race.


```python
salary_by_race = df[['Salary','Race']].boxplot(by='Race',vert=False, figsize = (12,7))
salary_by_race.axvline(df['Salary'][df['Race'] == 'White'].median(), color = 'red')
plt.grid(axis = 'y')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-18-3.png" width="1152" />

Mean of Salary based on Country.
Based on the graph we can say that there is not much difference in the salary based on each Country.


```python
df.groupby('Country')['Salary'].mean().plot(kind='bar', figsize = (12,6))
plt.title('Salary amount by Country', fontweight = 'bold', y = 0.94)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-19-5.png" width="1152" />

Mean of Salary based on Race field.


```python
print('\033[92m'+'*' * 38)
```

```
## [92m**************************************
```

```python
print('\033[92m\033[1m'+'.: Mean value of Salary by Race field :.'+'\033[0m')
```

```
## [92m[1m.: Mean value of Salary by Race field :.[0m
```

```python
print('\033[92m'+'*' * 38)
```

```
## [92m**************************************
```

```python
df.groupby('Race')['Salary'].mean()
```

```
## Race
## African American    112209.658192
## Asian               115302.068621
## Australian          114236.676991
## Black               118601.212815
## Chinese             111139.315315
## Hispanic            110410.139752
## Korean              117099.757112
## Mixed               116330.859281
## Welsh               112560.645646
## White               117071.055527
## Name: Salary, dtype: float64
```

Bar Plot based on Race and Salary mean value.
plt.grid(axis='y') - this line is used to add the vertical line. We must have to write this line after df.groupby.
Otherwise, this will not help to add the grid in the graph.

We can see that there is not much difference in the Salary based on Race.


```python
df.groupby('Race')['Salary'].mean().plot(kind='bar',figsize = (12,6))
plt.grid(axis='y')
plt.title('Salary amount by Race', fontweight = 'bold', y = 0.94)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-21-7.png" width="1152" />

Salary distribution based on Gender.
Here, vert=False is used to specify the boxes in horizontally
figsize(12,7) is used to set the size of the figure.

Based on the box plot we can see that male gender have highest salary amount with the comparision of other gender.


```python
df[['Salary','Gender']].boxplot(by='Gender', vert=False, figsize = (12,7))
plt.grid(axis = 'y')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-22-9.png" width="1152" />

Mean of Salary based on Gender field.
Male gender salary amount is higher than other but there is not much difference between one another.


```python
df.groupby('Gender')['Salary'].mean().plot(kind='bar',figsize = (12,6))
plt.title("Salary Amount's Mean Value by Gender" , fontweight = 'bold', y = 0.94)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-23-11.png" width="1152" />

