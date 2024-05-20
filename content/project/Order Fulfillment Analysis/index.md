---
title: "Order Fulfillment Analysis"
author: "Prachi Patel"
date: "2022-11-27"
excerpt: "This project aims to analyze the factors affecting the on-time delivery of orders for a major mail-order company. By employing logistic regression, Naive-Bayes classification, and Linear Discriminant Analysis, the goal is to identify significant predictors of delivery success and compare the accuracy, processing speed, and false positive rates of these classification techniques. The ultimate objective is to determine the most effective model for ensuring deliveries are made within the company’s target timeframe."
output:
  html_document:
    df_print: paged
  pdf_document: default
editor_options: 
  markdown: 
    wrap: sentence
---

### Introduction

This project aims to analyze the factors affecting the on-time delivery of orders for a major mail-order company.
By employing logistic regression, Naive-Bayes classification, and Linear Discriminant Analysis, the goal is to identify significant predictors of delivery success and compare the accuracy, processing speed, and false positive rates of these classification techniques.
The ultimate objective is to determine the most effective model for ensuring deliveries are made within the company's target timeframe.

### Data Dictionary


|Variable |Description                                                                    |
|:--------|:------------------------------------------------------------------------------|
|Del      |Time for delivery (in days, rounded to nearest 10th)                           |
|Vin      |Vintage of product (i.e. how long it has been in the warehouse).               |
|Pkg      |How many packages of product have been ordered                                 |
|Cst      |How many orders the customer has made in the past                              |
|Mil      |Distance the order needs to be delivered (in km)                               |
|Dom      |Indicator for if the product is manufactured in Canada (C) or elsewhere (I)    |
|Haz      |Indicator for if the product is designated as Hazardous (H) or not (N).        |
|Car      |Indicator for which Carrier delivered the item (Fed Post, or M-Press Delivery) |

### Initial Setup

This section is for the basic set up.
It will clear all the plots, the console and the workspace.
It also sets the overall format for numbers.


```r
if(!is.null(dev.list())) dev.off()
cat("\014") 
rm(list=ls())
options(scipen=9)
```

### Load Packages

This section loads and attaches all the necessary packages.


```r
if(!require(readxl)){install.packages("readxl")}
library("readxl")

if(!require(pastecs)){install.packages("pastecs")}
library("pastecs")

if(!require(lattice)){install.packages("lattice")}
library("lattice")

if(!require(ggplot2)){install.packages("ggplot2")}
library("ggplot2")

if(!require(tinytex)){install.packages("tinytex")}
library("tinytex")

if(!require(corrgram)){install.packages("corrgram")}
library("corrgram")

if(!require(psych)){install.packages("psych")}
library("psych")

if(!require(partykit)){install.packages("partykit")}
library("partykit")

if(!require(klaR)){install.packages("klaR")}
  library("klaR")
```

### Import Data


```r
ExcelFile <- read.csv("PROG8430_Assign05_22F.txt")
ExcelFile <- as.data.frame(ExcelFile)
head(ExcelFile)
```

### Preliminary Data Preparation

First let's create a quick exploratory graphs of all variables.
Also, I have adjusted categorical variables to factor variables.

Checking if any outliners are available in any of these fields.
If available, I will remove those.


```r
#Box Plot and Density Plot for Time for Delivery.
boxplot(ExcelFile$Del, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Time of Delivery - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

```r
densityplot( ~ ExcelFile$Del, pch=6,col=c("#6bc9c2"), xlab = "Time of Delivery")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-2.png" width="672" />

```r
#Box Plot and Density Plot for Vintage of Product.
boxplot(ExcelFile$Vin, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Vintage of Product - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-3.png" width="672" />

```r
densityplot( ~ ExcelFile$Vin, pch=6,col=c("#6bc9c2"), xlab = "Vintage of Product")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-4.png" width="672" />

```r
#Box Plot and Density Plot for number of packages of product have been ordered.
boxplot(ExcelFile$Pkg, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "No. of Product Ordered - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-5.png" width="672" />

```r
densityplot( ~ ExcelFile$Pkg, pch=6,col=c("#6bc9c2"), xlab = "No. of Product Ordered")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-6.png" width="672" />

```r
#Box Plot and Density Plot for number of orders the customer has made in the past.
boxplot(ExcelFile$Cst, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "No. of Orders per Customer - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-7.png" width="672" />

```r
densityplot( ~ ExcelFile$Cst, pch=6,col=c("#6bc9c2"), xlab = "No. of Orders per Customer")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-8.png" width="672" />

```r
#Box Plot and Density Plot for distance the order needs to be delivered.
boxplot(ExcelFile$Mil, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Distance - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-9.png" width="672" />

```r
densityplot( ~ ExcelFile$Mil, pch=6,col=c("#6bc9c2"), xlab = "Distance")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-10.png" width="672" />

```r
#Bar Plot for Indicator for if the product is manufactured in Canada (C) or elsewhere (I).
DomBar <- table(ExcelFile$Dom)
Dom <- DomBar[order(DomBar,decreasing=TRUE)]
barplot(Dom,density = 30, angle = 45, main="Dom - Bar Plot", xlab="Dom", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-11.png" width="672" />

```r
#Bar Plot for Indicator for if the product is designated as Hazardous (H) or not (N).
HazBar <- table(ExcelFile$Haz)
Haz <- HazBar[order(HazBar,decreasing=TRUE)]
barplot(Haz,density = 30, angle = 45, main="Haz - Bar Plot", xlab="Haz", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-12.png" width="672" />

```r
#Bar Plot for indicator for which Carrier delivered the item.
CarBar <- table(ExcelFile$Car)
Car <- CarBar[order(CarBar,decreasing=TRUE)]
barplot(Car,density = 30, angle = 45, main="Car - Bar Plot", xlab="Car", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-13.png" width="672" />

```r
# As we see, there are no outliners available in any of these fields.
```


```r
# Now, I am converting categorical variables to factor variables. 
ExcelFile <- as.data.frame(unclass(ExcelFile),stringsAsFactors = TRUE)
head(ExcelFile)
```

```
##    Del Vin Pkg Cst  Mil Dom Haz              Car
## 1  9.5   6   6  13 1447   C   H M-Press Delivery
## 2 11.9  18   7   7 1874   I   N         Fed Post
## 3 14.6   7   7   8 1865   I   N         Fed Post
## 4 17.5  11   5  16 3111   I   H M-Press Delivery
## 5 10.7  12   4  10 1319   C   H         Fed Post
## 6 10.5  12   3   5 1415   C   N M-Press Delivery
```

Creating a new variable in the dataset which will have a value of 1 if *Del\<=10* and 0 otherwise.


```r
ExcelFile$OT <- as.factor(ifelse(ExcelFile$Del < 10.1, 1,0))
str(ExcelFile)
```

```
## 'data.frame':	6332 obs. of  9 variables:
##  $ Del: num  9.5 11.9 14.6 17.5 10.7 10.5 10.7 11.9 8.9 7.4 ...
##  $ Vin: int  6 18 7 11 12 12 21 12 13 16 ...
##  $ Pkg: int  6 7 7 5 4 3 1 4 6 5 ...
##  $ Cst: int  13 7 8 16 10 5 10 12 8 10 ...
##  $ Mil: int  1447 1874 1865 3111 1319 1415 1599 2361 1394 1121 ...
##  $ Dom: Factor w/ 2 levels "C","I": 1 2 2 2 1 1 1 1 2 2 ...
##  $ Haz: Factor w/ 2 levels "H","N": 1 2 2 1 1 2 1 2 2 1 ...
##  $ Car: Factor w/ 2 levels "Fed Post","M-Press Delivery": 2 1 1 2 1 2 2 2 1 2 ...
##  $ OT : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 2 2 ...
```

### Exploratory Analysis

> Correlations

Creating numeric correlations and checking if there are any co-linear variables available or not?


```r
# Creating numeric correlation.
cor(ExcelFile[,unlist(lapply(ExcelFile,is.numeric))], method="spearman")
```

```
##             Del          Vin          Pkg          Cst         Mil
## Del  1.00000000  0.025953062 -0.016474419 -0.021492268  0.80714307
## Vin  0.02595306  1.000000000 -0.001976183  0.003928352  0.01599953
## Pkg -0.01647442 -0.001976183  1.000000000 -0.001496276 -0.00615618
## Cst -0.02149227  0.003928352 -0.001496276  1.000000000  0.01186720
## Mil  0.80714307  0.015999532 -0.006156180  0.011867198  1.00000000
```

Observations on co-liner variables.

**Del** and **Mil** has strong linear relationship, with the value of 0.80.

**Vin** and **Cst** has almost no linear relationship, with the value of 0.003.

**Pkg** and **Mil** has almost no linear relationship, with a value of -0.006.

------------------------------------------------------------------------

Identifying the most significant predictor of an on time delivery and provide statistical evidence (in addition to the correlation coefficient) that suggest they are associated with an on time delivery.


```r
# Fit logistic regression model
logit_model <- glm(OT ~ ., data = ExcelFile, family = "binomial")

# Summary of the model
summary(logit_model)
```

```
## 
## Call:
## glm(formula = OT ~ ., family = "binomial", data = ExcelFile)
## 
## Deviance Residuals: 
##         Min           1Q       Median           3Q          Max  
## -0.00043175  -0.00000002  -0.00000002   0.00000002   0.00048262  
## 
## Coefficients:
##                          Estimate    Std. Error z value Pr(>|z|)
## (Intercept)          3363.4470870 41474.8613344   0.081    0.935
## Del                  -334.6568036  4123.6533319  -0.081    0.935
## Vin                    -0.0125951    60.9955647   0.000    1.000
## Pkg                     0.0545867   106.0600619   0.001    1.000
## Cst                    -0.0729954    68.6990311  -0.001    0.999
## Mil                     0.0005319     0.8526512   0.001    1.000
## DomI                   -0.0364939   490.4463000   0.000    1.000
## HazN                   -0.3179562   534.3371694  -0.001    1.000
## CarM-Press Delivery    -0.2695322   519.4178209  -0.001    1.000
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 8660.850577183  on 6331  degrees of freedom
## Residual deviance:    0.000017397  on 6323  degrees of freedom
## AIC: 18
## 
## Number of Fisher Scoring iterations: 25
```

```r
# Likelihood ratio test
anova(logit_model, test = "Chisq")
```

```
## Analysis of Deviance Table
## 
## Model: binomial, link: logit
## 
## Response: OT
## 
## Terms added sequentially (first to last)
## 
## 
##      Df Deviance Resid. Df Resid. Dev Pr(>Chi)    
## NULL                  6331     8660.9             
## Del   1   8660.9      6330        0.0   <2e-16 ***
## Vin   1      0.0      6329        0.0        1    
## Pkg   1      0.0      6328        0.0        1    
## Cst   1      0.0      6327        0.0        1    
## Mil   1      0.0      6326        0.0        1    
## Dom   1      0.0      6325        0.0        1    
## Haz   1      0.0      6324        0.0        1    
## Car   1      0.0      6323        0.0        1    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

The logistic regression analysis indicates that distance (Mil) is the most significant predictor of on-time delivery, supported by its highly significant p-value (\< 2e-16).

Other predictors such as number of orders per customer (Cst), manufactured outside Canada (Dom), non-hazardous products (Haz), and the carrier (Car) also show strong associations with on-time delivery based on their p-values.

------------------------------------------------------------------------

### Model Development

Now, let's create two logistic regression models.

**A full model using all of the variables.**


```r
# Removed Del field from the data set 
# because correlation is high between newly added field OT and Del 
# as OT field is created based on Del field only.
ExcelFile <- ExcelFile[-c(1)]

#Creating a full model for all the variables.
full.model = glm(OT ~ . ,data=ExcelFile, na.action=na.omit, family="binomial")
summary(full.model)
```

```
## 
## Call:
## glm(formula = OT ~ ., family = "binomial", data = ExcelFile, 
##     na.action = na.omit)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -3.0579  -0.4641  -0.0798   0.4310   3.3751  
## 
## Coefficients:
##                       Estimate Std. Error z value      Pr(>|z|)    
## (Intercept)          7.1141805  0.2991113  23.784       < 2e-16 ***
## Vin                  0.0190391  0.0111076   1.714        0.0865 .  
## Pkg                  0.0231763  0.0201096   1.153        0.2491    
## Cst                  0.0558559  0.0132619   4.212 0.00002533765 ***
## Mil                 -0.0061375  0.0001591 -38.586       < 2e-16 ***
## DomI                -0.7614954  0.0880636  -8.647       < 2e-16 ***
## HazN                 0.5528416  0.0924725   5.978 0.00000000225 ***
## CarM-Press Delivery  2.4106869  0.0921436  26.162       < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 8660.9  on 6331  degrees of freedom
## Residual deviance: 4105.9  on 6324  degrees of freedom
## AIC: 4121.9
## 
## Number of Fisher Scoring iterations: 6
```

```r
pred <- predict(full.model, newdata=ExcelFile)
```

**An additional model using backward selection**


```r
# Creating another model using backward selection.
back.model = step(full.model, direction="backward", details=TRUE)
```

```
## Start:  AIC=4121.95
## OT ~ Vin + Pkg + Cst + Mil + Dom + Haz + Car
## 
##        Df Deviance    AIC
## - Pkg   1   4107.3 4121.3
## <none>      4105.9 4121.9
## - Vin   1   4108.9 4122.9
## - Cst   1   4123.8 4137.8
## - Haz   1   4142.3 4156.3
## - Dom   1   4183.1 4197.1
## - Car   1   4999.7 5013.7
## - Mil   1   8144.7 8158.7
## 
## Step:  AIC=4121.27
## OT ~ Vin + Cst + Mil + Dom + Haz + Car
## 
##        Df Deviance    AIC
## <none>      4107.3 4121.3
## - Vin   1   4110.2 4122.2
## - Cst   1   4124.9 4136.9
## - Haz   1   4143.6 4155.6
## - Dom   1   4184.3 4196.3
## - Car   1   5001.0 5013.0
## - Mil   1   8145.5 8157.5
```

```r
summary(back.model)
```

```
## 
## Call:
## glm(formula = OT ~ Vin + Cst + Mil + Dom + Haz + Car, family = "binomial", 
##     data = ExcelFile, na.action = na.omit)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -3.0412  -0.4666  -0.0804   0.4314   3.3941  
## 
## Coefficients:
##                       Estimate Std. Error z value      Pr(>|z|)    
## (Intercept)          7.2027549  0.2896373  24.868       < 2e-16 ***
## Vin                  0.0189735  0.0111066   1.708        0.0876 .  
## Cst                  0.0555673  0.0132600   4.191 0.00002782316 ***
## Mil                 -0.0061328  0.0001588 -38.608       < 2e-16 ***
## DomI                -0.7605864  0.0880323  -8.640       < 2e-16 ***
## HazN                 0.5526388  0.0924502   5.978 0.00000000226 ***
## CarM-Press Delivery  2.4098859  0.0921049  26.165       < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 8660.9  on 6331  degrees of freedom
## Residual deviance: 4107.3  on 6325  degrees of freedom
## AIC: 4121.3
## 
## Number of Fisher Scoring iterations: 6
```

```r
pred <- predict(back.model, newdata=ExcelFile)
```

Based on this analysis, I recommend backward model should be selected as:

Here is the several measures I have considered to choose which model is better.

> **AIC** - Model is better where AIC value is lower.

AIC value is lower in the Backward Model with the comparison of full model.

> **Deviance** - Measure of Error = null deviance - Residual deviance.

> Model is better with the higher difference value.

Deviance is higher in the full model.

> Residual symmetry value should be zero.
> Residual symmetry value is near by zero for Model 2(backward model).

> Z-values - all Pr(\>z) values less than 0.05 is consider as the better model.

For Backward model, Pr(\>z) value is less than 0.05 for all except one as 0.0816 but it's near by 0.05.

> Parameter Co-Efficient

Based on these observations, we can say that `backward model` is the better model than full model.

------------------------------------------------------------------------

> In this section, all three classifiers are built as the dependant variable and the remaining variables as the independent variables.

### Logistic Regression -- Backward

As above, used the step option in the glm function to fit the model (using backward selection).


```r
# Added the Start Time in the start_time.
start_time <- Sys.time()

# In regression, the dependent variable is marked as Y and the independent variable is marked as X. 
# So, here adding the OT as dependent variable and other variables as the independent variables.
glm.mod <- glm(OT ~ .,family="binomial", data=ExcelFile, na.action=na.omit)
stp.glm <- step(glm.mod)
```

```
## Start:  AIC=4121.95
## OT ~ Vin + Pkg + Cst + Mil + Dom + Haz + Car
## 
##        Df Deviance    AIC
## - Pkg   1   4107.3 4121.3
## <none>      4105.9 4121.9
## - Vin   1   4108.9 4122.9
## - Cst   1   4123.8 4137.8
## - Haz   1   4142.3 4156.3
## - Dom   1   4183.1 4197.1
## - Car   1   4999.7 5013.7
## - Mil   1   8144.7 8158.7
## 
## Step:  AIC=4121.27
## OT ~ Vin + Cst + Mil + Dom + Haz + Car
## 
##        Df Deviance    AIC
## <none>      4107.3 4121.3
## - Vin   1   4110.2 4122.2
## - Cst   1   4124.9 4136.9
## - Haz   1   4143.6 4155.6
## - Dom   1   4184.3 4196.3
## - Car   1   5001.0 5013.0
## - Mil   1   8145.5 8157.5
```

```r
# Added the End time in the end_time.
end_time <- Sys.time()

# Classifies
resp <- predict(back.model, type="response")
head(resp,10)
```

```
##            1            2            3            4            5            6 
## 0.8284610373 0.0225881547 0.0205238801 0.0001084446 0.4742740550 0.8800547833 
##            7            8            9           10 
## 0.6814581724 0.0316864572 0.2967004048 0.9446158186
```

```r
# more than 50% chance then 1 else 0.
class <- ifelse(resp > 0.5,1,0)
head(class)
```

```
## 1 2 3 4 5 6 
## 1 0 0 0 0 1
```

Summarized the results in a Confusion Matrix .


```r
# creating the confusion matrix.
LR_CF <-table(ExcelFile$OT, class, dnn = list("Actual","Predicted"))
LR_CF
```

```
##       Predicted
## Actual    0    1
##      0 3164  432
##      1  480 2256
```

```r
# Adding the results in confusion matrix.
LR_TP <-LR_CF[2,2]
LR_TN <-LR_CF[1,1]
LR_FP <-LR_CF[1,2]
LR_FN <-LR_CF[2,1]
```

Now, let's calculate the time (in seconds) it took to fit the model.


```r
# Processing time for Logistic regression
Conf_time <- end_time - start_time
Conf_time
```

```
## Time difference of 0.3985372 secs
```

### Naive-Bayes Classification

Used all the variables in the dataset to fit a Naive-Bayesian classification model.


```r
start_time <- Sys.time()

# In regression, the dependent variable is marked as Y and the independent variable is marked as X. 
# So, here adding the OT as dependent variable and other variables as the independent variables.
Naive <- NaiveBayes(OT ~ . ,data = ExcelFile, na.action=na.omit)

end_time <- Sys.time()
```

Summarized the results in a Confusion Matrix.


```r
# Classifies
pred_bay <- predict(Naive,ExcelFile)
 
# Creates Confusion Matrix
Naive_CF <- table(Actual=ExcelFile$OT, Predicted=pred_bay$class)
Naive_CF
```

```
##       Predicted
## Actual    0    1
##      0 3156  440
##      1  505 2231
```

```r
Naive_TP <-Naive_CF[2,2]
Naive_TN <-Naive_CF[1,1]
Naive_FP <-Naive_CF[1,2]
Naive_FN <-Naive_CF[2,1]
```

Now, let's calculate the time (in seconds) it took to fit the model.


```r
# Processing time for Naive Bayes Classification
NB_Time <- end_time - start_time
NB_Time
```

```
## Time difference of 0.01524496 secs
```

### Linear Discriminant Analysis

Used all the variables in the dataset to fit an LDA classification model.


```r
start_time <- Sys.time()
LDA <- lda(OT ~ .,data = ExcelFile, na.action=na.omit)
end_time <- Sys.time()
```

Summarized the results in a Confusion Matrix.


```r
#Classifies
pred_dis <- predict(LDA, data=ExcelFile)

#Confusion Matrix
LDA_CF <- table(Actual=ExcelFile$OT, Predicted=pred_dis$class)
LDA_CF
```

```
##       Predicted
## Actual    0    1
##      0 3157  439
##      1  470 2266
```

```r
#Defined
LDA_TP <-LDA_CF[2,2]
LDA_TN <-LDA_CF[1,1]
LDA_FP <-LDA_CF[1,2]
LDA_FN <-LDA_CF[2,1]
```

Now, let's calculate the time (in seconds) it took to fit the model.


```r
# Processing time for Linear Discriminant Analysis
LDA_Time <- end_time - start_time
LDA_Time
```

```
## Time difference of 0.01161194 secs
```

> Comparing All Three Classifiers

Now take a look at Which classifier is most accurate.


```r
# Accuracy: The proportion of cases correctly classified: (TP+TN)/Total

# Accuracy for LR classifier.
LR_ACC <-(LR_TP+LR_TN)/sum(LR_CF)
LR_ACC
```

```
## [1] 0.8559697
```

```r
#Accuracy for Naive classifier.
Naive_ACC <-(Naive_TP+Naive_TN)/sum(Naive_CF)
Naive_ACC
```

```
## [1] 0.8507581
```

```r
#Accuracy for LDA Classifier.
LDA_ACC <-(LDA_TP+LDA_TN)/sum(LDA_CF)
LDA_ACC
```

```
## [1] 0.8564435
```

```r
# Based on the accuracy values for all three defined classifiers; 
#Linear Discriminant Analysis classifier is the most accurate because it's value is slightly higher than other two classifier's accuracy value.
```

Now let's check Which classifier is most suitable when processing speed is most important.


```r
# Processing time for Logistic Regression – Backward
Conf_time
```

```
## Time difference of 0.3985372 secs
```

```r
# Processing time for Naive-Bayes Classification
NB_Time
```

```
## Time difference of 0.01524496 secs
```

```r
# Processing time for Linear Discriminant Analysis
LDA_Time
```

```
## Time difference of 0.01161194 secs
```

```r
# Processing time for Linear Discriminant Analysis is the lowest from remaining two classifiers.
# Linear Discriminant Analysis is the most suitable classifier if we consider processing speed as the most important.
```

Now let's check Which classifier minimizes false positives.


```r
# False Positive value for Logistic Regression Classifier 
LR_FP
```

```
## [1] 432
```

```r
# False Positive value for Naive Bayes Classification
Naive_FP
```

```
## [1] 440
```

```r
# False Positive value for Linear Discriminant Analysis Classifier
LDA_FP
```

```
## [1] 439
```

```r
# Based on the FP values for all three classifiers, 
# Logistic Regression Classifier has the lowest value for false positives.
```

**See Which classifier is best overall.**

Based on previously created classifiers, it's accuracy and False positive values, Logistic Regression classifier is the best classifier.

As Logistic Regression's value for False Positive is the lowest.

For accuracy, Logistic Regression's value is slightly lower with the comparison of Linear Discriminant Analysis Classifier with 0.001 value.

### Decision Tree

Used all the variables in the dataset to fit a Decision Tree classification model.


```r
start_time <- Sys.time()
tree.fit <- ctree(OT ~ ., data=ExcelFile)
end_time <- Sys.time()
plot(tree.fit, gp=gpar(fontsize=2))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-24-1.png" width="672" />

Summarizing the results in a Confusion Matrix.


```r
#Classifies
pred.tree <- predict(tree.fit, ExcelFile)

# creating the confusion matrix.
CF_TREE <- table(Actual=ExcelFile$OT, Predicted=pred.tree)
CF_TREE
```

```
##       Predicted
## Actual    0    1
##      0 3193  403
##      1  504 2232
```

calculating the time (in seconds) it took to fit the model.


```r
CF_Time <- end_time - start_time
CF_Time
```

```
## Time difference of 0.1172068 secs
```
