---
title: "Drug Trial Analysis"
subtitle: ""
author: "Prachi Patel"
date: "2022-10-14"
excerpt: ""
output:
  html_document:
    df_print: paged
  pdf_document: default
editor_options: 
  markdown: 
    wrap: sentence

---

### Introduction
Statistical Inference and Comparisons
500 patients participated in a new drug trial designed to increase the white blood cell count (WBC). One group of patients (“Treat”) were given the medication while the other (“Control”) were not. A number of variables were measured and recorded (including a survey they were asked to fill in). Appendix 1 contains the data dictionary for the data set. One group of respondents (“Treat”) were given additional education on political matters while the other (“Control”) were not.
    
# Data Dictionary

|Variable |Description                                                                    |
|:--------|:------------------------------------------------------------------------------|
|Del      |Time for delivery (in days, rounded to nearest 10th)                           |
|Vin      |Vintage of product (i.e. how long it has been in the warehouse).               |
|Pkg      |How many packages of product have been ordered                                 |
|Cst      |How many orders the customer has made in the past                              |
|Mil      |Distance the order needs to be delivered (in km)                               |
|Dom      |Indicator for if the product is manufactured in Canada (C) or elsewhere (I)    |
|Hazard   |Indicator for if the product is designated as Hazardous (H) or not (N).        |
|Car      |Indicator for which Carrier delivered the item (Fed Post, or M-Press Delivery) |

### Initial Setup

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

### Load Packages

This section loads and attaches all the necessary packages.


```r
if(!require(readxl)){install.packages("readxl")}
```

```
## Loading required package: readxl
```

```
## Warning: package 'readxl' was built under R version 4.2.3
```

```r
library("readxl")

if(!require(pastecs)){install.packages("pastecs")}
```

```
## Loading required package: pastecs
```

```
## Warning: package 'pastecs' was built under R version 4.2.3
```

```r
library("pastecs")

if(!require(lattice)){install.packages("lattice")}
```

```
## Loading required package: lattice
```

```
## Warning: package 'lattice' was built under R version 4.2.3
```

```r
library("lattice")

if(!require(ggplot2)){install.packages("ggplot2")}
```

```
## Loading required package: ggplot2
```

```
## Warning: package 'ggplot2' was built under R version 4.2.3
```

```r
library("ggplot2")

if(!require(tinytex)){install.packages("tinytex")}
```

```
## Loading required package: tinytex
```

```r
library("tinytex")
```

### Import Data



### Data Transformation and Preparation

> Initial Transformation

Transforming character variables to factor variables.
  Using factor function we can convert character variable to factor variable.
        

```r
ExcelFile$gender <- as.factor(ExcelFile$gender)
ExcelFile$HR <- as.factor(ExcelFile$HR)
ExcelFile$BP <- as.factor(ExcelFile$BP)
ExcelFile$Smoke <- as.factor(ExcelFile$Smoke)
ExcelFile$Drink <- as.factor(ExcelFile$Drink)
ExcelFile$Group <- as.factor(ExcelFile$Group)
head(ExcelFile)
```

```
##   ID gender   HR   BP  Wgt1  Wgt2 Exercise  Hgt Smoke Drink   Group  WBC Income
## 1  1 female Norm Norm 118.6 121.5      158 67.8     N     Y Control 5193 125000
## 2  2 female Norm Norm 143.1 146.6      152 65.9     N     Y Control 5705     NA
## 3  3 female Norm High 105.3 107.3      205 69.3     N     N    Test 7680     NA
## 4  4 female Norm Norm 119.5 120.9      151 65.4     N     Y Control 7342     NA
## 5  5 female Norm High 130.9 132.1      178 65.8     N     N Control 7714     NA
## 6  6 female Norm High  90.0  91.4      204 63.1     N     Y Control 3851     NA
```

> Reduce Dimensionality
  
Applying the Missing Value Filter to remove appropriate columns of data using summary function, summarized all data in the data source. 

Based on the summary data 492 values out of 500 is blank or null for Income field.
    

```r
summary(ExcelFile)
```

```
##        ID           gender       HR         BP           Wgt1      
##  Min.   :  1.0   female:500   High: 77   High: 94   Min.   : 49.2  
##  1st Qu.:125.8                Low : 18   Low : 34   1st Qu.:114.3  
##  Median :250.5                Norm:405   Norm:372   Median :131.7  
##  Mean   :250.5                                      Mean   :131.0  
##  3rd Qu.:375.2                                      3rd Qu.:149.6  
##  Max.   :500.0                                      Max.   :199.3  
##                                                                    
##       Wgt2          Exercise          Hgt        Smoke   Drink       Group    
##  Min.   : 50.2   Min.   : 67.0   Min.   :59.80   N:427   N:140   Control:250  
##  1st Qu.:115.2   1st Qu.:147.0   1st Qu.:65.00   Y: 73   Y:360   Test   :250  
##  Median :133.7   Median :175.5   Median :67.05                                
##  Mean   :132.9   Mean   :176.9   Mean   :67.01                                
##  3rd Qu.:151.6   3rd Qu.:207.0   3rd Qu.:69.00                                
##  Max.   :201.5   Max.   :297.0   Max.   :78.90                                
##                                                                               
##       WBC            Income      
##  Min.   : 3851   Min.   :  2000  
##  1st Qu.: 6195   1st Qu.:  7000  
##  Median : 6902   Median : 53500  
##  Mean   : 6896   Mean   : 66625  
##  3rd Qu.: 7532   3rd Qu.:128000  
##  Max.   :10652   Max.   :148000  
##                  NA's   :492
```

```r
ExcelFile <- ExcelFile[-c(13)]
summary(ExcelFile)
```

```
##        ID           gender       HR         BP           Wgt1      
##  Min.   :  1.0   female:500   High: 77   High: 94   Min.   : 49.2  
##  1st Qu.:125.8                Low : 18   Low : 34   1st Qu.:114.3  
##  Median :250.5                Norm:405   Norm:372   Median :131.7  
##  Mean   :250.5                                      Mean   :131.0  
##  3rd Qu.:375.2                                      3rd Qu.:149.6  
##  Max.   :500.0                                      Max.   :199.3  
##       Wgt2          Exercise          Hgt        Smoke   Drink       Group    
##  Min.   : 50.2   Min.   : 67.0   Min.   :59.80   N:427   N:140   Control:250  
##  1st Qu.:115.2   1st Qu.:147.0   1st Qu.:65.00   Y: 73   Y:360   Test   :250  
##  Median :133.7   Median :175.5   Median :67.05                                
##  Mean   :132.9   Mean   :176.9   Mean   :67.01                                
##  3rd Qu.:151.6   3rd Qu.:207.0   3rd Qu.:69.00                                
##  Max.   :201.5   Max.   :297.0   Max.   :78.90                                
##       WBC       
##  Min.   : 3851  
##  1st Qu.: 6195  
##  Median : 6902  
##  Mean   : 6896  
##  3rd Qu.: 7532  
##  Max.   :10652
```
  
Applying the Low Variance Filter to remove appropriate columns of data.

Removed Hgt column because Height's coef.var value is 0.04.
      

```r
stat.desc(ExcelFile)
```

```
##                          ID gender HR BP          Wgt1         Wgt2
## nbr.val         500.0000000     NA NA NA   500.0000000   500.000000
## nbr.null          0.0000000     NA NA NA     0.0000000     0.000000
## nbr.na            0.0000000     NA NA NA     0.0000000     0.000000
## min               1.0000000     NA NA NA    49.2000000    50.200000
## max             500.0000000     NA NA NA   199.3000000   201.500000
## range           499.0000000     NA NA NA   150.1000000   151.300000
## sum          125250.0000000     NA NA NA 65478.0000000 66452.900000
## median          250.5000000     NA NA NA   131.6500000   133.700000
## mean            250.5000000     NA NA NA   130.9560000   132.905800
## SE.mean           6.4614240     NA NA NA     1.1942880     1.198161
## CI.mean.0.95     12.6949496     NA NA NA     2.3464527     2.354061
## var           20875.0000000     NA NA NA   713.1618677   717.794415
## std.dev         144.4818328     NA NA NA    26.7050907    26.791686
## coef.var          0.5767738     NA NA NA     0.2039241     0.201584
##                   Exercise            Hgt Smoke Drink Group             WBC
## nbr.val        500.0000000   500.00000000    NA    NA    NA     500.0000000
## nbr.null         0.0000000     0.00000000    NA    NA    NA       0.0000000
## nbr.na           0.0000000     0.00000000    NA    NA    NA       0.0000000
## min             67.0000000    59.80000000    NA    NA    NA    3851.0000000
## max            297.0000000    78.90000000    NA    NA    NA   10652.0000000
## range          230.0000000    19.10000000    NA    NA    NA    6801.0000000
## sum          88440.0000000 33503.40000000    NA    NA    NA 3447776.0000000
## median         175.5000000    67.05000000    NA    NA    NA    6902.0000000
## mean           176.8800000    67.00680000    NA    NA    NA    6895.5520000
## SE.mean          1.8917697     0.13089697    NA    NA    NA      46.1807869
## CI.mean.0.95     3.7168156     0.25717712    NA    NA    NA      90.7327494
## var           1789.3963928     8.56700778    NA    NA    NA 1066332.5403768
## std.dev         42.3012576     2.92694513    NA    NA    NA    1032.6337881
## coef.var         0.2391523     0.04368131    NA    NA    NA       0.1497536
```

```r
summary(ExcelFile)
```

```
##        ID           gender       HR         BP           Wgt1      
##  Min.   :  1.0   female:500   High: 77   High: 94   Min.   : 49.2  
##  1st Qu.:125.8                Low : 18   Low : 34   1st Qu.:114.3  
##  Median :250.5                Norm:405   Norm:372   Median :131.7  
##  Mean   :250.5                                      Mean   :131.0  
##  3rd Qu.:375.2                                      3rd Qu.:149.6  
##  Max.   :500.0                                      Max.   :199.3  
##       Wgt2          Exercise          Hgt        Smoke   Drink       Group    
##  Min.   : 50.2   Min.   : 67.0   Min.   :59.80   N:427   N:140   Control:250  
##  1st Qu.:115.2   1st Qu.:147.0   1st Qu.:65.00   Y: 73   Y:360   Test   :250  
##  Median :133.7   Median :175.5   Median :67.05                                
##  Mean   :132.9   Mean   :176.9   Mean   :67.01                                
##  3rd Qu.:151.6   3rd Qu.:207.0   3rd Qu.:69.00                                
##  Max.   :201.5   Max.   :297.0   Max.   :78.90                                
##       WBC       
##  Min.   : 3851  
##  1st Qu.: 6195  
##  Median : 6902  
##  Mean   : 6896  
##  3rd Qu.: 7532  
##  Max.   :10652
```

```r
table(ExcelFile$Hgt)
```

```
## 
## 59.8 59.9 60.3 60.6 60.7 60.8 60.9 61.2 61.5 61.6 61.7 61.8 61.9 62.1 62.2 62.4 
##    1    1    1    1    1    2    3    2    1    2    2    1    4    2    2    1 
## 62.5 62.6 62.7 62.9   63 63.1 63.2 63.3 63.4 63.5 63.6 63.7 63.8 63.9   64 64.1 
##    1    1    2    4    4    3    6    3    4    3    4    2    3    9    2    5 
## 64.2 64.3 64.4 64.5 64.6 64.7 64.8   65 65.1 65.2 65.3 65.4 65.5 65.6 65.7 65.8 
##    5    4    7    8    7    7    3    3    6    9    8    1    4    8    5   11 
## 65.9   66 66.1 66.2 66.3 66.4 66.5 66.6 66.7 66.8 66.9   67 67.1 67.2 67.3 67.4 
##    6    7    7    8    4    6    5    5    5   10    3    5    9    8    4    6 
## 67.5 67.6 67.7 67.8 67.9   68 68.1 68.2 68.3 68.4 68.5 68.6 68.7 68.8 68.9   69 
##   10    8    7    7    9    3    6    2    5    4    9    5    5    8    7    5 
## 69.1 69.2 69.3 69.4 69.5 69.6 69.7 69.8 69.9   70 70.1 70.2 70.3 70.4 70.5 70.6 
##    4   11    5    7   12    2    4    4    5    4    2    2    4    6    1    1 
## 70.7 70.8 70.9   71 71.1 71.2 71.3 71.4 71.5 71.6 71.7 71.8 71.9 72.1 72.2 72.3 
##    3    3    3    2    1    3    2    2    2    2    2    2    1    2    2    2 
## 72.4 72.5 72.6 72.8 72.9   73 74.1 74.7 75.6 77.5 78.4 78.9 
##    3    1    1    1    1    1    1    2    1    1    1    1
```

```r
ExcelFile <- ExcelFile[-c(8)]
stat.desc(ExcelFile)
```

```
##                          ID gender HR BP          Wgt1         Wgt2
## nbr.val         500.0000000     NA NA NA   500.0000000   500.000000
## nbr.null          0.0000000     NA NA NA     0.0000000     0.000000
## nbr.na            0.0000000     NA NA NA     0.0000000     0.000000
## min               1.0000000     NA NA NA    49.2000000    50.200000
## max             500.0000000     NA NA NA   199.3000000   201.500000
## range           499.0000000     NA NA NA   150.1000000   151.300000
## sum          125250.0000000     NA NA NA 65478.0000000 66452.900000
## median          250.5000000     NA NA NA   131.6500000   133.700000
## mean            250.5000000     NA NA NA   130.9560000   132.905800
## SE.mean           6.4614240     NA NA NA     1.1942880     1.198161
## CI.mean.0.95     12.6949496     NA NA NA     2.3464527     2.354061
## var           20875.0000000     NA NA NA   713.1618677   717.794415
## std.dev         144.4818328     NA NA NA    26.7050907    26.791686
## coef.var          0.5767738     NA NA NA     0.2039241     0.201584
##                   Exercise Smoke Drink Group             WBC
## nbr.val        500.0000000    NA    NA    NA     500.0000000
## nbr.null         0.0000000    NA    NA    NA       0.0000000
## nbr.na           0.0000000    NA    NA    NA       0.0000000
## min             67.0000000    NA    NA    NA    3851.0000000
## max            297.0000000    NA    NA    NA   10652.0000000
## range          230.0000000    NA    NA    NA    6801.0000000
## sum          88440.0000000    NA    NA    NA 3447776.0000000
## median         175.5000000    NA    NA    NA    6902.0000000
## mean           176.8800000    NA    NA    NA    6895.5520000
## SE.mean          1.8917697    NA    NA    NA      46.1807869
## CI.mean.0.95     3.7168156    NA    NA    NA      90.7327494
## var           1789.3963928    NA    NA    NA 1066332.5403768
## std.dev         42.3012576    NA    NA    NA    1032.6337881
## coef.var         0.2391523    NA    NA    NA       0.1497536
```

```r
head(ExcelFile)
```

```
##   ID gender   HR   BP  Wgt1  Wgt2 Exercise Smoke Drink   Group  WBC
## 1  1 female Norm Norm 118.6 121.5      158     N     Y Control 5193
## 2  2 female Norm Norm 143.1 146.6      152     N     Y Control 5705
## 3  3 female Norm High 105.3 107.3      205     N     N    Test 7680
## 4  4 female Norm Norm 119.5 120.9      151     N     Y Control 7342
## 5  5 female Norm High 130.9 132.1      178     N     N Control 7714
## 6  6 female Norm High  90.0  91.4      204     N     Y Control 3851
```

Applying the High Correlation Filter to remove appropriate columns of data.

Removing Wgt2 fields because Wgt2's correlation is 0.99; which is almost same as 1 for Wgt1.
    

```r
cor(ExcelFile[sapply(ExcelFile, function(x) !is.factor(x))])
```

```
##                    ID        Wgt1         Wgt2    Exercise          WBC
## ID        1.000000000  0.15288257  0.157262222  0.03725986 -0.003826326
## Wgt1      0.152882567  1.00000000  0.999323559 -0.20273777  0.001588060
## Wgt2      0.157262222  0.99932356  1.000000000 -0.19986865  0.001131027
## Exercise  0.037259861 -0.20273777 -0.199868649  1.00000000  0.072994887
## WBC      -0.003826326  0.00158806  0.001131027  0.07299489  1.000000000
```

```r
ExcelFile <- ExcelFile[-c(6)]
head(ExcelFile)
```

```
##   ID gender   HR   BP  Wgt1 Exercise Smoke Drink   Group  WBC
## 1  1 female Norm Norm 118.6      158     N     Y Control 5193
## 2  2 female Norm Norm 143.1      152     N     Y Control 5705
## 3  3 female Norm High 105.3      205     N     N    Test 7680
## 4  4 female Norm Norm 119.5      151     N     Y Control 7342
## 5  5 female Norm High 130.9      178     N     N Control 7714
## 6  6 female Norm High  90.0      204     N     Y Control 3851
```
    
Dropping the ID and Gender fields because both columns data is not useful for the analytics. 

As ID is the row number for each field and for gender column all data are marked as Female.


```r
ExcelFile <- ExcelFile[-c(1:2)]
head(ExcelFile)
```

```
##     HR   BP  Wgt1 Exercise Smoke Drink   Group  WBC
## 1 Norm Norm 118.6      158     N     Y Control 5193
## 2 Norm Norm 143.1      152     N     Y Control 5705
## 3 Norm High 105.3      205     N     N    Test 7680
## 4 Norm Norm 119.5      151     N     Y Control 7342
## 5 Norm High 130.9      178     N     N Control 7714
## 6 Norm High  90.0      204     N     Y Control 3851
```

> Outliers
  
Creating box plots of all relevant variables (i.e. numeric, non-binary) to determine outliers.

Created box plot for Weight, Exercise, and White Blood Cell fields as remaining fields are non-numeric.
      

```r
#Weight Data

boxplot(ExcelFile$Wgt1, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Weight - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />

```r
densityplot( ~ ExcelFile$Wgt1, pch=6,col=c("#6bc9c2"), xlab = "Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-2.png" width="672" />

```r
#Exercise Data
boxplot(ExcelFile$Exercise, horizontal=TRUE,col=c("#6bc9c2"), pch=20, main = "Exercise - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-3.png" width="672" />

```r
densityplot( ~ ExcelFile$Exercise, pch=6,col=c("#6bc9c2"), xlab = "Exercise")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-4.png" width="672" />

```r
boxplot(ExcelFile$WBC, horizontal=TRUE,col=c("#6bc9c2"), pch=20, main = "White Blood Cell - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-5.png" width="672" />

```r
densityplot( ~ ExcelFile$WBC, pch=6,col=c("#6bc9c2"), xlab = "White Blood Cell")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-6.png" width="672" />
    
Commenting on any outliers you see and deal with them appropriately

Based on the previous box plots and density plots for Weight, Exercise and White Blood Cells fields; Weight and White Blood Cell fields has outlines.

For Weight, we have removed outliners data where value is less than 58.

For White blood cell, we have removed outliners data where value is less than 4100 and greater than 9700.
         

```r
#Code to delete outliner data where Weight value is less than 58.
nr <- which(ExcelFile$Wgt1 <58)
ExcelFile <- ExcelFile[-c(nr),]
boxplot(ExcelFile$Wgt1, horizontal=TRUE, col=c("#e8bbfa"), pch=20, main = "Weight - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-1.png" width="672" />

```r
densityplot( ~ ExcelFile$Wgt1, pch=6,col=c("#e8bbfa"), xlab = "Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-2.png" width="672" />

```r
#Code to delete outliner data where WBC value is less than 4100 and greater than 9700.
nr <- which(ExcelFile$WBC < 4100)
ExcelFile <- ExcelFile[-c(nr),]
nr <- which(ExcelFile$WBC > 9700)
ExcelFile <- ExcelFile[-c(nr),]
boxplot(ExcelFile$WBC, horizontal=TRUE, col=c("#e8bbfa"), pch=20, main = "White Blood Cell - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-3.png" width="672" />

```r
densityplot( ~ ExcelFile$WBC, pch=6,col=c("#e8bbfa"), xlab = "White Blood Cell")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-4.png" width="672" />
    
### Organizing Data

> Scatter Plots

Created a histogram for one of the Weight variables.
    

```r
histogram(~Wgt1, dat=ExcelFile, breaks=10,col = c("#6bc9c2"), type="density", xlab="Weight", main="Histogram of Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-11-1.png" width="672" />
    
Created a histogram for Exercise. 
    

```r
histogram(~Exercise, dat=ExcelFile, breaks=10,col = c("#6bc9c2"), type="density", xlab="Exercise", main="Histogram of Exercise")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-1.png" width="672" />

Created a scatter plot showing the relationship between Exercise and Weight. (note: Exercise should be on the x-axis, Weight should be the y-axis)
    

```r
plot(Wgt1 ~ Exercise,
     data=ExcelFile,
     col = c("#6bc9c2"),
     pch=25,
     main="Scatterplot - Exercise & Weight",
     xlab="Exercise",
     ylab="Weight")
abline(0, 1, col='blue')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-13-1.png" width="672" />

> Conclusion from the chart...

Based on the Scatter plot graph, we can say that exercise and weight data is not in a direct relationship as all data is show all over the graph.
      
Calculating a correlation coefficient between these two variables. 

Based on the correlation coefficient value, we can say that weight and exercise value are correlated to each other.
    

```r
ExcelFile$Wgt1 <- as.numeric(ExcelFile$Wgt1)
ExcelFile$Exercise <- as.numeric(ExcelFile$Exercise)
cor.test(ExcelFile$Wgt1,ExcelFile$Exercise, method="spearman")
```

```
## Warning in cor.test.default(ExcelFile$Wgt1, ExcelFile$Exercise, method =
## "spearman"): Cannot compute exact p-value with ties
```

```
## 
## 	Spearman's rank correlation rho
## 
## data:  ExcelFile$Wgt1 and ExcelFile$Exercise
## S = 23084557, p-value = 0.0001521
## alternative hypothesis: true rho is not equal to 0
## sample estimates:
##        rho 
## -0.1701192
```
    
### Inference

> Normality

Created a QQ Normal plot of White Blood Cell counts.
    

```r
qqnorm(ExcelFile$WBC, pch = 1,col = c("#6bc9c2"), frame = FALSE, main = "Normal Q-Q Plot for White Blood Cell")
qqline(ExcelFile$WBC, col = "black", lwd = 2)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-1.png" width="672" />
    
Conducted a statistical test for normality on White Blood Cell counts.

For the statistical test, we can perform shapiro test to get the value for normality.

For this, p-value is 0.1515. We can say that data is normally distributed.
    

```r
shapiro.test(ExcelFile$WBC)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  ExcelFile$WBC
## W = 0.99536, p-value = 0.1515
```

    
Now let's check if is White Blood Cell count normally distributed? What led you to this conclusion?

Based on the p-value get using shapiro test and the Q-Q plot graph, we can say that White Blood Cell data is normally distributed.
    
> Statistically Significant Differences

Compared White Blood Cell counts between the treatment and control group using a suitable hypothesis test.

Below is the process to choose comparison test.
    

```r
#As data is in the continuous manner, we will compare the means and groups if there are two or more than two and whether data meets the main three assumptions or not. 

#Data is independent.

#Data is normally distributed.
## Using Shapiro test p-value = 0.1515, we can that data is normally distributed.  
shapiro.test(ExcelFile$WBC)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  ExcelFile$WBC
## W = 0.99536, p-value = 0.1515
```

```r
#Variance is unknown.
##Based on f-test p-value = 0.6327, we can say that variances are same.
res.ftest <- var.test(WBC ~ Group, data = ExcelFile)
res.ftest
```

```
## 
## 	F test to compare two variances
## 
## data:  WBC by Group
## F = 1.0631, num df = 244, denom df = 245, p-value = 0.6327
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.8269123 1.3668343
## sample estimates:
## ratio of variances 
##           1.063098
```

```r
#Data met all the assumptions, we are performing t-test to mean.
res <- t.test(WBC ~ Group, data = ExcelFile, var.equal = TRUE)
res
```

```
## 
## 	Two Sample t-test
## 
## data:  WBC by Group
## t = -2.5064, df = 489, p-value = 0.01252
## alternative hypothesis: true difference in means between group Control and group Test is not equal to 0
## 95 percent confidence interval:
##  -395.75136  -47.93134
## sample estimates:
## mean in group Control    mean in group Test 
##              6770.029              6991.870
```
    
Based on previous assumptions conditions, all 3 conditions are satisfied to perform the t-test.

Based on p-value for WBC counts, we can say that means are different. 

For T-test, if p > 0.05 - Means are same.
            if p < 0.05 - Means are different.
    

```r
t.test(WBC ~ Group, data = ExcelFile, var.equal = TRUE)
```

```
## 
## 	Two Sample t-test
## 
## data:  WBC by Group
## t = -2.5064, df = 489, p-value = 0.01252
## alternative hypothesis: true difference in means between group Control and group Test is not equal to 0
## 95 percent confidence interval:
##  -395.75136  -47.93134
## sample estimates:
## mean in group Control    mean in group Test 
##              6770.029              6991.870
```

```r
bwplot(WBC ~ Group, data=ExcelFile, 
       main="WBC by Group",
       xlab="Group", ylab = "WBC",  pch = '|')
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-18-1.png" width="672" />
    
> Multiple Statistical Differences

Determining if White Blood Cell count varies by Heart Rate Level using ANOVA (statistical) and a sequence of boxplots (graphical).
  
Using ANOVA test result, where pr = 0.768 we can say that means are same.
    

```r
summary(aov(WBC ~ HR, data=ExcelFile))
```

```
##              Df    Sum Sq Mean Sq F value Pr(>F)
## HR            2    515115  257558   0.264  0.768
## Residuals   488 475776098  974951
```

```r
boxplot(WBC ~ HR, data=ExcelFile,
main="WBC by HR level",
ylab = "WBC",
xlab = "HR",
col=c("#6bc9c2"),
range=0)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-19-1.png" width="672" />
    
Determining if White Blood Cell count varies by Blood Pressure Level using ANOVA and a sequence of boxplots.

Using ANOVA test result, where pr = 0.322 we can say that means are same.
    

```r
summary(aov(WBC ~ BP, data=ExcelFile))
```

```
##              Df    Sum Sq Mean Sq F value Pr(>F)
## BP            2   2206157 1103078   1.135  0.322
## Residuals   488 474085056  971486
```

```r
boxplot(WBC ~ BP, data=ExcelFile,
main="WBC by BP Level",
xlab = "BP",
ylab = "WBC",
col=c("#6bc9c2"),
range=0)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-20-1.png" width="672" />
