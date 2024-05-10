---
title: "Delivery Time Analysis"
author: "Prachi Patel"
date: "2022-11-13"
output: pdf_document
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

This section loads and attaches all the necessary packages.


```r
if(!require(readxl)){install.packages("readxl")}
```

```
## Loading required package: readxl
```

```r
library("readxl")

if(!require(pastecs)){install.packages("pastecs")}
```

```
## Loading required package: pastecs
```

```r
library("pastecs")

if(!require(lattice)){install.packages("lattice")}
```

```
## Loading required package: lattice
```

```r
library("lattice")

if(!require(ggplot2)){install.packages("ggplot2")}
```

```
## Loading required package: ggplot2
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

if(!require(corrgram)){install.packages("corrgram")}
```

```
## Loading required package: corrgram
```

```
## Warning: package 'corrgram' was built under R version 4.2.2
```

```
## 
## Attaching package: 'corrgram'
```

```
## The following object is masked from 'package:lattice':
## 
##     panel.fill
```

```r
library("corrgram")

if(!require(psych)){install.packages("psych")}
```

```
## Loading required package: psych
```

```
## Warning: package 'psych' was built under R version 4.2.2
```

```
## 
## Attaching package: 'psych'
```

```
## The following objects are masked from 'package:ggplot2':
## 
##     %+%, alpha
```

```r
library("psych")
```




####################################
# 1. Preliminary and Exploratory
####################################

##  1. Rename all variables with your initials appended (just as was done in assignment 1,2 and 3)
  

```r
colnames(ExcelFile) <- paste(colnames(ExcelFile), "", sep = "")
head(ExcelFile)
```

```
##   Del Vin Pkg Cst  Mil Dom Haz              Car
## 1 8.4  17   8   8 2122   C   N M-Press Delivery
## 2 9.3  18   6   7 2390   C   N         Fed Post
## 3 9.6  13   5   5 1141   C   N         Fed Post
## 4 7.4   6   4  12 2469   C   N         Fed Post
## 5 9.0  14   6  10 1488   I   N         Fed Post
## 6 6.0   8   7  12  852   C   N         Fed Post
```

##    2. Examine the data using the exploratory techniques we have learned in class. Does the data look reasonable? Are there any outliers? If so, deal with them appropriately. 

###     -> As per below boxplots and density plots for vintage of products, removing the outliers as value -10 is not making any cense for this field.
###       Also, removed the outliners for No. of Product Ordered data where value is marked as Zero
  

```r
#Checking the outliners for all the fields.

boxplot(ExcelFile$Del, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Time of Delivery - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />

```r
densityplot( ~ ExcelFile$Del, pch=6,col=c("#6bc9c2"), xlab = "Time of Delivery")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-2.png" width="672" />

```r
boxplot(ExcelFile$Vin, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Vintage of Product - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-3.png" width="672" />

```r
densityplot( ~ ExcelFile$Vin, pch=6,col=c("#6bc9c2"), xlab = "Vintage of Product")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-4.png" width="672" />

```r
boxplot(ExcelFile$Pkg, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "No. of Product Ordered - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-5.png" width="672" />

```r
densityplot( ~ ExcelFile$Pkg, pch=6,col=c("#6bc9c2"), xlab = "No. of Product Ordered")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-6.png" width="672" />

```r
boxplot(ExcelFile$Cst, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "No. of Orders per Customer - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-7.png" width="672" />

```r
densityplot( ~ ExcelFile$Cst, pch=6,col=c("#6bc9c2"), xlab = "No. of Orders per Customer")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-8.png" width="672" />

```r
boxplot(ExcelFile$Mil, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Distance - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-9.png" width="672" />

```r
densityplot( ~ ExcelFile$Mil, pch=6,col=c("#6bc9c2"), xlab = "Distance")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-10.png" width="672" />

```r
#Removed the vintage of products fields outliers and showing the updated box plot and density plot.

nr <- which(ExcelFile$Vin <0)
ExcelFile <- ExcelFile[-c(nr),]

boxplot(ExcelFile$Vin, horizontal=TRUE, col=c("#e8bbfa"), pch=20, main = "Vintage of Product - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-11.png" width="672" />

```r
densityplot( ~ ExcelFile$Vin, pch=6,col=c("#e8bbfa"), xlab = "Vintage of Product")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-12.png" width="672" />

```r
#Removed the No. of Product Ordered data where value is marked as Zero. Because zero value will not help in the analysis of this data.

nr <- which(ExcelFile$Pkg <= 0)
ExcelFile <- ExcelFile[-c(nr),]

boxplot(ExcelFile$Pkg, horizontal=TRUE, col=c("#e8bbfa"), pch=20, main = "No. of Product Ordered - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-13.png" width="672" />

```r
densityplot( ~ ExcelFile$Pkg, pch=6,col=c("#e8bbfa"), xlab = "No. of Product Ordered")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-14.png" width="672" />
  
  
##  3. Using an appropriate technique from class, determine if there is any evidence if one Carrier has faster delivery times than the other. Make sure you explain the approach you took and your conclusions.

###   -> For checking fastest delivery times than the other, we can check using comparision test. 
###       Based on the mean value we received using t-test, we can say that Fed Post carrier has faster delivery time than M-Press Delivery Carrier.
  

```r
# Based on QQ-Norm Plot, we can say that data is normally distributed.

qqnorm(ExcelFile$Del)
qqline(ExcelFile$Del)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

```r
# To check compare two mean values we have to check the 3 assumptions for t-test.

# 1. Data is independent.
# 2. Data is normally distributed. 
#      Using Shapiro Normality test - p-value; we can say that data is normally distributed as p-value is 0.1288 that is greater than 0.05.

shapiro.test(ExcelFile$Del)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  ExcelFile$Del
## W = 0.99858, p-value = 0.1288
```

```r
bwplot(Del ~ Car, data = ExcelFile)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-2.png" width="672" />

```r
# 3. Variance is unknown.
#     Based on f-test p-value = 0.1454, we can say that variances are same.

Car.FTest <- var.test(Del ~ Car, data=ExcelFile)
Car.FTest
```

```
## 
## 	F test to compare two variances
## 
## data:  Del by Car
## F = 1.1007, num df = 912, denom df = 931, p-value = 0.1454
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.9673087 1.2525707
## sample estimates:
## ratio of variances 
##           1.100689
```

```r
# Data met all the assumptions so that we are performing t-test to compare mean value.
#   Based on the mean value we received using t-test, we can say that Fed Post carrier has faster delivery time than M-Press Delivery Carrier.

Car.TTest <- t.test(Del ~ Car, data=ExcelFile)
Car.TTest
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  Del by Car
## t = 1.9996, df = 1834.4, p-value = 0.0457
## alternative hypothesis: true difference in means between group Fed Post and group M-Press Delivery is not equal to 0
## 95 percent confidence interval:
##  0.002857232 0.295479384
## sample estimates:
##         mean in group Fed Post mean in group M-Press Delivery 
##                       7.706572                       7.557403
```
  
  
##  4. As demonstrated in class, split the data frame into a training and a test file. This should be a 80/20 split. For the set.seed(), use the last four digits of your student number. The training set will be used to build the following models and the test set will be used to validate them.
  


  
  
####################################
# 2.  Simple Linear Regression
####################################

##  1. Correlations: Create both numeric and graphical correlations (as demonstrated in class) and comment on noteworthy correlations you observe. Are these surprising? Do they make sense?

###   -> Based on the numeric and graphical corelation values, we can say that Time for Delivery is corelated to the number of packages ordered as it's value is 0.54. Further away the corelation value is from zero means stronger relation between those two variables. For number of orders customer has made is also negatively corelated to the time for delivery as it's value is -0.58. 


```r
cor(train[,unlist(lapply(train, is.numeric))], method="spearman")
```

```
##              Del          Vin          Pkg         Cst          Mil
## Del  1.000000000  0.007855868  0.504372143 -0.58551721  0.105883776
## Vin  0.007855868  1.000000000 -0.007940269 -0.02354802 -0.004411105
## Pkg  0.504372143 -0.007940269  1.000000000 -0.05598772 -0.056046184
## Cst -0.585517207 -0.023548016 -0.055987720  1.00000000  0.015777773
## Mil  0.105883776 -0.004411105 -0.056046184  0.01577777  1.000000000
```

```r
corrgram(train, order=TRUE, lower.panel=panel.shade,
         upper.panel=panel.pie, text.panel=panel.txt,
         main="Correlations")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />

```r
lm.fit_Pkg <-lm(Del ~ Pkg, data = train)
plot(Del ~ Pkg, data = train, pch=20)
abline(lm.fit_Pkg, col="Red")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-2.png" width="672" />

```r
lm.fit_Vin <-lm(Del ~ Vin, data = train)
plot(Del ~ Vin, data = train, pch=20)
abline(lm.fit_Vin, col="Red")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-3.png" width="672" />

```r
lm.fit_Cst <-lm(Del ~ Cst, data = train)
plot(Del ~ Cst, data = train, pch=20)
abline(lm.fit_Cst, col="Red")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-4.png" width="672" />

```r
lm.fit_Mil <-lm(Del ~ Mil, data = train)
plot(Del ~ Mil, data = train, pch=20)
abline(lm.fit_Mil, col="Red")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-5.png" width="672" />
  
  
##  2. Create a simple linear regression model using time for delivery as the dependent variable and number of packages as the independent. Create a scatter plot of the two variables and overlay the regression line.
  
###  -> Simple linear regression is used to predict outcome based one variable.In regression, the dependent variable is marked as Y and the independent variable is marked as X. 
###       Based on the regression line and plot, we can say that delivery is not dependent based on number of packages field.
  

```r
  lm.fit_Pkg <-lm(Del ~ Pkg, data = train)
  summary(lm.fit_Pkg)
```

```
## 
## Call:
## lm(formula = Del ~ Pkg, data = train)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.5049 -0.8306  0.0837  0.9694  4.1179 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  5.90202    0.08309   71.03   <2e-16 ***
## Pkg          0.42572    0.01827   23.30   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.375 on 1474 degrees of freedom
## Multiple R-squared:  0.2692,	Adjusted R-squared:  0.2687 
## F-statistic: 542.9 on 1 and 1474 DF,  p-value: < 2.2e-16
```

```r
  plot(Del ~ Pkg, data = train, pch=20)
  abline(lm.fit_Pkg, col="Red")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />
  
  
##  3. Create a simple linear regression model using time for delivery as the dependent variable and vintage of the product as the independent. Create a scatter plot of the two variables and overlay the regression line.

###   -> Based on the regression line and plot, we can say that delivery is not dependent based on vintage of the product field.
  

```r
lm.fit_Vin <-lm(Del ~ Vin, data = train)
summary(lm.fit_Vin)
```

```
## 
## Call:
## lm(formula = Del ~ Vin, data = train)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.5549 -1.0516  0.0462  1.0528  6.1484 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 7.635223   0.149477  51.079   <2e-16 ***
## Vin         0.001092   0.011118   0.098    0.922    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.608 on 1474 degrees of freedom
## Multiple R-squared:  6.55e-06,	Adjusted R-squared:  -0.0006719 
## F-statistic: 0.009654 on 1 and 1474 DF,  p-value: 0.9217
```

```r
plot(Del ~ Vin, data = train, pch=20)
abline(lm.fit_Vin, col="Red")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />
  
  
##  4. As demonstrated in class, compare the models (F-Stat,r2, RMSE for train and test, etc.) Which model is superior? Why?
  
###   -> Lower the RMSE better the model. Based on the RMSE values we can say that train model is better for no. of packeges per order data and test model is better for Vintage of product data. As both the models value is lower than other.



```r
############### Training Model###############

pred <- predict(lm.fit_Pkg, newdata=train)

RMSE_trn <- sqrt(mean((train$Del - pred)^2))
round(RMSE_trn,3)
```

```
## [1] 1.374
```

```r
pred <- predict(lm.fit_Vin, newdata=train)

RMSE_trn <- sqrt(mean((train$Del - pred)^2))
round(RMSE_trn,3)
```

```
## [1] 1.607
```

```r
############### Test Model###############

pred <- predict(lm.fit_Pkg, newdata=test)

RMSE_trn <- sqrt(mean((test$Del - pred)^2))
round(RMSE_trn,3)
```

```
## [1] 1.39
```

```r
pred <- predict(lm.fit_Vin, newdata=test)

RMSE_trn <- sqrt(mean((test$Del - pred)^2))
round(RMSE_trn,3)
```

```
## [1] 1.583
```
  
  

####################################
# 4. Model Development – Multivariate
####################################

##  As demonstrated in class, create two models, one using all the variables and the other using backward selection. For each model interpret and comment on the main measures we discussed in class (including RMSE for train and test). (Your commentary should be yours, not simply copied from my example).

###       Check F-Stat (P-Value) for significance. So, value is less tha 0.05 then model is better than random one. Over here p-value is closer to zero for both the models then it's better than choosing randomly.

###       Adjusted R-Squared value is can't be negatice.
###       -For train model adjusted r-squared value is 0.6084 meaning 60.84% variation in the output. 
###       -For test model adjusted r-squared value is 0.6078 meaning 60.78% variation in the output.
###           Higher the r-square better the model. So, based on r-squared value, we can say that train model is better than test model.

###       Residuals should be zero and symmetrical and it will used to calculate the difference between model's prediction and actual value.

###       T-test of coefficient is for checking if the value matches with corelation and for significance. If value is less than 0.5 then coefficient is non zero. and value is greater than 0.05 then coefficient is zero.

###       Estimated Coefficient. Normal value for this test is 0.05 but in both the model is near to zero.

###       Lower values of RMSE indicate better fit. For test model, RMSE value is 0.98 and train model RMSE value is indicated as 1.

  

```r
## Using All Variables created Training Model

full.model_Train = lm(Del ~ . ,data=train, na.action=na.omit)

summary(full.model_Train)
```

```
## 
## Call:
## lm(formula = Del ~ ., data = train, na.action = na.omit)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.5757 -0.6416  0.0027  0.6349  3.6333 
## 
## Coefficients:
##                        Estimate  Std. Error t value Pr(>|t|)    
## (Intercept)          7.80021924  0.17895089  43.589  < 2e-16 ***
## Vin                 -0.00181818  0.00696227  -0.261  0.79402    
## Pkg                  0.40300781  0.01346730  29.925  < 2e-16 ***
## Cst                 -0.29639355  0.00855067 -34.663  < 2e-16 ***
## Mil                  0.00046864  0.00005429   8.632  < 2e-16 ***
## DomI                -0.04447682  0.06738245  -0.660  0.50932    
## HazN                 0.18108154  0.06622289   2.734  0.00632 ** 
## CarM-Press Delivery  0.00999403  0.05277180   0.189  0.84982    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.007 on 1468 degrees of freedom
## Multiple R-squared:  0.6096,	Adjusted R-squared:  0.6078 
## F-statistic: 327.5 on 7 and 1468 DF,  p-value: < 2.2e-16
```

```r
pred <- predict(full.model_Train, newdata=train)

# calculating RMSE.
RMSE_trn_full <- sqrt(mean((train$Del - pred)^2))
round(RMSE_trn_full,2)
```

```
## [1] 1
```

```r
## Using Backward Selection for Training Model

back.model_Train = step(full.model_Train, direction="backward", details=TRUE)
```

```
## Start:  AIC=28.14
## Del ~ Vin + Pkg + Cst + Mil + Dom + Haz + Car
## 
##        Df Sum of Sq    RSS    AIC
## - Car   1      0.04 1488.2  26.17
## - Vin   1      0.07 1488.3  26.21
## - Dom   1      0.44 1488.6  26.58
## <none>              1488.2  28.14
## - Haz   1      7.58 1495.8  33.64
## - Mil   1     75.54 1563.7  99.22
## - Pkg   1    907.82 2396.0 729.07
## - Cst   1   1218.06 2706.2 908.79
## 
## Step:  AIC=26.17
## Del ~ Vin + Pkg + Cst + Mil + Dom + Haz
## 
##        Df Sum of Sq    RSS    AIC
## - Vin   1      0.07 1488.3  24.24
## - Dom   1      0.44 1488.7  24.61
## <none>              1488.2  26.17
## - Haz   1      7.54 1495.8  31.64
## - Mil   1     75.69 1563.9  97.39
## - Pkg   1    909.59 2397.8 728.19
## - Cst   1   1218.60 2706.8 907.10
## 
## Step:  AIC=24.24
## Del ~ Pkg + Cst + Mil + Dom + Haz
## 
##        Df Sum of Sq    RSS    AIC
## - Dom   1      0.44 1488.7  22.68
## <none>              1488.3  24.24
## - Haz   1      7.57 1495.9  29.73
## - Mil   1     75.71 1564.0  95.48
## - Pkg   1    909.54 2397.8 726.20
## - Cst   1   1218.53 2706.8 905.11
## 
## Step:  AIC=22.68
## Del ~ Pkg + Cst + Mil + Haz
## 
##        Df Sum of Sq    RSS    AIC
## <none>              1488.7  22.68
## - Haz   1      7.74 1496.5  28.33
## - Mil   1     75.60 1564.3  93.79
## - Pkg   1    909.54 2398.3 724.47
## - Cst   1   1218.20 2706.9 903.16
```

```r
summary(back.model_Train)
```

```
## 
## Call:
## lm(formula = Del ~ Pkg + Cst + Mil + Haz, data = train, na.action = na.omit)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.5821 -0.6436  0.0035  0.6277  3.6334 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  7.7738335  0.1478917  52.564  < 2e-16 ***
## Pkg          0.4028640  0.0134385  29.978  < 2e-16 ***
## Cst         -0.2962749  0.0085396 -34.694  < 2e-16 ***
## Mil          0.0004676  0.0000541   8.643  < 2e-16 ***
## HazN         0.1823173  0.0659457   2.765  0.00577 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.006 on 1471 degrees of freedom
## Multiple R-squared:  0.6095,	Adjusted R-squared:  0.6084 
## F-statistic: 573.9 on 4 and 1471 DF,  p-value: < 2.2e-16
```

```r
pred <- predict(back.model_Train, newdata=train)

# calculating RMSE.
RMSE_trn_back <- sqrt(mean((train$Del - pred)^2))
round(RMSE_trn_back,2)
```

```
## [1] 1
```

```r
#########################################################33
## Using All Variables created Test Model

full.model_test = lm(Del ~ . ,data=test, na.action=na.omit)

summary(full.model_test)
```

```
## 
## Call:
## lm(formula = Del ~ ., data = test, na.action = na.omit)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.5081 -0.5840 -0.0010  0.5858  2.8075 
## 
## Coefficients:
##                       Estimate Std. Error t value     Pr(>|t|)    
## (Intercept)          7.4701850  0.3331826  22.421      < 2e-16 ***
## Vin                  0.0096407  0.0144074   0.669      0.50382    
## Pkg                  0.3593750  0.0252018  14.260      < 2e-16 ***
## Cst                 -0.2866160  0.0167353 -17.126      < 2e-16 ***
## Mil                  0.0005524  0.0001014   5.449 0.0000000938 ***
## DomI                -0.2895930  0.1328689  -2.180      0.02994 *  
## HazN                 0.3749524  0.1246305   3.009      0.00281 ** 
## CarM-Press Delivery -0.1175782  0.1037509  -1.133      0.25785    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.9915 on 361 degrees of freedom
## Multiple R-squared:  0.6149,	Adjusted R-squared:  0.6074 
## F-statistic: 82.33 on 7 and 361 DF,  p-value: < 2.2e-16
```

```r
pred <- predict(full.model_test, newdata=test)

#calculating RMSE.
RMSE_tst_full <- sqrt(mean((test$Del - pred)^2))
round(RMSE_tst_full,2)
```

```
## [1] 0.98
```

```r
## Using Backward Selection for Test Model

back.model_test = step(full.model_test, direction="backward", details=TRUE)
```

```
## Start:  AIC=1.61
## Del ~ Vin + Pkg + Cst + Mil + Dom + Haz + Car
## 
##        Df Sum of Sq    RSS     AIC
## - Vin   1     0.440 355.32   0.064
## - Car   1     1.263 356.15   0.917
## <none>              354.88   1.607
## - Dom   1     4.670 359.55   4.431
## - Haz   1     8.898 363.78   8.744
## - Mil   1    29.193 384.08  28.777
## - Pkg   1   199.899 554.78 164.471
## - Cst   1   288.346 643.23 219.056
## 
## Step:  AIC=0.06
## Del ~ Pkg + Cst + Mil + Dom + Haz + Car
## 
##        Df Sum of Sq    RSS     AIC
## - Car   1     1.177 356.50  -0.716
## <none>              355.32   0.064
## - Dom   1     4.846 360.17   3.063
## - Haz   1     8.736 364.06   7.027
## - Mil   1    28.923 384.25  26.941
## - Pkg   1   200.735 556.06 163.319
## - Cst   1   287.920 643.24 217.063
## 
## Step:  AIC=-0.72
## Del ~ Pkg + Cst + Mil + Dom + Haz
## 
##        Df Sum of Sq    RSS     AIC
## <none>              356.50  -0.716
## - Dom   1     4.835 361.34   2.255
## - Haz   1     8.564 365.07   6.044
## - Mil   1    28.797 385.30  25.948
## - Pkg   1   200.604 557.11 162.012
## - Cst   1   289.121 645.62 216.425
```

```r
summary(back.model_test)
```

```
## 
## Call:
## lm(formula = Del ~ Pkg + Cst + Mil + Dom + Haz, data = test, 
##     na.action = na.omit)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.5897 -0.6064 -0.0115  0.5735  2.7528 
## 
## Coefficients:
##               Estimate Std. Error t value    Pr(>|t|)    
## (Intercept)  7.5527168  0.2725339  27.713     < 2e-16 ***
## Pkg          0.3598096  0.0251756  14.292     < 2e-16 ***
## Cst         -0.2865936  0.0167034 -17.158     < 2e-16 ***
## Mil          0.0005480  0.0001012   5.415 0.000000112 ***
## DomI        -0.2941982  0.1325973  -2.219     0.02712 *  
## HazN         0.3673279  0.1243910   2.953     0.00335 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.991 on 363 degrees of freedom
## Multiple R-squared:  0.6131,	Adjusted R-squared:  0.6078 
## F-statistic:   115 on 5 and 363 DF,  p-value: < 2.2e-16
```

```r
pred <- predict(back.model_test, newdata=test)
#calculating RMSE.
RMSE_tst_back <- sqrt(mean((test$Del - pred)^2))
round(RMSE_tst_back,2)
```

```
## [1] 0.98
```
  
  

####################################
# 5. Model Evaluation – Verifying Assumptions - Multivariate
####################################

##  For both models created in Step 4, evaluate the main assumptions of regression (for example, Error terms mean of zero, constant variance and normally distributed, etc.)
  

```r
# Training Model
par(mfrow = c(2, 2))  
plot(full.model_Train)  
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-1.png" width="672" />

```r
par(mfrow = c(1, 1))  

par(mfrow = c(2, 2))  
plot(back.model_Train)  
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-2.png" width="672" />

```r
par(mfrow = c(1, 1))
# Test Model
par(mfrow = c(2, 2))  
plot(full.model_test)  
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-3.png" width="672" />

```r
par(mfrow = c(1, 1))  

par(mfrow = c(2, 2))  
plot(back.model_test)  
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-4.png" width="672" />

```r
par(mfrow = c(1, 1))

# Checking Residual vectors.
# Training Model
full.res_trn <- residuals(full.model_Train)
back.res_trn <- residuals(back.model_Train)

# Test Model
full.res_tst <- residuals(full.model_test)
back.res_tst <- residuals(back.model_test)


# Check Normality Numerical - Based on the shapiro normality test we can say that all the models' are normally distributed as p-value is greater than 0.05.
# Training Model
shapiro.test(full.res_trn)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  full.res_trn
## W = 0.9983, p-value = 0.142
```

```r
shapiro.test(back.res_trn)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  back.res_trn
## W = 0.99829, p-value = 0.1394
```

```r
# Test Model

shapiro.test(full.res_tst)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  full.res_tst
## W = 0.99272, p-value = 0.06985
```

```r
shapiro.test(back.res_tst)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  back.res_tst
## W = 0.99239, p-value = 0.05675
```

```r
# Comparing the RMSE for both the models.
##  Lower the RMSE better the model. test model is better for both full and backward models as test model's value is lower with the comparision of backward model.

RMSE_full <- c(RMSE_trn_full,RMSE_tst_full)
round(RMSE_full,2)
```

```
## [1] 1.00 0.98
```

```r
RMSE_back <- c(RMSE_trn_back,RMSE_tst_back)
round(RMSE_back,2)
```

```
## [1] 1.00 0.98
```
  

####################################
# 6. Final Recommendation - Multivariate
####################################

## Based on your preceding analysis, recommend which of the two models from step 4 should be used and why.

###     Based on all the points mentioned in the step 4, we can say that test model is better than train model as all values are better in the test model based with the comparision of train model.






