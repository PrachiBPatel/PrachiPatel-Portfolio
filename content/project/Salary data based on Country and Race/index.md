---
title: "Salary data based on country and race"
author: "Prachi Patel"
date: "2024-03-28"
excerpt: "This project analyzes salary data to understand how income varies by country, race, and gender. Using data from Kaggle, we examine patterns and differences in salaries. Our goal is to identify any disparities and provide insights into salary distribution across different demographic groups."
output:
  html_document:
    df_print: paged
  pdf_document: default
editor_options: 
  markdown: 
    wrap: sentence
---

### Introduction

This project analyzes salary data to understand how income varies by country, race, and gender. Using data from Kaggle, we examine patterns and differences in salaries. Our goal is to identify any disparities and provide insights into salary distribution across different demographic groups

### Import Library

This section loads and attaches all the necessary libraries for this project.


```r
library(reticulate)
```



> Data Set Reference: Kaggle

### Importing Python Library


```python
import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
```

### Import Dataset


```python
df = pd.read_csv('Salary_Data_Based_country_and_race.csv')
```

### Read Dataset


```python
# It will return first few rows of the dataset.
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

#### Database Information


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

#### Dataset Details


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

As per the details mentioned above we can say that there are 9 columns and 6704 records.

Also, there are couple of null values and unnamed column available in the dataset.

---------------------------------------------------------------------------------------------------

Let's drop the unnamed column from the dataset.


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

Now let's check unique values available for each column and adjust if needed.


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


```python
# Unique Job Title List
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

Now, let's change Job Title value from large segment to smaller unique values. So, it will be easy to analyze.


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
#Unique values for Race field
df['Race'].unique()
```

```
## array(['White', 'Hispanic', 'Asian', 'Korean', 'Chinese', 'Australian',
##        'Welsh', 'African American', 'Mixed', 'Black'], dtype=object)
```


```python
# Number of data for Race field.
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

Creating a new data frame to see the percentage of each race data in out database.


```python
# Calculate the percentage of each race
race_percentage = df['Race'].value_counts(normalize=True) * 100

# Convert the series to a DataFrame
race_percentage_df = race_percentage.reset_index()
race_percentage_df.columns = ['Race', 'Percentage']

# Display the DataFrame
race_percentage_df
```

```
##                Race  Percentage
## 0             White   29.355609
## 1             Asian   23.911098
## 2            Korean    6.816826
## 3        Australian    6.742243
## 4           Chinese    6.622912
## 5             Black    6.518496
## 6  African American    5.280430
## 7             Mixed    4.982100
## 8             Welsh    4.967184
## 9          Hispanic    4.803103
```

Now, we will add the percentage(%) symbol into the percentage field.

First converted numeric datatype into the string.

After that, I have added the percentage(%) symbol into the percentage field.


```python
# Round the percentage values to two decimal places
race_percentage_df['Percentage'] = race_percentage_df['Percentage'].astype(float).round(2)

# Convert the percentage values to strings and add the percentage symbol
race_percentage_df['Percentage'] = race_percentage_df['Percentage'].astype(str) + '%'

# Display the DataFrame with the formatted percentage values
race_percentage_df
```

```
##                Race Percentage
## 0             White     29.36%
## 1             Asian     23.91%
## 2            Korean      6.82%
## 3        Australian      6.74%
## 4           Chinese      6.62%
## 5             Black      6.52%
## 6  African American      5.28%
## 7             Mixed      4.98%
## 8             Welsh      4.97%
## 9          Hispanic       4.8%
```

Making the Pie Plot for the race percentage data.

autopct='%.2f' is used to add the two decimal value in the field and add the percentage symbol into the Percentage Field.

y=0.94 is used to adjust the fine-tune the vertical alignment of the title.


```python
# Create a pie chart
plt.figure(figsize=(8, 8))  # Set the figure size
plt.pie(
    race_percentage,  # Data for the pie chart
    labels=race_percentage.index,  # Labels for each slice
    autopct='%.2f%%',  # Format the percentage values to two decimal places
    startangle=140  # Start angle for the first slice
)
```

```
## ([<matplotlib.patches.Wedge object at 0x000001CAA7995E10>, <matplotlib.patches.Wedge object at 0x000001CAA79E77D0>, <matplotlib.patches.Wedge object at 0x000001CAA79F5610>, <matplotlib.patches.Wedge object at 0x000001CAA79F4C90>, <matplotlib.patches.Wedge object at 0x000001CAA79FCED0>, <matplotlib.patches.Wedge object at 0x000001CAA79FEE50>, <matplotlib.patches.Wedge object at 0x000001CAA7A08990>, <matplotlib.patches.Wedge object at 0x000001CAA7A0A6D0>, <matplotlib.patches.Wedge object at 0x000001CAA7A14150>, <matplotlib.patches.Wedge object at 0x000001CAA79FEC50>], [Text(-1.072493477675293, -0.2444539636495102, 'White'), Text(0.3530411045439956, -1.0418070735516993, 'Asian'), Text(1.0575487878554573, -0.3026393254444195, 'Korean'), Text(1.088096237888774, 0.16138951977218494, 'Australian'), Text(0.9277920392832755, 0.5909331026796362, 'Chinese'), Text(0.612745305701961, 0.9135333548044159, 'Black'), Text(0.2402077844388794, 1.0734524769615863, 'African American'), Text(-0.11229257013235895, 1.0942533430120602, 'Mixed'), Text(-0.44333604109140684, 1.006704104823954, 'Welsh'), Text(-0.726786471721758, 0.8257005658967652, 'Hispanic')], [Text(-0.5849964423683416, -0.13333852562700554, '29.36%'), Text(0.19256787520581578, -0.5682584037554722, '23.91%'), Text(0.5768447933757039, -0.16507599569695608, '6.82%'), Text(0.5935070388484222, 0.0880306471484645, '6.74%'), Text(0.5060683850636047, 0.32232714691616515, '6.62%'), Text(0.3342247122010696, 0.49829092080240855, '6.52%'), Text(0.13102242787575238, 0.585519532888138, '5.28%'), Text(-0.06125049279946851, 0.59686545982476, '4.98%'), Text(-0.24181965877713096, 0.5491113299039748, '4.97%'), Text(-0.39642898457550435, 0.45038212685278095, '4.80%')])
```

```python
# Add a title with vertical alignment fine-tuned
plt.title('Race Percentage Distribution', y=0.94)

# Display the plot
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-16-1.png" width="768" />

Salary distribution based on the Race.

Based on the box plot we can see that there is not much difference with the distribution of all race.


```python
# Create a box plot for salary distribution based on race
plt.figure(figsize=(12, 8))
sns.boxplot(x='Race', y='Salary', data=df)

# Add a title to the plot
plt.title('Salary Distribution Based on Race')

# Display the plot
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-17-3.png" width="1152" />

**Mean of Salary based on Country**

Based on the graph we can say that there is not much difference in the salary based on each Country.


```python
# Calculate the mean salary for each country
mean_salary_by_country = df.groupby('Country')['Salary'].mean().reset_index()

# Rename the columns for clarity
mean_salary_by_country.columns = ['Country', 'Mean Salary']

# Sort the values by mean salary for better visualization
mean_salary_by_country = mean_salary_by_country.sort_values(by='Mean Salary')

# Create a bar plot for mean salary based on country
plt.figure(figsize=(14, 8))
sns.barplot(x='Mean Salary', y='Country', data=mean_salary_by_country, palette='viridis')

# Add a title to the plot
plt.title('Mean Salary Based on Country')

# Display the plot
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-18-5.png" width="1344" />

**Mean of Salary based on Race field**


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

plt.grid(axis='y') - this line is used to add the vertical line.

We must have to write this line after df.groupby.

Otherwise, this will not help to add the grid in the graph.

We can see that there is not much difference in the Salary based on Race.


```python
# Calculate the mean salary for each race
mean_salary_by_race = df.groupby('Race')['Salary'].mean().reset_index()

# Rename the columns for clarity
mean_salary_by_race.columns = ['Race', 'Mean Salary']

# Sort the values by mean salary for better visualization
mean_salary_by_race = mean_salary_by_race.sort_values(by='Mean Salary')

# Create a bar plot for mean salary based on race
plt.figure(figsize=(14, 8))
sns.barplot(x='Mean Salary', y='Race', data=mean_salary_by_race, palette='viridis')

# Add a title to the plot
plt.title('Mean Salary Based on Race')

# Add vertical grid lines
plt.grid(axis='x')

# Display the plot
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-20-7.png" width="1344" />

**Salary distribution based on Gender**

Here, `vert=False` is used to specify the boxes in horizontally figsize(12,7) is used to set the size of the figure.

Based on the box plot we can see that male gender have highest salary amount with the comparision of other gender.


```python
# Create a box plot for salary distribution based on gender
plt.figure(figsize=(12, 7))
sns.boxplot(x='Salary', y='Gender', data=df, orient='h')

# Add a title to the plot
plt.title('Salary Distribution Based on Gender')

# Display the plot
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-21-9.png" width="1152" />

**Mean of Salary based on Gender field**

Male gender salary amount is higher than other but there is not much difference between one another.


```python
# Calculate the mean salary for each gender
mean_salary_by_gender = df.groupby('Gender')['Salary'].mean().reset_index()

# Rename the columns for clarity
mean_salary_by_gender.columns = ['Gender', 'Mean Salary']

# Sort the values by mean salary for better visualization (optional)
mean_salary_by_gender = mean_salary_by_gender.sort_values(by='Mean Salary')

# Create a bar plot for mean salary based on gender
plt.figure(figsize=(10, 6))
sns.barplot(x='Gender', y='Mean Salary', data=mean_salary_by_gender, palette='viridis')

# Add a title to the plot
plt.title('Mean Salary Based on Gender')

# Add vertical grid lines
plt.grid(axis='y')

# Display the plot
plt.show()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-22-11.png" width="960" />

#### Conclusion

The analysis found that salaries are quite similar across different countries and races, showing no significant disparities. However, there is a slight difference in salaries based on gender, with males earning slightly more on average. Overall, the data suggests relative salary equality with some minor gender-based differences.
