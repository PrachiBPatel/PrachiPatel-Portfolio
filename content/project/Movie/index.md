---
title: "Movie Data Analysis"
author: "Prachi Patel"
date: "2022-09-28"
output:
  html_document:
    df_print: paged
  pdf_document: default
editor_options: 
  markdown: 
    wrap: sentence
---

### Introduction:

The dataset given containing data of the movie from year 2014 and 2015 including Movie, Year, Ratings, Genre, Gross, Budget, Screens, Sequel, Sentiment, Views, Likes, Dislikes, Comments, Aggregate Followers. The aim of this project is to analyse the data to dig deep into data and know if there are any patters available or not?

----------------------------------------------------------

#### Data Dictionary


|Variable            |Description                                             |
|:-------------------|:-------------------------------------------------------|
|Movie               |Movie Title                                             |
|Year                |Year of Movie Release                                   |
|Ratings             |Average Rating                                          |
|Genre               |Genre Code                                              |
|Gross               |Gross Sales (USD)                                       |
|Budget              |Budget (USD)                                            |
|Screens             |Nbr of Screens showing                                  |
|Sequel              |Sequel (1=Original Movie, 2=Sequel, 3=2nd Sequel, etc.) |
|Sentiment           |Gross Social Media Sentiment about the movie            |
|Views               |Number of Views of Reviews                              |
|Likes               |Related to Reviews                                      |
|Dislikes            |Related to Reviews                                      |
|Comments            |Number of Comments made by users                        |
|Aggregate Followers |Users following social postings on the movie            |

#### Initial Setup

This section is for the basic set up.
It will clear all the plots, the console and the workspace.
It also sets the overall format for numbers.


```r
if(!is.null(dev.list())) dev.off()
cat("\014") 
rm(list=ls())
options(scipen=9)
```

------------------------------------------------------------------------

### Basic Manipulation

This code chunk is to read in the excel file and convert it into a data frame.


```r
getwd()  # for verifying working directory
Excel <- read.csv("2014 and 2015 CSM 22F.csv")
Excel <- as.data.frame(Excel)
```

Read data set to view first few rows with the header(column name).


```r
head(Excel)
```

```
##                               Movie Year Ratings Genre     Gross    Budget
## 1                           13 Sins 2014     6.3     8      9130   4000000
## 2                    22 Jump Street 2014     7.1     1 192000000  50000000
## 3                    3 Days to Kill 2014     6.2     1  30700000  28000000
## 4            300: Rise of an Empire 2014     6.3     1 106000000 110000000
## 5                 A Haunted House 2 2014     4.7     8  17300000   3500000
## 6 A Million Ways to Die in the West 2014     6.1     8  42600000  40000000
##   Screens Sequel Sentiment   Views Likes Dislikes Comments Aggregate.Followers
## 1      45      1         0 3280543  4632      425      636             1120000
## 2    3306      2         2  583289  3465       61      186            12350000
## 3    2872      1         0  304861   328       34       47              483000
## 4    3470      2         0  452917  2429      132      590              568000
## 5    2310      2         0 3145573 12163      610     1082             1923800
## 6    3158      1         0 3013011  9595      419     1020             8153000
```

To get all the dimensions from the data set we have used ncol() and nrow() function.
We can use dim() function as well to get the dimension of the data set.

First function provides number of rows and the second function provides number of columns.


```r
ncol(Excel)
```

```
## [1] 14
```

```r
nrow(Excel)
```

```
## [1] 187
```

#### Summarizing Data.

> Means and Standard Deviations

To calculate mean, we have to use mean() function.
To calculate standard deviation, we have to use sd() function.

Used the results above to calculated the coefficient of variation.

To calculate the coefficient of variation I have to divide the standard deviation with the mean value.

The purpose of using the round() function with 2 decimal points is to simplify and make the number more suitable for practical use or presentation.


```r
mg<- mean(Excel$Gross)
sg<- sd(Excel$Gross)

cv<- sg/mg
round(cv,2)
```

```
## [1] 1.21
```

I calculated the mean and standard deviation for the budget and then found the coefficient of variation, rounding it to 2 decimal places.


```r
mb<- mean(Excel$Budget)
sb<- sd(Excel$Budget)

cv <- sb / mb
round(cv,2)
```

```
## [1] 1.06
```

Adding this step lets me see which changes more: the budget or the gross sales of a movie.


```r
var(Excel)
```

```
## Warning in var(Excel): NAs introduced by coercion
```

```
##                     Movie              Year            Ratings
## Movie                  NA                NA                 NA
## Year                   NA       0.206486114       -0.006532114
## Ratings                NA      -0.006532114        0.996137657
## Genre                  NA      -0.053734690       -0.499350239
## Gross                  NA 5069306.624115922 35308300.753406934
## Budget                 NA 1775262.632396067 18028205.578172617
## Screens                NA     147.237306653      143.905065839
## Sequel                 NA       0.055488471        0.130921166
## Sentiment              NA       0.555775976        0.889031683
## Views                  NA  387530.928411247  -112913.686723017
## Likes                  NA    1191.165229142     3126.346006555
## Dislikes               NA     134.073745041     -296.364544880
## Comments               NA      50.704531079      100.410574435
## Aggregate.Followers    NA -116881.651515152   369094.903714565
##                                  Genre            Gross           Budget
## Movie                               NA               NA               NA
## Year                       -0.05373469          5069307          1775263
## Ratings                    -0.49935024         35308301         18028206
## Genre                      17.10844690        -78734037        -64858884
## Gross               -78734037.32936576 8817061282170421 3775843855778270
## Budget              -64858883.61278822 3775843855778270 3260488656757256
## Screens                  -871.51909033      77489975267      48034256066
## Sequel                     -1.03619688         41018337         27693879
## Sentiment                   1.20418607        -28141562          1043029
## Views                 -318639.25858203   58857173303556   13785937238320
## Likes                   -6844.48148468     311013391984      -6037263120
## Dislikes                 -193.34445978      14436368912       3437450257
## Comments                -1768.46038181      40965596550      15479685009
## Aggregate.Followers     77138.12463343  136237635030487   45054358719593
##                              Screens            Sequel         Sentiment
## Movie                             NA                NA                NA
## Year                        147.2373        0.05548847         0.5557760
## Ratings                     143.9051        0.13092117         0.8890317
## Genre                      -871.5191       -1.03619688         1.2041861
## Gross               77489975266.9634 41018336.58214018 -28141561.9803347
## Budget              48034256065.8525 27693878.50152378   1043029.4704157
## Screens                 2077377.4176      404.82232189      -602.8739578
## Sequel                      404.8223        1.11850957        -1.0337818
## Sentiment                  -602.8740       -1.03378184        51.4328101
## Views                1413979304.9865  -311571.79986775   1270965.0659249
## Likes                   6049302.5728    -1304.07187626     11382.2799436
## Dislikes                 442742.9096     -103.11919959       220.4684607
## Comments                 906575.7182     -289.06376862      1284.7840262
## Aggregate.Followers  1495830280.5684  1167289.29521017  -3603098.9100684
##                                Views            Likes         Dislikes
## Movie                             NA               NA               NA
## Year                        387530.9         1191.165         134.0737
## Ratings                    -112913.7         3126.346        -296.3645
## Genre                      -318639.3        -6844.481        -193.3445
## Gross               58857173303556.1 311013391983.780 14436368911.8972
## Budget              13785937238320.1  -6037263120.436  3437450256.9616
## Screens                 1413979305.0      6049302.573      442742.9096
## Sequel                     -311571.8        -1304.072        -103.1192
## Sentiment                  1270965.1        11382.280         220.4685
## Views               22511075377595.3  99200225335.090  4845152325.0543
## Likes                  99200225335.1    835953856.676    17001330.8359
## Dislikes                4845152325.1     17001330.836     1729808.4660
## Comments               12617757685.6     90816442.094     2581598.2658
## Aggregate.Followers  3481677725545.1  11869505141.978   305387081.2977
##                              Comments Aggregate.Followers
## Movie                              NA                  NA
## Year                         50.70453          -116881.65
## Ratings                     100.41057           369094.90
## Genre                     -1768.46038            77138.12
## Gross               40965596549.64465  136237635030487.05
## Budget              15479685008.62458   45054358719593.48
## Screens                  906575.71825       1495830280.57
## Sequel                     -289.06377          1167289.30
## Sentiment                  1284.78403         -3603098.91
## Views               12617757685.59801    3481677725545.06
## Likes                  90816442.09436      11869505141.98
## Dislikes                2581598.26583        305387081.30
## Comments               12097462.45673        600348631.19
## Aggregate.Followers   600348631.18524   24747035973720.31
```

Based on the data from the `var()` function, it's clear that both the budget and gross sales values have more differences.

------------------------------------------------------------------------

Adding this step helps me to find the middle value where 32% of the likes given fall below it, providing insight into the distribution of likes.

For this, I can use quantile function to calculate percentile function with the provided ratio.

quantile(table_name\$column_name,provided ratio value)


```r
PercentileOfLikes <- quantile(Excel$Likes, c(.32))
round(PercentileOfLikes)
```

```
##  32% 
## 3355
```

------------------------------------------------------------------------

### Organizing Data

> Summary Table

Created a table that shows average rating by year and rounded to two decimal places.


```r
AvgRateY <- aggregate(Excel$Ratings, by=list(Excel$Year), FUN=mean, na.rm=TRUE)
AvgRateY
```

```
##   Group.1        x
## 1    2014 6.435338
## 2    2015 6.403704
```

```r
round(AvgRateY,2)
```

```
##   Group.1    x
## 1    2014 6.44
## 2    2015 6.40
```

Based on data received from the aggregate function, we can say that 2014 has highest rating as 6.44.

> Cross Tabulation

Now Let's create a table counting all genres of movies and which sequel number it is.


```r
table(Excel$Genre,Excel$Sequel)
```

```
##     
##       1  2  3  4  5  6  7
##   1  36 10  2  3  2  0  2
##   2   5  1  3  0  1  1  0
##   3  33  2  1  0  1  0  0
##   6   2  0  0  0  0  0  0
##   7   1  0  0  0  0  0  0
##   8  34  8  1  0  0  0  0
##   9  10  0  0  0  0  0  0
##   10  8  1  0  0  0  0  0
##   12  9  2  1  0  0  0  0
##   15  7  0  0  0  0  0  0
```

Changed the table to show the percentage of each genre that is the 1st, 2nd and so on, movie in the series.
Also, rounded to two decimal places.


```r
A <- with(Excel, table(Genre,Sequel))
round(prop.table(A,1)*100,2)
```

```
##      Sequel
## Genre      1      2      3      4      5      6      7
##    1   65.45  18.18   3.64   5.45   3.64   0.00   3.64
##    2   45.45   9.09  27.27   0.00   9.09   9.09   0.00
##    3   89.19   5.41   2.70   0.00   2.70   0.00   0.00
##    6  100.00   0.00   0.00   0.00   0.00   0.00   0.00
##    7  100.00   0.00   0.00   0.00   0.00   0.00   0.00
##    8   79.07  18.60   2.33   0.00   0.00   0.00   0.00
##    9  100.00   0.00   0.00   0.00   0.00   0.00   0.00
##    10  88.89  11.11   0.00   0.00   0.00   0.00   0.00
##    12  75.00  16.67   8.33   0.00   0.00   0.00   0.00
##    15 100.00   0.00   0.00   0.00   0.00   0.00   0.00
```

Percentage of movies in genre number 8 are sequel or not.

Approx, 77% of number 8 genre are not sequel.


```r
A <- with(Excel, table(Genre,Sequel))
round(prop.table(A)*100,2)
```

```
##      Sequel
## Genre     1     2     3     4     5     6     7
##    1  19.25  5.35  1.07  1.60  1.07  0.00  1.07
##    2   2.67  0.53  1.60  0.00  0.53  0.53  0.00
##    3  17.65  1.07  0.53  0.00  0.53  0.00  0.00
##    6   1.07  0.00  0.00  0.00  0.00  0.00  0.00
##    7   0.53  0.00  0.00  0.00  0.00  0.00  0.00
##    8  18.18  4.28  0.53  0.00  0.00  0.00  0.00
##    9   5.35  0.00  0.00  0.00  0.00  0.00  0.00
##    10  4.28  0.53  0.00  0.00  0.00  0.00  0.00
##    12  4.81  1.07  0.53  0.00  0.00  0.00  0.00
##    15  3.74  0.00  0.00  0.00  0.00  0.00  0.00
```

> Bar Plot

Created a bar plot of genre of movies.
The plot should be: rank ordered by highest count of genre.

Properly labeled (title, x-axis, etc)

To show BarPlot, I have used barplot function and added the value in the descending order using decreasing = FALSE in the order() function.


```r
genreBar <- table(Excel$Genre)
Genre <- genreBar[order(genreBar,decreasing=TRUE)]
barplot(Genre,
        col = c("#27B8A6"),
        density = 25, angle = 45,
        main="Bar Plot - Number of Genres",
        xlab="Genres")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-14-1.png" width="672" />

> Histogram

Now let's create a histogram of sentiment.

For histogram, we use hist() function and color Hex codes in the col value.
Also, to label the axis I have used xlab and ylab for labeling graph.


```r
hist(Excel$Sentiment,
     col=c("#27B8A6"),
     density = 30, angle = 140,
     xlab="sentiment",
     main="Histogram - sentiments for movies")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-1.png" width="672" />

> Box plot

Created a horizontal box plot of number of screens the movies were shown on.

For the Box Plot, I have used boxplot() function.For creating the horizontal box plot added horizontal=TRUE inside the boxplot() function.
For labeling, used xlab and ylab.


```r
boxplot(Excel$Screens, 
        main="Distribution of movie screens",
        xlab="Percentage of movies on screens",
        col=c("#27B8A6"),
        horizontal=TRUE,
        pch=20)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-16-1.png" width="672" />

> Scatter Plot

Created a scatter plot comparing budget and gross sales.

To create a Scatter plot, we used plot() function and add budget and gross field in the function then add the main source(table) name.
Also,added the line at 45 degree using abline() function to know association between budget and gross sales for movies.


```r
plot(Budget ~ Gross,
     data=Excel,
     col=c("#B82759"),
     pch=25,
     main="Scatterplot - Budget & Gross",
     xlab="Budget",
     ylab="Gross")
#abline(coef = c(0,1))
abline(0, 1, col='blue')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Based on the scatter plot, we can say that there is an association between budget and gross sales for movies.
As Gross value is increasing with the budget.

Also, we can say that below the line pointer moview plotted has more budget the gross value is lesser.
