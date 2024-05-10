---
title: "App Management Analysis"
author: "Prachi Patel"
date: "2022-09-28"
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

This section is for the basic set up.
It will clear all the plots, the console and the workspace.
It also sets the overall format for numbers.



```r
if(!is.null(dev.list())) dev.off()
```

```
## null device 
##           1
```

```r
cat("\014") 
```



```r
rm(list=ls())
options(scipen=9)
```



```r
#Load packages
#For Excel
if(!require(readxl)){install.packages("readxl")}
```

```
## Loading required package: readxl
```

```r
library("readxl")
```

PART 1

1.  The following statement is made by your manager. Based on the examples and discussion in Lecture 1, transform it in to a question that can be answered with data analytics. Make sure you discuss the logic and reasoning you use to transform it.

People are downloading our app more and more but our current users are using the app less and less frequently.

Answer:

There are many reasons for the provided statement.
Such as, 1.
It will occurs because of poor management for the application.
2.
Application functionality is not convenient for the long term or to use in a timely manner.
3.
There is a chance of sudden drop-off of the application while using.
4.
The application functionality is useful for must users but there is a chance of not having user friendly user functionality or UI/UX.

To solve mentioned points, company can do a proper management and enhancement for the current application.
Company can do a survey for to identify the issues.
Company can improve UI/UX as well as security for their application and make it more convenient for the users to use on a timely manner.
They can create additional features to the application for current users to use their application more frequent.

2.  Consider the following three arrays of data. Each array is data for one customer of a streaming service. The numbers in the array represent the number of videos the customer streamed in a day (for example, customer A streamed 21 videos on the first day, 20 on the second and so on). Customer A: (21 20 19 18 21 20 18 22 20 18) Customer B: (22 19 18 21 27 21 22 19 21 24) Customer C: (9 10 8 11 8 7 10 11 7 10) Based on the data provided, answer the following questions. Make sure to provide evidence for your answers.

<!-- -->

a)  Which customer streams the least on a typical day?

-\>To define the least value for the provided data we can use the min function for the provided multiple vectors.
min(vector)


```r
Customer_A<- c(21,20,19,18,21,20,18,22,20,18)
Customer_B<- c(22,19,18,21,27,21,22,19,21,24)
Customer_C<- c(9,10,8,11,8,7,10,11,7,10)

min(c(Customer_A,Customer_B,Customer_C))
```

```
## [1] 7
```

b)  Which customer is the most inconsistent in the usage of the streaming service?

-\>To find the inconsistency, mean function will provide the value.
mean(vector)


```r
Customer_A<- c(21,20,19,18,21,20,18,22,20,18)
Customer_B<- c(22,19,18,21,27,21,22,19,21,24)
Customer_C<- c(9,10,8,11,8,7,10,11,7,10)

mean(c(Customer_A,Customer_B,Customer_C))
```

```
## [1] 16.73333
```

PART 2 :

[1].
Basic Manipulation

1.  Read in the excel file and change to a data frame.


```r
getwd()  # for verifying working directory
```

```
## [1] "C:/Users/patel/Documents/Prachi-Patel-Portfolio/content/project/App Management Analysis"
```

```r
Excel_PP <- read_excel("2014 and 2015 CSM 22F.xlsx")
Excel_PP <- as.data.frame(Excel_PP)
head(Excel_PP)
```

```
##                               Movie Year Ratings Genre     Gross    Budget
## 1                           13 Sins 2014     6.3     8      9130   4000000
## 2                    22 Jump Street 2014     7.1     1 192000000  50000000
## 3                    3 Days to Kill 2014     6.2     1  30700000  28000000
## 4            300: Rise of an Empire 2014     6.3     1 106000000 110000000
## 5                 A Haunted House 2 2014     4.7     8  17300000   3500000
## 6 A Million Ways to Die in the West 2014     6.1     8  42600000  40000000
##   Screens Sequel Sentiment   Views Likes Dislikes Comments Aggregate Followers
## 1      45      1         0 3280543  4632      425      636             1120000
## 2    3306      2         2  583289  3465       61      186            12350000
## 3    2872      1         0  304861   328       34       47              483000
## 4    3470      2         0  452917  2429      132      590              568000
## 5    2310      2         0 3145573 12163      610     1082             1923800
## 6    3158      1         0 3013011  9595      419     1020             8153000
```

2.  Append your initials to all variables in the data frame (Note -- you will need to do this in all your subsequent assignments).

-\>To append initials in the data frame we can use paste function to concatenate value and added \_ as a separater.


```r
#Append BDSA initials to column names
colnames(Excel_PP) <- paste(colnames(Excel_PP), "PP", sep = "_")
```

3.  What are the dimensions of the data set (rows and columns)?

-\>To get all the dimensions from the data set we have to use dim() function.
First element provides number of rows and the second element provides number of columns.


```r
dim(Excel_PP)
```

```
## [1] 187  14
```

[2].

Summarizing Data 1.
Means and Standard Deviations

a.  Calculate the mean and standard deviation for Gross.

To calculate mean, we have to use mean() function.
To calculate standard deviation, we have to use sd() function.


```r
mean(Excel_PP$Gross_PP)
```

```
## [1] 77646944
```

```r
sd(Excel_PP$Gross_PP)
```

```
## [1] 93899208
```

b.  Use the results above to calculate the coefficient of variation (rounded to 2 decimal places).

-\> To calculate the coefficient of variation we can divide the standard deviation with the mean value.
After that, we can round the value with 2 decimal points will round() function.


```r
mg<- mean(Excel_PP$Gross_PP)
sg<- sd(Excel_PP$Gross_PP)

cv<- sg/mg
round(cv,2)
```

```
## [1] 1.21
```

c.  Calculate the mean and standard deviation for Budget. Also calculate the coefficient of variation (rounded to 2 decimal places).


```r
mean(Excel_PP$Budget_PP)
```

```
## [1] 53844373
```

```r
sd(Excel_PP$Budget_PP)
```

```
## [1] 57100689
```

```r
mb<- mean(Excel_PP$Budget_PP)
sb<- sd(Excel_PP$Budget_PP)

cv <- sb / mb
round(cv,2)
```

```
## [1] 1.06
```

d.  Does the budget or the gross sales of a movie have more variation?

-\> Based on data received using var() function.
Budget and Gross values have more variation.


```r
var(Excel_PP)
```

```
## Warning in var(Excel_PP): NAs introduced by coercion
```

```
##                        Movie_PP           Year_PP         Ratings_PP
## Movie_PP                     NA                NA                 NA
## Year_PP                      NA       0.206486114       -0.006532114
## Ratings_PP                   NA      -0.006532114        0.996137657
## Genre_PP                     NA      -0.053734690       -0.499350239
## Gross_PP                     NA 5069306.624115922 35308300.753406934
## Budget_PP                    NA 1775262.632396067 18028205.578172617
## Screens_PP                   NA     147.237306653      143.905065839
## Sequel_PP                    NA       0.055488471        0.130921166
## Sentiment_PP                 NA       0.555775976        0.889031683
## Views_PP                     NA  387530.928411247  -112913.686723017
## Likes_PP                     NA    1191.165229142     3126.346006555
## Dislikes_PP                  NA     134.073745041     -296.364544880
## Comments_PP                  NA      50.704531079      100.410574435
## Aggregate Followers_PP       NA -116881.651515152   369094.903714565
##                                  Genre_PP         Gross_PP        Budget_PP
## Movie_PP                               NA               NA               NA
## Year_PP                       -0.05373469          5069307          1775263
## Ratings_PP                    -0.49935024         35308301         18028206
## Genre_PP                      17.10844690        -78734037        -64858884
## Gross_PP               -78734037.32936576 8817061282170421 3775843855778270
## Budget_PP              -64858883.61278822 3775843855778270 3260488656757256
## Screens_PP                  -871.51909033      77489975267      48034256066
## Sequel_PP                     -1.03619688         41018337         27693879
## Sentiment_PP                   1.20418607        -28141562          1043029
## Views_PP                 -318639.25858203   58857173303556   13785937238320
## Likes_PP                   -6844.48148468     311013391984      -6037263120
## Dislikes_PP                 -193.34445978      14436368912       3437450257
## Comments_PP                -1768.46038181      40965596550      15479685009
## Aggregate Followers_PP     77138.12463343  136237635030487   45054358719593
##                              Screens_PP         Sequel_PP      Sentiment_PP
## Movie_PP                             NA                NA                NA
## Year_PP                        147.2373        0.05548847         0.5557760
## Ratings_PP                     143.9051        0.13092117         0.8890317
## Genre_PP                      -871.5191       -1.03619688         1.2041861
## Gross_PP               77489975266.9634 41018336.58214018 -28141561.9803347
## Budget_PP              48034256065.8525 27693878.50152378   1043029.4704157
## Screens_PP                 2077377.4176      404.82232189      -602.8739578
## Sequel_PP                      404.8223        1.11850957        -1.0337818
## Sentiment_PP                  -602.8740       -1.03378184        51.4328101
## Views_PP                1413979304.9865  -311571.79986775   1270965.0659249
## Likes_PP                   6049302.5728    -1304.07187626     11382.2799436
## Dislikes_PP                 442742.9096     -103.11919959       220.4684607
## Comments_PP                 906575.7182     -289.06376862      1284.7840262
## Aggregate Followers_PP  1495830280.5684  1167289.29521017  -3603098.9100684
##                                Views_PP         Likes_PP      Dislikes_PP
## Movie_PP                             NA               NA               NA
## Year_PP                        387530.9         1191.165         134.0737
## Ratings_PP                    -112913.7         3126.346        -296.3645
## Genre_PP                      -318639.3        -6844.481        -193.3445
## Gross_PP               58857173303556.1 311013391983.780 14436368911.8972
## Budget_PP              13785937238320.1  -6037263120.436  3437450256.9616
## Screens_PP                 1413979305.0      6049302.573      442742.9096
## Sequel_PP                     -311571.8        -1304.072        -103.1192
## Sentiment_PP                  1270965.1        11382.280         220.4685
## Views_PP               22511075377595.3  99200225335.090  4845152325.0543
## Likes_PP                  99200225335.1    835953856.676    17001330.8359
## Dislikes_PP                4845152325.1     17001330.836     1729808.4660
## Comments_PP               12617757685.6     90816442.094     2581598.2658
## Aggregate Followers_PP  3481677725545.1  11869505141.978   305387081.2977
##                              Comments_PP Aggregate Followers_PP
## Movie_PP                              NA                     NA
## Year_PP                         50.70453             -116881.65
## Ratings_PP                     100.41057              369094.90
## Genre_PP                     -1768.46038               77138.12
## Gross_PP               40965596549.64465     136237635030487.05
## Budget_PP              15479685008.62458      45054358719593.48
## Screens_PP                  906575.71825          1495830280.57
## Sequel_PP                     -289.06377             1167289.30
## Sentiment_PP                  1284.78403            -3603098.91
## Views_PP               12617757685.59801       3481677725545.06
## Likes_PP                  90816442.09436         11869505141.98
## Dislikes_PP                2581598.26583           305387081.30
## Comments_PP               12097462.45673           600348631.19
## Aggregate Followers_PP   600348631.18524      24747035973720.31
```

2.  Calculate the 32nd percentile of the number of Likes given. This calculation should be rounded to the nearest whole number (no decimal places).

-\> For this, we can use quantile function to calculate percentile function with the provided ratio.
quantile(table_name\$column_name,provided ratio value)


```r
PercentileOfLikes <- quantile(Excel_PP$Likes_PP, c(.32))
round(PercentileOfLikes)
```

```
##  32% 
## 3355
```

[3].

Organizing Data

1.  Summary Table

<!-- -->

a.  Create a table showing average rating by year. This should be rounded to two decimal places.


```r
AvgRateY_PP <- aggregate(Excel_PP$Ratings_PP, by=list(Excel_PP$Year_PP), FUN=mean, na.rm=TRUE)
AvgRateY_PP
```

```
##   Group.1        x
## 1    2014 6.435338
## 2    2015 6.403704
```

```r
round(AvgRateY_PP,2)
```

```
##   Group.1    x
## 1    2014 6.44
## 2    2015 6.40
```

b.  Which year's movies have the highest rating? What is it?

Based on data received from the aggregate function, we can say that 2014 has highest rating as 6.44.

2.  Cross Tabulation

<!-- -->

a.  Create a table counting all genres of movies and which sequel number it is.


```r
table(Excel_PP$Genre_PP,Excel_PP$Sequel_PP)
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

b.  Change the table to show the percentage of each genre that is the 1st, 2nd, etc. movie in the series. These should be rounded to two decimal places.


```r
A <- with(Excel_PP, table(Genre_PP,Sequel_PP))
round(prop.table(A,1)*100,2)
```

```
##         Sequel_PP
## Genre_PP      1      2      3      4      5      6      7
##       1   65.45  18.18   3.64   5.45   3.64   0.00   3.64
##       2   45.45   9.09  27.27   0.00   9.09   9.09   0.00
##       3   89.19   5.41   2.70   0.00   2.70   0.00   0.00
##       6  100.00   0.00   0.00   0.00   0.00   0.00   0.00
##       7  100.00   0.00   0.00   0.00   0.00   0.00   0.00
##       8   79.07  18.60   2.33   0.00   0.00   0.00   0.00
##       9  100.00   0.00   0.00   0.00   0.00   0.00   0.00
##       10  88.89  11.11   0.00   0.00   0.00   0.00   0.00
##       12  75.00  16.67   8.33   0.00   0.00   0.00   0.00
##       15 100.00   0.00   0.00   0.00   0.00   0.00   0.00
```

c.  What percentage of movies in genre number 8 are not sequel?

-\> Approx, 77% of number 8 genre are not sequel.


```r
A <- with(Excel_PP, table(Genre_PP,Sequel_PP))
round(prop.table(A)*100,2)
```

```
##         Sequel_PP
## Genre_PP     1     2     3     4     5     6     7
##       1  19.25  5.35  1.07  1.60  1.07  0.00  1.07
##       2   2.67  0.53  1.60  0.00  0.53  0.53  0.00
##       3  17.65  1.07  0.53  0.00  0.53  0.00  0.00
##       6   1.07  0.00  0.00  0.00  0.00  0.00  0.00
##       7   0.53  0.00  0.00  0.00  0.00  0.00  0.00
##       8  18.18  4.28  0.53  0.00  0.00  0.00  0.00
##       9   5.35  0.00  0.00  0.00  0.00  0.00  0.00
##       10  4.28  0.53  0.00  0.00  0.00  0.00  0.00
##       12  4.81  1.07  0.53  0.00  0.00  0.00  0.00
##       15  3.74  0.00  0.00  0.00  0.00  0.00  0.00
```

3.  Bar Plot

<!-- -->

a.  Create a bar plot of genre of movies.
b.  The plot should be:
c.  Rank ordered by highest count of genre.\

<!-- -->

ii. Properly labeled (title, x-axis, etc)
iii. The bars should have a different colour than the one shown in class.

-\> To show BarPlot, we can use barplot function.
To add the value in the descending order we have to add the decreasing = FALSE in the order() function.


```r
genreBar_PP <- table(Excel_PP$Genre_PP)
Genre_PP <- genreBar_PP[order(genreBar_PP,decreasing=TRUE)]
barplot(Genre_PP,
        col = c("#eb8060", "#b9e38d", "#a1e9f0", "#d9b1f0","#A8FF33","#FFFC33","#9B9A8F","#B82759","#27B8A6","#4827B8"),
        density = 25, angle = 45,
        main="Bar Plot - Number of Genres",
        xlab="Genres")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-17-1.png" width="672" />

c.  Based on the bar plot, (approximately) how many movies are there in genre number 8?

-\> As per the bar plot graph, we can say that 43 movies are there in the number 8 genre.

4.  Histogram

<!-- -->

a.  Create a histogram of sentiment.
b.  The plot should be properly labeled and a unique colour.

-\> For histogram, we use hist() function.
we can add unique colours by adding color name or Hex codes in the col value.
Also, to label the axis we can use xlab and ylab for the labeling.


```r
hist(Excel_PP$Sentiment_PP,
     col=c("#eb8060", "#b9e38d", "#a1e9f0", "#d9b1f0","#A8FF33","#FFFC33","#9B9A8F","#B82759","#27B8A6","#4827B8"),
     density = 30, angle = 140,
     xlab="sentiment",
     main="Histogram - sentiments for movies")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-18-1.png" width="672" />

c.  Which range of sentiment is the most common?

-\> Based on the histogram, we can say that the most common range is from -10 to 0.

5.  Box plot

<!-- -->

a.  Create a horizontal box plot of number of screens the movies were shown on.
b.  The plot should be properly labeled and a unique colour.

-\> For the Box Plot, we use boxplot() function.
To create the horizontal box plot we have to use horizontal=TRUE inside the boxplot() function.
For labeling, we can use xlab and ylab values inside the boxplot() function.
To change color, we have to define color in the col filed inside the boxplot().


```r
boxplot(Excel_PP$Screens_PP, 
        main="Distribution of movie screens",
        xlab="Percentage of movies on screens",
        col=c("#27B8A6"),
        horizontal=TRUE,
        pch=20)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-19-1.png" width="672" />

c.  Based on the box plot, approximately how many movies were on fewer than 775 screens?

We can say approximately 50 movies has screens less than 775.

6.  Scatter Plot

<!-- -->

a.  Create a scatter plot comparing budget and gross sales.
b.  The plot should be properly labeled with a marker type different than the one demonstrated in class.
c.  Add a line at 45 degrees to the chart.

-\> To create a Scatter plot, we can use plot() function and add budget and gross field in the function then add the main source(table) name.
To label the plot we can use xlab and ylab and define the values.
For adding the line at 45 degree, we use abline() function.


```r
plot(Budget_PP ~ Gross_PP,
     data=Excel_PP,
     col=c("#B82759"),
     pch=25,
     main="Scatterplot - Budget & Gross",
     xlab="Budget",
     ylab="Gross")
#abline(coef = c(0,1))
abline(0, 1, col='blue')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-20-1.png" width="672" />

d.  Does there appear to be an association between budget and gross sales for movies?

-\>Based on the scatterplot, we can say that there is an association between budget and gross sales for movies.
Because Gross value is also increasing when budget is increased.

e.  What does it mean if a movie is plotted below the line?

-\>Below line pointer will have more budget but less gross value.
