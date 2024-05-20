---
title: "Cancer Diagnosis Prediction Model"
author: "Prachi Patel"
date: "2022-12-13"
excerpt: "This project aims to predict cancer diagnosis using patient data. It compares different methods like Logistic Regression, Naive Bayes, and Linear Discriminant Analysis to find the most accurate model. The goal is to identify the best way to predict cancer while minimizing errors."
output:
  html_document:
    df_print: paged
  pdf_document: default
editor_options: 
  markdown: 
    wrap: sentence
---

### Introduction

This project aims to predict cancer diagnosis using patient data. It compares different methods like Logistic Regression, Naive Bayes, and Linear Discriminant Analysis to find the most accurate model. The goal is to identify the best way to predict cancer while minimizing errors.

### Data Dictionary


|Variable |Description                                                                                                                              |
|:--------|:----------------------------------------------------------------------------------------------------------------------------------------|
|Cancer   |The patient was diagnosed with cancer (0=no, 1=yes)                                                                                      |
|Age      |Age in years                                                                                                                             |
|HW       |A ratio of Height to Weight                                                                                                              |
|Hst      |History of Cancer in Family (0=No, 1=Yes)                                                                                                |
|Exe      |Time spent exercising each week (in minutes)                                                                                             |
|Smk      |Patient is smoker (0=No, 1=Yes)                                                                                                          |
|Drk      |Patient drinks alcohol (0=No, 1=Yes)                                                                                                     |
|Hlth     |General health of patient. Five point scale: (VP) very poor health, (P) poor health, (A) average health, (G) good, (VG) very good health |

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

#### Load Packages

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

if(!require(xlsx)){install.packages("xlsx")}
  library(xlsx)
```

#### Import Data

This code chunk is to read in the excel file and convert it into a data frame.


```r
ExcelFile <- read_excel("PROG8430_Final_22F_train.xlsx")
ExcelFile <- as.data.frame(ExcelFile)
head(ExcelFile)
```

#### Data Manupulation

Convert all the Character variable into Factor.


```r
as.data.frame(unclass(ExcelFile),stringsAsFactors = TRUE) 
```

Converting all fields in numeric data type as we are checking correlation between all the fields.


```r
ExcelFile$Cancer <- as.numeric(ExcelFile$Cancer)
ExcelFile$Hst <- as.numeric(ExcelFile$Hst)
ExcelFile$Smk <- as.numeric(ExcelFile$Smk)
ExcelFile$Drk <- as.numeric(ExcelFile$Drk)
ExcelFile1 <- ExcelFile[c(1:7)]
cor(ExcelFile1[,unlist(lapply(ExcelFile1,is.numeric))], method="spearman")
```

```
##             Cancer         Age          HW           Hst           Exe
## Cancer  1.00000000  0.24144147 -0.58841442  0.3469225476 -0.0256017207
## Age     0.24144147  1.00000000 -0.15722835  0.0637420924  0.0321905781
## HW     -0.58841442 -0.15722835  1.00000000 -0.1425845373 -0.0410886539
## Hst     0.34692255  0.06374209 -0.14258454  1.0000000000 -0.0003593495
## Exe    -0.02560172  0.03219058 -0.04108865 -0.0003593495  1.0000000000
## Smk     0.59131155  0.15294993 -0.38752376  0.2044849284  0.0144553973
## Drk     0.50882751  0.16800966 -0.33218132  0.1556839972  0.0146751770
##               Smk         Drk
## Cancer  0.5913116  0.50882751
## Age     0.1529499  0.16800966
## HW     -0.3875238 -0.33218132
## Hst     0.2044849  0.15568400
## Exe     0.0144554  0.01467518
## Smk     1.0000000  0.89535521
## Drk     0.8953552  1.00000000
```

Based on the correlation, we can say that there is strong correlation between Smoke and Drink in the model.


```r
ExcelFile1 <- ExcelFile1[-c(7)]
ExcelFile <- ExcelFile[-c(7)]
summary(ExcelFile)
```

```
##      Cancer           Age              HW             Hst        
##  Min.   :0.000   Min.   :22.00   Min.   :0.678   Min.   :0.0000  
##  1st Qu.:0.000   1st Qu.:42.00   1st Qu.:0.948   1st Qu.:0.0000  
##  Median :1.000   Median :48.00   Median :1.031   Median :0.0000  
##  Mean   :0.505   Mean   :47.09   Mean   :1.040   Mean   :0.3568  
##  3rd Qu.:1.000   3rd Qu.:52.00   3rd Qu.:1.115   3rd Qu.:1.0000  
##  Max.   :1.000   Max.   :70.00   Max.   :8.500   Max.   :1.0000  
##       Exe              Smk             Hlth          
##  Min.   :  1.00   Min.   :0.0000   Length:695        
##  1st Qu.: 15.00   1st Qu.:0.0000   Class :character  
##  Median : 26.00   Median :1.0000   Mode  :character  
##  Mean   : 31.45   Mean   :0.5079                     
##  3rd Qu.: 42.00   3rd Qu.:1.0000                     
##  Max.   :140.00   Max.   :1.0000
```

> Checking Dimensionality reduction.

Using summary function, I have summarized all data in the data source.


```r
summary(ExcelFile)
```

```
##      Cancer           Age              HW             Hst        
##  Min.   :0.000   Min.   :22.00   Min.   :0.678   Min.   :0.0000  
##  1st Qu.:0.000   1st Qu.:42.00   1st Qu.:0.948   1st Qu.:0.0000  
##  Median :1.000   Median :48.00   Median :1.031   Median :0.0000  
##  Mean   :0.505   Mean   :47.09   Mean   :1.040   Mean   :0.3568  
##  3rd Qu.:1.000   3rd Qu.:52.00   3rd Qu.:1.115   3rd Qu.:1.0000  
##  Max.   :1.000   Max.   :70.00   Max.   :8.500   Max.   :1.0000  
##       Exe              Smk             Hlth          
##  Min.   :  1.00   Min.   :0.0000   Length:695        
##  1st Qu.: 15.00   1st Qu.:0.0000   Class :character  
##  Median : 26.00   Median :1.0000   Mode  :character  
##  Mean   : 31.45   Mean   :0.5079                     
##  3rd Qu.: 42.00   3rd Qu.:1.0000                     
##  Max.   :140.00   Max.   :1.0000
```

Based on summary, we can say that no null data is available as no data is available in `nbr.null` row.


```r
stat.desc(ExcelFile)
```

```
##                    Cancer           Age           HW          Hst           Exe
## nbr.val      695.00000000   695.0000000 695.00000000 695.00000000   695.0000000
## nbr.null     344.00000000     0.0000000   0.00000000 447.00000000     0.0000000
## nbr.na         0.00000000     0.0000000   0.00000000   0.00000000     0.0000000
## min            0.00000000    22.0000000   0.67800000   0.00000000     1.0000000
## max            1.00000000    70.0000000   8.50000000   1.00000000   140.0000000
## range          1.00000000    48.0000000   7.82200000   1.00000000   139.0000000
## sum          351.00000000 32731.0000000 722.46700000 248.00000000 21858.0000000
## median         1.00000000    48.0000000   1.03100000   0.00000000    26.0000000
## mean           0.50503597    47.0949640   1.03952086   0.35683453    31.4503597
## SE.mean        0.01897878     0.3030735   0.01169660   0.01818507     0.8477562
## CI.mean.0.95   0.03726271     0.5950510   0.02296496   0.03570436     1.6644745
## var            0.25033483    63.8382311   0.09508318   0.22983435   499.4899674
## std.dev        0.50033472     7.9898830   0.30835561   0.47941041    22.3492722
## coef.var       0.99069126     0.1696547   0.29663244   1.34350903     0.7106206
##                       Smk Hlth
## nbr.val      695.00000000   NA
## nbr.null     342.00000000   NA
## nbr.na         0.00000000   NA
## min            0.00000000   NA
## max            1.00000000   NA
## range          1.00000000   NA
## sum          353.00000000   NA
## median         1.00000000   NA
## mean           0.50791367   NA
## SE.mean        0.01897736   NA
## CI.mean.0.95   0.03725993   NA
## var            0.25029751   NA
## std.dev        0.50029743   NA
## coef.var       0.98500485   NA
```

> Checking Outliners

Create `barplot` for cancer field that shows if the patient was diagnosed with cancer or not.

This plot will show if any outliners are available or not.


```r
Cancer <- table(ExcelFile$Cancer)
Cancer <- Cancer[order(Cancer,decreasing=TRUE)]
barplot(Cancer,density = 30, angle = 45, main="The patient was diagnosed with cancer (0=no, 1=yes)", xlab="The patient was diagnosed with cancer", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-1.png" width="672" />

As per the graph we can say that there are no outliners are available for the cancer field.

Created `boxplot` for Age (i.e. numeric, non-binary) to determine outliers.


```r
boxplot(ExcelFile$Age, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Age in years")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-11-1.png" width="672" />

```r
densityplot( ~ ExcelFile$Age, pch=6,col=c("#6bc9c2"), xlab = "Age")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-11-2.png" width="672" />

As per the graph we can say that there are no outliners are available for the Age field.

Created `boxplot` for Height to Weight (i.e. numeric, non-binary) to determine outliers.


```r
boxplot(ExcelFile$HW, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "A ratio of Height to Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-1.png" width="672" />

```r
densityplot( ~ ExcelFile$HW, pch=6,col=c("#6bc9c2"), xlab = "Height to Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-2.png" width="672" />

As per the graph we can say that there are outliners are available for the height to weight ratio field.

This code to delete outliner data where Height to Weight value is more than 2.


```r
nr <- which(ExcelFile$HW > 2)
ExcelFile <- ExcelFile[-c(nr),]
boxplot(ExcelFile$HW, horizontal=TRUE, col=c("#e8bbfa"), pch=20, main = "A ratio of Height to Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-13-1.png" width="672" />

```r
densityplot( ~ ExcelFile$HW, pch=6,col=c("#e8bbfa"), xlab = "Height to Weight")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-13-2.png" width="672" />

Now we can see that no outliners are available for the height to weight field.

Created `boxplot` for History of Cancer in Family field to determine outliers.


```r
Hst <- table(ExcelFile$Hst)
Hst <- Hst[order(Hst,decreasing=TRUE)]
barplot(Hst,density = 30, angle = 45, main="History of Cancer in Family (0=No, 1=Yes)", xlab="History of Cancer", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-14-1.png" width="672" />

And we can see that no outliners are available in the History of Cancer in Family field.

Created `boxplot` for Exercise (i.e. numeric, non-binary) to determine outliers.


```r
boxplot(ExcelFile$Exe, horizontal=TRUE, col=c("#6bc9c2"), pch=20, main = "Time spent exercising each week (in minutes) - Box Plot")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-1.png" width="672" />

```r
densityplot( ~ ExcelFile$Exe, pch=6,col=c("#6bc9c2"), xlab = "Exercise")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-2.png" width="672" />

No outliners are available in the exercise field as well.

Now, let's check the outliners for the smoker field.


```r
Smk <- table(ExcelFile$Smk)
Smk <- Smk[order(Smk,decreasing=TRUE)]
barplot(Smk,density = 30, angle = 45, main="Patient is smoker (0=No, 1=Yes) - Bar Plot", xlab="Smoker", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-16-1.png" width="672" />

And again we can see that no data available in smoker field as well.

Now, let's check the outliners for the health of the patient field as well.


```r
Hlth <- table(ExcelFile$Hlth)
Hlth <- Hlth[order(Hlth,decreasing=TRUE)]
barplot(Hlth,density = 30, angle = 45, main="General health of patient. Five point scale: (VP) very poor health, (P) poor 
health, (A) average health, (G) good, (VG) very good health - Bar Plot", xlab="Health", ylab = "Frequency", col=c("#6bc9c2"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-17-1.png" width="672" />

And for this field as well we can see that no outliners are available for this field as well.

> Full Model

Now, let's create a full model for all the variable with the Cancer variable as a dependent variable.


```r
full.model = glm(Cancer ~ . ,data=ExcelFile, na.action=na.omit, family="binomial")
summary(full.model)
```

```
## 
## Call:
## glm(formula = Cancer ~ ., family = "binomial", data = ExcelFile, 
##     na.action = na.omit)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.9683  -0.4631   0.0611   0.4394   2.8500  
## 
## Coefficients:
##               Estimate Std. Error z value        Pr(>|z|)    
## (Intercept)  10.164755   1.637304   6.208 0.0000000005359 ***
## Age           0.055293   0.016001   3.456        0.000549 ***
## HW          -14.060269   1.426834  -9.854         < 2e-16 ***
## Hst           1.806521   0.266683   6.774 0.0000000000125 ***
## Exe          -0.006828   0.005392  -1.266        0.205366    
## Smk           2.453180   0.243388  10.079         < 2e-16 ***
## HlthG        -0.012830   0.320314  -0.040        0.968051    
## HlthP         0.189707   0.341117   0.556        0.578118    
## HlthVG       -0.001776   0.361760  -0.005        0.996082    
## HlthVP        0.386264   0.493251   0.783        0.433569    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 962.00  on 693  degrees of freedom
## Residual deviance: 460.39  on 684  degrees of freedom
## AIC: 480.39
## 
## Number of Fisher Scoring iterations: 6
```

```r
pred <- predict(full.model, newdata=ExcelFile)
```

> Backward Model

Creating another model using backward selection.


```r
back.model = step(full.model, direction="backward", details=TRUE)
```

```
## Start:  AIC=480.39
## Cancer ~ Age + HW + Hst + Exe + Smk + Hlth
## 
##        Df Deviance    AIC
## - Hlth  4   461.39 473.39
## - Exe   1   462.00 480.00
## <none>      460.39 480.39
## - Age   1   472.97 490.97
## - Hst   1   512.56 530.56
## - Smk   1   580.17 598.17
## - HW    1   614.96 632.96
## 
## Step:  AIC=473.39
## Cancer ~ Age + HW + Hst + Exe + Smk
## 
##        Df Deviance    AIC
## - Exe   1   463.20 473.20
## <none>      461.39 473.39
## - Age   1   474.16 484.16
## - Hst   1   513.91 523.91
## - Smk   1   581.16 591.16
## - HW    1   616.86 626.86
## 
## Step:  AIC=473.2
## Cancer ~ Age + HW + Hst + Smk
## 
##        Df Deviance    AIC
## <none>      463.20 473.20
## - Age   1   475.87 483.87
## - Hst   1   516.15 524.15
## - Smk   1   582.73 590.73
## - HW    1   618.03 626.03
```

```r
summary(back.model)
```

```
## 
## Call:
## glm(formula = Cancer ~ Age + HW + Hst + Smk, family = "binomial", 
##     data = ExcelFile, na.action = na.omit)
## 
## Deviance Residuals: 
##      Min        1Q    Median        3Q       Max  
## -3.00105  -0.47042   0.06143   0.45058   2.86729  
## 
## Coefficients:
##              Estimate Std. Error z value         Pr(>|z|)    
## (Intercept)  10.02415    1.61317   6.214 0.00000000051671 ***
## Age           0.05456    0.01573   3.469         0.000523 ***
## HW          -14.02958    1.42157  -9.869          < 2e-16 ***
## Hst           1.80988    0.26535   6.821 0.00000000000906 ***
## Smk           2.43806    0.24161  10.091          < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 962.0  on 693  degrees of freedom
## Residual deviance: 463.2  on 689  degrees of freedom
## AIC: 473.2
## 
## Number of Fisher Scoring iterations: 6
```

```r
pred <- predict(back.model, newdata=ExcelFile)
```

-------------------------------------------------------------------------------

Let's analyse the backward model.

**AIC** - Backward model has lowest AIC value.

**Deviance** - Backward model has higher deviance difference.

**Residual symmetry** - Residual symmetry value is near by zero for backward model.

**z-values** - all Pr(>z) values less than 0.05 is consider as the better model.

For Backward model, Pr(>z) value is less than 0.05 for one and another variable Pr(>z) value is near by 0.05.

> Based on all these observations, we can say that *backward model* is the better model than other model. 

-------------------------------------------------------------------------

As backward model is better, let's create a Logistic Regression using Backward.


```r
# Converting these field into factor.
ExcelFile$Cancer <- as.factor(ExcelFile$Cancer)
ExcelFile$Hst <- as.factor(ExcelFile$Hst)
ExcelFile$Smk <- as.factor(ExcelFile$Smk)
```

In regression, the dependent variable is marked as Y and the independent variable is marked as X. 

So, here adding the Cancer as dependent variable and other variables as the independent variables.


```r
# glm = 'Generalized Linear Models'
glm.mod <- glm(Cancer ~ .,family="binomial", data=ExcelFile, na.action=na.omit)
stp.glm <- step(glm.mod)
```

```
## Start:  AIC=480.39
## Cancer ~ Age + HW + Hst + Exe + Smk + Hlth
## 
##        Df Deviance    AIC
## - Hlth  4   461.39 473.39
## - Exe   1   462.00 480.00
## <none>      460.39 480.39
## - Age   1   472.97 490.97
## - Hst   1   512.56 530.56
## - Smk   1   580.17 598.17
## - HW    1   614.96 632.96
## 
## Step:  AIC=473.39
## Cancer ~ Age + HW + Hst + Exe + Smk
## 
##        Df Deviance    AIC
## - Exe   1   463.20 473.20
## <none>      461.39 473.39
## - Age   1   474.16 484.16
## - Hst   1   513.91 523.91
## - Smk   1   581.16 591.16
## - HW    1   616.86 626.86
## 
## Step:  AIC=473.2
## Cancer ~ Age + HW + Hst + Smk
## 
##        Df Deviance    AIC
## <none>      463.20 473.20
## - Age   1   475.87 483.87
## - Hst   1   516.15 524.15
## - Smk   1   582.73 590.73
## - HW    1   618.03 626.03
```

Classifies


```r
resp <- predict(back.model, type="response")
head(resp,10)
```

```
##           1           2           3           4           5           6 
## 0.163677830 0.007541106 0.926709960 0.778853270 0.062941720 0.474168752 
##           7           8           9          10 
## 0.995023557 0.064995936 0.008534171 0.160286731
```

adding a condition of more than 50% chance then 1 else 0.


```r
class <- ifelse(resp > 0.5,1,0)
head(class)
```

```
## 1 2 3 4 5 6 
## 0 0 1 1 0 0
```

> Confusion Matrix for Logistic Regression – Backward

creating the confusion matrix.


```r
LR_CF <-table(ExcelFile$Cancer, class, dnn = list("Actual","Predicted"))
LR_CF
```

```
##       Predicted
## Actual   0   1
##      0 291  52
##      1  46 305
```

```r
LR_TP <-LR_CF[2,2]
LR_TN <-LR_CF[1,1]
LR_FP <-LR_CF[1,2]
LR_FN <-LR_CF[2,1]
```

> Naive-Bayes Classification

In regression, the dependent variable is marked as Y and the independent variable is marked as X. 

So, here adding the OT as dependent variable and other variables as the independent variables.


```r
Naive <- NaiveBayes(Cancer ~ .,family="binomial", data=ExcelFile, na.action=na.omit)
```

> Confusion Matrix for Naive-Bayes Classification

Classifies


```r
pred_bay <- predict(Naive,ExcelFile)
```

Creates Confusion Matrix


```r
Naive_CF <- table(Actual=ExcelFile$Cancer, Predicted=pred_bay$class)
Naive_CF
```

```
##       Predicted
## Actual   0   1
##      0 291  52
##      1  47 304
```

```r
Naive_TP <-Naive_CF[2,2]
Naive_TN <-Naive_CF[1,1]
Naive_FP <-Naive_CF[1,2]
Naive_FN <-Naive_CF[2,1]
```

> Linear Discriminant Analysis


```r
LDA <- lda(Cancer ~ .,data = ExcelFile, na.action=na.omit)
```

> Confusion Matrix for Linear Discriminant Analysis

Classifies

```r
pred_dis <- predict(LDA, data=ExcelFile)
```

Confusion Matrix


```r
LDA_CF <- table(Actual=ExcelFile$Cancer, Predicted=pred_dis$class)
LDA_CF
```

```
##       Predicted
## Actual   0   1
##      0 293  50
##      1  49 302
```

Defined


```r
LDA_TP <-LDA_CF[2,2]
LDA_TN <-LDA_CF[1,1]
LDA_FP <-LDA_CF[1,2]
LDA_FN <-LDA_CF[2,1]
```

> Checking which classifier is most accurate.

Accuracy: The proportion of cases correctly classified: (TP+TN)/Total

Accuracy for LR classifier.


```r
LR_ACC <-(LR_TP+LR_TN)/sum(LR_CF)
cat("Logistic Regression: ")
```

```
## Logistic Regression:
```

```r
LR_ACC
```

```
## [1] 0.8587896
```

Accuracy for Naive classifier.


```r
Naive_ACC <-(Naive_TP+Naive_TN)/sum(Naive_CF)
cat("Naive Bayes Classification: ")
```

```
## Naive Bayes Classification:
```

```r
Naive_ACC
```

```
## [1] 0.8573487
```

Accuracy for LDA Classifier.


```r
LDA_ACC <-(LDA_TP+LDA_TN)/sum(LDA_CF)
cat("Linear Discriminant Analysis: ")
```

```
## Linear Discriminant Analysis:
```

```r
LDA_ACC
```

```
## [1] 0.8573487
```

Based on the accuracy values for all three defined classifiers, Linear Discriminant Analysis classifier is the most accurate 
because it's value is slightly higher than other two classifier's accuracy value.

> Checking which classifier minimizes false positives.

False Positive value for Logistic Regression Classifier.


```r
cat("Logistic Regression: ")
```

```
## Logistic Regression:
```

```r
LR_FP
```

```
## [1] 52
```

False Positive value for Naive Bayes Classification.


```r
cat("Naive Bayes Classification: ")
```

```
## Naive Bayes Classification:
```

```r
Naive_FP
```

```
## [1] 52
```

False Positive value for Linear Discriminant Analysis Classifier


```r
cat("Linear Discriminant Analysis: ")
```

```
## Linear Discriminant Analysis:
```

```r
LDA_FP
```

```
## [1] 50
```

Based on the FP(False Positive) values for all three classifiers, 
Linear Discriminant Analysis Classifier has the lowest value for false positives.

> Add Prediction


```r
Test <- read_excel("PROG8430_Final_22F_test.xlsx")
head(Test,10)
```

```
## # A tibble: 10 × 7
##      Age    HW Hst     Exe Smk   Drk   Hlth 
##    <dbl> <dbl> <chr> <dbl> <chr> <chr> <chr>
##  1    43 0.99  0        19 0     0     VG   
##  2    50 0.847 0        29 1     1     A    
##  3    62 0.792 0        20 1     1     G    
##  4    44 0.857 0         5 0     0     VG   
##  5    52 1.06  1       149 0     0     P    
##  6    55 0.963 0        10 0     0     P    
##  7    43 1.23  1        21 0     0     VG   
##  8    51 1.14  0        31 0     0     P    
##  9    50 1.04  0        19 0     1     A    
## 10    41 0.946 0        15 0     0     VG
```




```r
# convert all the char variables to factor.
as.data.frame(unclass(Test),stringsAsFactors = TRUE) 
```

```
##     Age    HW Hst Exe Smk Drk Hlth
## 1    43 0.990   0  19   0   0   VG
## 2    50 0.847   0  29   1   1    A
## 3    62 0.792   0  20   1   1    G
## 4    44 0.857   0   5   0   0   VG
## 5    52 1.065   1 149   0   0    P
## 6    55 0.963   0  10   0   0    P
## 7    43 1.233   1  21   0   0   VG
## 8    51 1.142   0  31   0   0    P
## 9    50 1.038   0  19   0   1    A
## 10   41 0.946   0  15   0   0   VG
## 11   48 0.980   0  45   0   1    A
## 12   42 1.085   0  20   1   1    P
## 13   46 0.972   0  18   0   0    A
## 14   49 1.027   0  29   0   0   VG
## 15   52 1.191   0  22   0   0    A
## 16   40 1.035   0  42   1   1   VG
## 17   40 1.352   0  23   1   1    P
## 18   43 0.982   1  12   0   0    A
## 19   62 1.127   0  12   0   0   VG
## 20   38 0.998   1  49   0   0    A
## 21   41 1.061   0  53   1   1   VG
## 22   40 0.772   1  27   1   1    P
## 23   48 0.927   1  16   1   1    G
## 24   42 1.042   1  60   1   1    A
## 25   37 1.071   1  30   0   0    A
## 26   39 0.943   1  10   1   1   VG
## 27   32 0.928   0  10   1   1    G
## 28   50 0.999   0  15   1   1   VP
## 29   47 0.876   0  11   1   1   VG
## 30   40 0.973   0  20   0   0    G
## 31   34 0.929   1  20   1   1    G
## 32   42 0.872   1  19   1   1    A
## 33   49 0.886   0  62   1   1    P
## 34   56 0.995   1   4   1   1    P
## 35   34 0.974   0  44   1   1    G
## 36   54 1.043   1  23   0   1    A
## 37   55 1.202   0  28   0   0   VG
## 38   43 0.912   0   1   0   0    A
## 39   41 1.105   0   8   0   0    P
## 40   42 1.318   1  65   0   0    P
## 41   30 1.019   0  52   0   0    G
## 42   58 0.952   0  76   1   1    P
## 43   63 0.987   0   3   0   0    G
## 44   39 0.929   0  75   1   1   VP
## 45   52 0.977   0  17   0   0    G
## 46   54 1.027   1  26   1   1    G
## 47   36 1.086   0  77   0   0    A
## 48   40 1.028   0   6   0   0    G
## 49   48 0.979   1  47   0   0    P
## 50   35 1.093   1  22   1   1    G
## 51   38 1.257   1  72   0   0    G
## 52   49 1.003   1  28   1   1    A
## 53   32 1.082   0  32   0   0   VP
## 54   43 1.112   1  32   1   1    A
## 55   51 0.881   0  20   1   1   VG
## 56   48 1.210   0  29   1   1    P
## 57   43 1.140   1  10   1   1   VP
## 58   61 0.974   1  53   1   1    G
## 59   61 0.988   1  22   1   0    P
## 60   53 1.068   0  25   0   0    P
## 61   53 1.000   0  26   0   0    G
## 62   56 1.060   0  64   0   0    P
## 63   47 0.888   0  37   1   1   VP
## 64   49 0.813   0  12   1   1    A
## 65   57 1.023   0  48   1   1    P
## 66   38 1.262   0  37   0   0    G
## 67   42 0.815   0  82   1   1    A
## 68   34 1.078   0  44   0   0    P
## 69   46 0.654   0   5   1   1    G
## 70   60 0.902   0  56   1   1    A
## 71   37 1.065   1  77   0   0    P
## 72   40 0.997   0  31   1   1   VP
## 73   56 1.007   0  35   0   0    A
## 74   50 1.177   1  21   0   0    P
## 75   49 1.060   0  33   1   1    A
## 76   48 0.994   1  43   1   1    P
## 77   42 1.270   0  11   1   1    A
## 78   39 1.046   0  38   1   1   VP
## 79   48 0.823   0  24   1   1    G
## 80   33 1.138   0  12   0   0    G
## 81   44 0.957   0   7   0   0    A
## 82   38 1.264   0  38   0   1    P
## 83   53 1.142   1  20   0   0   VG
## 84   42 0.918   0  37   0   0    P
## 85   49 1.130   1  19   0   0    P
## 86   46 0.940   1  15   1   1    G
## 87   47 1.179   0  15   0   0    A
## 88   56 1.000   0  19   0   0    A
## 89   37 0.801   1  28   1   1    P
## 90   50 1.329   1  10   0   0   VP
## 91   67 0.933   0  10   0   0    G
## 92   49 1.001   0  33   0   0    G
## 93   45 1.052   0  49   0   0   VG
## 94   53 0.891   1  14   1   1   VP
## 95   44 0.945   0  35   1   1    A
## 96   39 1.076   0  34   0   0   VG
## 97   50 0.921   0  36   1   1    A
## 98   49 1.003   0  13   1   1    P
## 99   50 0.885   0  17   1   1    G
## 100  45 1.195   0  32   1   1    G
## 101  50 0.854   1  17   1   1    G
## 102  67 1.010   0  10   1   1    G
## 103  55 1.004   0   9   0   0    G
## 104  61 0.912   0  10   1   1    G
## 105  45 0.968   0  24   1   1   VG
## 106  45 0.948   0  19   1   1    A
## 107  61 0.875   1  68   1   1    G
## 108  53 1.249   1   4   1   1    A
## 109  51 1.043   0  15   0   0   VG
## 110  64 0.919   1  13   0   0    P
## 111  48 1.085   1  25   0   0    G
## 112  41 1.045   0  50   1   1    A
## 113  51 1.089   0  73   1   1    A
## 114  43 1.174   1  24   1   1    A
## 115  46 0.935   1   7   1   1    A
## 116  45 1.262   0  39   0   0    A
## 117  45 0.867   0  26   1   1   VG
## 118  52 1.021   0  25   1   1    G
## 119  35 0.814   0   5   1   1    A
## 120  40 1.108   0  32   0   0    G
## 121  58 0.820   1   1   0   0    P
## 122  41 1.103   0  15   0   0    G
## 123  32 1.046   1  32   1   1    G
## 124  40 1.120   0  33   0   0    G
## 125  28 0.998   0  36   0   0    A
## 126  48 1.257   0  27   0   0    A
## 127  58 1.075   1   0   1   1   VP
## 128  42 1.068   0  43   0   0   VP
## 129  47 1.103   1  85   1   1    A
## 130  36 0.808   0  15   1   1    P
## 131  39 0.892   0  22   1   1    A
## 132  38 1.115   0  38   0   0    A
## 133  42 1.340   0  32   0   0    A
## 134  39 0.985   0  41   1   1    A
## 135  55 1.045   1  53   1   1    A
## 136  50 1.016   0  27   0   0   VG
## 137  40 1.134   0  44   0   0    A
## 138  56 1.045   0  25   0   0    G
## 139  37 0.907   0  33   0   0    G
## 140  55 0.991   0  33   0   0   VG
## 141  59 1.008   1  36   0   0    P
## 142  41 1.093   1  45   1   1    P
## 143  43 0.790   1  27   0   0    P
## 144  50 0.853   1  17   1   1    G
## 145  35 1.001   0  18   0   0   VG
## 146  49 1.131   0  47   0   0    P
## 147  39 1.078   0  10   0   0    G
## 148  53 0.714   0  19   0   0   VP
## 149  47 1.029   1  25   0   0    G
## 150  48 1.046   0  24   1   1    A
## 151  39 1.119   0  40   1   1   VP
## 152  43 1.087   0  21   1   1   VG
## 153  63 1.042   1  24   1   1    A
## 154  61 1.199   1  45   1   1    A
## 155  55 0.866   1  28   0   0    G
## 156  56 0.741   0  13   0   0   VG
## 157  45 0.865   0  19   1   1    A
## 158  57 0.930   1  39   1   1    A
## 159  47 0.922   1  57   1   1    P
## 160  48 1.134   1   8   0   0    P
## 161  45 1.282   0  17   0   0    P
## 162  51 0.936   1  30   1   1    A
## 163  57 1.062   0  13   0   0    A
## 164  47 0.725   0  34   1   1    P
## 165  56 0.910   0  18   0   0   VG
## 166  43 1.081   0  63   0   0    P
## 167  46 1.046   0  64   0   0    G
## 168  53 0.949   0  24   0   0   VG
## 169  41 1.038   0  29   0   0   VG
## 170  40 0.924   1  23   1   1    A
## 171  56 0.814   0  18   1   1   VG
## 172  46 0.905   1   9   1   1    P
## 173  56 0.990   0   7   0   0    A
## 174  43 0.874   0   4   1   1    A
## 175  39 0.829   0  19   1   1   VG
## 176  43 1.034   0  27   1   1    G
## 177  39 0.797   1  34   1   1   VP
## 178  43 1.142   0  20   1   1    A
## 179  48 1.086   0   2   1   1    G
## 180  42 1.134   0  41   1   1   VP
## 181  49 1.097   1  14   1   1   VG
## 182  43 1.056   0  33   0   0    A
## 183  44 1.291   0  38   0   1    G
## 184  53 0.923   1  19   0   0    P
## 185  68 1.006   1  24   1   1   VG
## 186  49 0.891   0  40   1   1   VP
## 187  45 0.901   1  14   1   1   VP
## 188  41 0.908   0   9   1   1    A
## 189  51 1.104   0  18   0   0   VP
## 190  34 1.176   0   7   1   1    A
## 191  46 0.880   0  27   1   1   VP
## 192  40 1.039   0  12   0   0    A
## 193  50 0.934   0  21   1   0    A
## 194  50 1.188   1  22   0   0    P
## 195  36 0.881   0  92   1   1    P
## 196  42 1.046   0  19   0   0    G
## 197  46 1.068   0  24   0   0    A
## 198  48 1.231   1  32   0   0    P
## 199  57 1.008   0  13   1   1    P
## 200  55 0.870   0  60   1   1    G
## 201  47 1.190   1  52   0   0    G
## 202  55 1.215   0  10   0   0   VG
## 203  53 1.146   0  25   0   0   VG
## 204  48 1.063   0 101   0   1    A
## 205  67 1.027   1  25   0   0    P
## 206  46 1.017   0  14   0   0   VG
## 207  55 1.216   0  19   0   0    G
## 208  43 0.850   1  47   1   1   VP
## 209  39 1.246   0  44   0   0   VG
## 210  42 1.133   0  44   0   0    A
## 211  46 0.870   1  26   1   1    G
## 212  46 0.991   1   5   1   1    G
## 213  55 1.068   0  13   0   0    G
## 214  48 1.051   1  15   0   0    P
## 215  50 1.120   0  72   0   0   VP
## 216  52 0.962   0  33   1   1   VG
## 217  48 0.922   0   5   1   1    G
## 218  42 1.349   1  41   0   0    A
## 219  51 0.932   1  16   1   1   VG
## 220  40 1.178   0  17   0   0   VP
## 221  24 1.031   0  28   1   1    A
## 222  37 1.000   1  10   1   1    G
## 223  45 1.006   1  14   1   1    P
## 224  50 0.930   1  28   1   1    A
## 225  56 1.049   0  16   0   0    A
## 226  60 1.053   0  64   1   1   VP
## 227  54 1.165   0  63   1   1    A
## 228  59 1.075   1  13   0   0   VG
## 229  53 1.006   0   6   1   1    G
## 230  50 0.999   1  18   1   1    A
## 231  57 0.971   0   6   0   0   VG
## 232  55 1.152   0  25   0   0   VG
## 233  49 1.067   0  52   0   0    P
## 234  47 1.271   1  16   1   1   VP
## 235  47 1.020   0  23   1   1    G
## 236  35 1.086   0  20   0   0    A
## 237  66 0.947   1  25   1   1    G
## 238  51 0.858   0   4   1   1   VP
## 239  44 1.114   0  15   0   1    G
## 240  49 1.025   0  37   0   0    A
## 241  45 1.090   0  29   0   0    A
## 242  50 1.217   0   9   0   0    P
## 243  44 0.807   1   6   1   1    A
## 244  49 1.083   1  28   0   1    G
## 245  55 1.169   0  41   1   1    A
## 246  39 0.900   0  28   1   1    G
## 247  54 0.927   1  19   1   1   VG
## 248  41 1.169   0   6   1   0    G
## 249  42 1.004   1  36   0   0    G
## 250  55 1.261   0  87   1   1    P
## 251  57 0.820   1   2   0   0    G
## 252  65 1.205   0  26   0   0   VG
## 253  39 1.069   0  39   0   0    G
## 254  37 1.122   0   5   0   0   VP
## 255  56 0.987   0  22   0   0    A
## 256  37 1.070   0  14   0   0   VP
## 257  38 1.082   0  24   1   1    A
## 258  58 1.143   0   9   1   1    P
## 259  46 1.086   0  42   1   1    P
## 260  45 1.138   0  22   0   0    A
## 261  47 1.231   0  37   0   0    P
## 262  42 1.239   0  31   0   0    G
## 263  50 0.808   1   5   1   1    P
## 264  42 0.876   0  12   0   0    A
## 265  40 0.933   0  21   0   0    P
## 266  54 0.858   1   8   1   1    G
## 267  46 1.013   0  18   0   0    A
## 268  37 1.035   0  41   0   0    P
## 269  48 0.917   0  29   1   1    A
## 270  47 1.005   0   5   1   1    P
## 271  49 1.090   0   4   1   1    P
## 272  49 1.136   1  26   1   1    P
## 273  53 0.920   0  52   0   0   VG
## 274  43 1.232   0   9   0   0    A
## 275  35 0.944   0   5   0   0   VP
## 276  52 0.844   0   8   1   1   VG
## 277  52 0.809   0  52   1   1    A
## 278  40 1.270   0  41   1   1    A
## 279  54 1.013   0  24   0   0   VP
## 280  60 0.877   1  41   1   1   VP
## 281  45 0.898   0  62   1   1    P
## 282  50 1.091   1   7   1   0    G
## 283  43 0.979   0  33   1   1    P
## 284  55 0.988   1  11   1   1   VG
## 285  43 1.121   0  53   1   1    A
## 286  37 0.805   0  41   1   1    P
## 287  49 0.928   1  63   1   1   VG
## 288  36 1.007   0  59   1   1    G
## 289  67 1.051   0  15   1   1   VP
## 290  45 0.856   0  32   1   1   VP
## 291  40 1.186   0  26   0   0    G
## 292  34 1.136   0  54   0   0   VP
## 293  50 1.204   1  34   0   1    G
## 294  59 0.847   1  24   1   1    G
## 295  52 1.318   1  21   0   0   VG
## 296  52 1.184   0  59   0   0   VG
## 297  52 1.042   0  15   1   1    G
## 298  41 1.290   0  57   0   0   VG
## 299  65 0.809   0  26   1   1   VG
## 300  40 1.099   0  52   0   0    A
## 301  59 1.054   1   8   1   1   VG
## 302  69 0.870   0  27   1   1    A
## 303  55 1.144   0   4   1   1    A
## 304  55 1.167   0  28   0   1   VG
## 305  38 1.096   0  49   0   0    A
## 306  50 0.833   0  37   1   1    G
## 307  42 1.126   1  20   1   1    A
## 308  58 0.941   0  52   0   0    G
## 309  39 1.250   0  17   1   1    P
## 310  52 0.992   1  33   0   0    G
## 311  43 0.925   0  48   0   0    G
## 312  55 0.959   0  40   0   0    G
## 313  48 1.161   0  31   0   1    G
## 314  56 1.044   0  24   1   1    A
## 315  55 1.040   1  47   1   1    A
## 316  51 0.832   0  56   1   1    G
## 317  40 0.843   1  17   0   0    P
## 318  54 0.930   0  44   1   1   VG
## 319  35 0.852   0  12   1   1    G
## 320  45 1.208   0  13   0   0    A
## 321  51 1.356   0  49   0   0    G
## 322  55 1.241   0  42   0   0    G
## 323  48 0.972   0  57   1   1    A
## 324  45 1.050   1  42   0   0    P
## 325  48 1.076   0  90   0   0   VP
## 326  57 1.067   0  78   1   1   VG
## 327  39 1.241   0  56   0   0   VP
## 328  42 0.900   1  44   1   1    G
## 329  54 0.890   1  25   0   0    G
## 330  33 1.090   0  18   0   0    G
## 331  54 0.874   0  85   1   1    A
## 332  41 0.751   1  14   0   0    P
## 333  48 1.079   0  24   1   1    G
## 334  53 0.891   0  55   1   1    G
## 335  51 1.067   0  37   1   1   VP
## 336  59 0.914   0  16   1   1    A
## 337  28 1.088   1  66   0   0   VG
## 338  40 1.165   1  34   1   1   VG
## 339  42 1.176   0  81   0   0    P
## 340  44 0.918   0  19   0   0    G
## 341  48 0.801   1  28   1   1    G
## 342  42 0.986   0  34   0   0    A
## 343  80 1.089   1  29   1   1    A
## 344  58 1.118   0  12   0   0    G
## 345  44 1.188   0  28   0   0    P
## 346  48 1.204   0  34   0   0    A
## 347  47 0.915   0  18   1   1    A
## 348  46 1.062   0  28   0   0    A
## 349  56 0.775   1  18   1   1    G
## 350  44 1.041   0   7   1   1   VP
## 351  48 0.966   0  11   1   1    G
## 352  57 0.922   1  15   1   1    A
## 353  35 1.083   0  13   1   1    G
## 354  54 0.979   1   5   1   1    G
## 355  54 1.089   0  25   0   0    P
## 356  45 0.842   1  82   1   1   VG
## 357  44 1.043   0  40   0   0    P
## 358  49 1.191   0  11   0   1    G
## 359  44 0.848   1  23   1   1    G
## 360  42 1.012   1   2   0   0    A
## 361  37 1.095   0  12   1   1    G
## 362  49 1.238   1  32   1   1    A
## 363  68 1.082   0  32   1   1   VG
## 364  57 0.951   0  62   0   0   VG
## 365  52 0.873   0  19   1   1    P
## 366  53 0.851   1  31   1   1    G
## 367  55 0.864   1  13   1   1    A
## 368  56 1.233   0 149   0   0    A
## 369  54 1.017   1  10   1   1    G
## 370  38 1.155   1  16   0   1    A
## 371  59 1.124   0   2   0   0    A
## 372  44 1.223   1  17   0   0   VG
## 373  44 0.826   1   7   0   0    P
## 374  48 0.912   0  18   1   1   VG
## 375  57 0.831   1  34   1   1   VP
## 376  37 0.896   1  82   1   1    G
## 377  54 0.858   0  49   0   0    G
## 378  43 1.197   0  51   0   0    A
## 379  30 0.992   0  41   0   0    A
## 380  46 0.994   0  11   1   1    G
## 381  41 1.055   1  34   1   1    G
## 382  54 1.314   0  10   0   0    P
## 383  50 1.027   0  70   1   1    A
## 384  50 1.265   0  20   0   0    G
## 385  41 0.901   1  57   0   0    G
## 386  32 1.087   0   7   0   0   VG
## 387  43 0.870   1  73   0   0   VG
## 388  43 1.216   0  25   0   0    A
## 389  46 1.070   0   7   1   1   VG
## 390  48 0.923   0  23   1   1    P
## 391  55 0.963   0  30   1   1   VG
## 392  43 1.062   0   8   0   0   VG
## 393  36 1.195   1  34   1   1    A
## 394  39 1.192   0  21   0   0    P
## 395  52 0.951   0  10   1   1   VG
## 396  44 0.873   0  24   1   1    P
## 397  44 0.996   1   9   0   0   VP
## 398  43 1.012   0  27   0   0    A
## 399  37 1.129   0  16   0   0    G
## 400  47 1.214   0  19   1   1    A
## 401  46 0.901   1   7   1   1    A
## 402  48 1.125   1  38   0   0    A
## 403  54 0.897   1  20   1   1   VP
## 404  40 1.113   1  50   1   1   VP
## 405  47 0.944   0  12   0   1   VP
## 406  51 1.022   1  17   1   1    A
## 407  42 1.219   1  11   0   0   VG
## 408  59 1.269   0   7   0   1    P
## 409  32 0.956   1  19   0   0   VG
## 410  50 1.153   1  14   0   0    P
## 411  44 1.024   0  30   0   1    A
## 412  76 1.063   0  73   1   1   VP
## 413  44 1.126   0  59   1   1    A
## 414  57 1.002   1  12   1   1   VG
## 415  35 1.183   0  64   0   1   VG
## 416  34 1.045   1  55   1   1    A
## 417  43 0.833   1  35   1   1    G
## 418  37 1.073   0   7   0   0    A
## 419  52 1.015   0  21   0   0    P
## 420  50 1.185   0  26   1   1    A
## 421  60 1.086   0  21   0   0    A
## 422  68 0.909   0  30   1   1    A
## 423  32 1.024   0  42   0   0    A
## 424  51 1.075   0  68   0   0   VP
## 425  51 0.905   1  40   1   1    P
## 426  60 0.856   1  34   1   1    G
## 427  54 1.039   1  43   0   0   VG
## 428  39 1.210   0  93   0   0   VG
## 429  51 1.045   0  51   1   1    G
## 430  43 1.080   0  95   1   1    G
## 431  42 1.141   0  52   0   0    G
## 432  37 1.232   0  38   0   0    P
## 433  49 0.886   0  37   1   1    G
## 434  48 1.065   0   6   0   0   VG
## 435  48 0.672   1   9   1   1   VG
## 436  56 0.762   0  54   0   0    A
## 437  47 0.910   1  21   1   1    A
## 438  45 0.981   0  19   1   1    A
## 439  42 0.871   1  65   0   0   VG
## 440  60 0.776   1  45   1   1    A
## 441  50 1.058   1  36   0   0    A
## 442  41 0.817   1  33   1   1    P
## 443  54 1.182   1  21   1   1    A
## 444  51 0.973   0  27   0   0    A
## 445  47 0.978   0  40   0   0    G
## 446  48 0.711   1   8   1   1   VP
## 447  50 1.025   1  21   1   1   VG
## 448  42 1.053   1  47   1   1    G
## 449  48 1.040   1  22   1   1    P
## 450  50 0.742   1  25   1   1    G
## 451  53 1.060   0  49   0   0   VG
## 452  46 1.031   1  41   1   1    P
## 453  54 1.034   1  68   1   1    G
## 454  47 1.170   0  20   0   0    A
## 455  46 0.919   1  23   1   1    A
## 456  52 1.085   1  36   0   0    G
## 457  41 1.135   1  39   0   0    P
## 458  43 1.094   0  28   0   1   VP
## 459  56 0.979   0   6   1   1    A
## 460  46 0.825   1   5   1   1    P
## 461  49 1.239   0  12   0   0    G
## 462  55 1.217   0  23   1   1    G
## 463  41 1.162   1  33   1   1    A
## 464  43 0.881   0  31   1   1    G
## 465  34 0.961   0  39   1   1   VG
## 466  52 1.238   0   7   0   1    G
## 467  59 0.954   0  41   1   1    A
## 468  35 0.908   0  41   0   0    G
## 469  50 1.276   0  15   0   0   VP
## 470  46 1.023   0   8   0   0   VP
## 471  45 1.172   1   6   0   0   VP
## 472  51 1.169   1  27   1   1   VG
## 473  50 1.209   0  13   0   1    P
## 474  48 1.102   0  34   0   0    G
## 475  58 0.969   0  29   1   1    A
## 476  63 1.019   0  27   1   1    A
## 477  46 1.045   1  27   0   0    P
## 478  67 0.774   0  27   1   1   VG
## 479  48 1.151   1  17   1   1    A
## 480  52 1.076   0  39   0   0    P
## 481  53 1.006   0  22   1   1    A
## 482  50 1.017   1   5   1   1   VG
## 483  57 1.213   0  86   1   1    G
## 484  63 0.879   1  11   1   1    A
## 485  55 0.946   0  18   0   0    A
## 486  40 0.862   1  36   1   1   VG
## 487  33 0.972   0  41   0   0    A
## 488  38 0.888   0  41   1   1    G
## 489  58 0.880   0  16   1   1   VG
## 490  44 0.919   1  41   1   1    P
## 491  55 0.813   0  22   1   1    A
## 492  68 0.964   1  44   0   0    A
## 493  49 1.218   0  53   0   0    P
## 494  49 1.215   0  19   0   0   VG
## 495  63 0.735   0  26   1   1   VG
## 496  38 1.041   0  52   1   1   VP
## 497  55 1.109   0  18   0   0    G
## 498  52 0.892   0   9   1   1   VG
## 499  39 0.975   0  11   1   1    G
## 500  35 0.967   0   5   0   0    G
## 501  55 1.048   0  28   1   1    A
## 502  52 1.233   0  34   0   0    P
## 503  32 1.163   0  19   0   0   VP
## 504  48 1.041   0  31   0   1    G
## 505  60 1.068   0  39   0   0    P
## 506  60 1.169   1  18   0   0   VG
## 507  46 0.922   1  59   0   0    A
## 508  50 0.983   0  28   0   0    G
## 509  41 1.180   0  11   0   0    P
## 510  59 0.983   0  35   0   1    P
## 511  58 0.754   0  26   1   1    G
## 512  49 1.134   1  16   1   1   VP
## 513  37 0.890   0  19   1   1    P
## 514  50 0.858   1  35   0   1    G
## 515  48 1.079   0  32   0   0    G
## 516  40 1.117   0   6   0   0   VP
## 517  50 1.044   0  15   0   1    G
## 518  43 1.170   0  24   0   0    G
## 519  45 1.042   0   3   1   1    A
## 520  49 1.129   0  30   0   0    A
## 521  53 0.845   0   7   1   1    G
## 522  61 1.137   0   8   0   0    G
## 523  62 0.698   0   5   1   1    A
## 524  52 0.978   1  10   1   1    P
## 525  55 0.981   1   5   0   0    G
## 526  53 1.082   0   5   0   0    P
## 527  69 0.791   1  35   1   1    A
## 528  49 0.972   1   6   0   1   VG
## 529  34 1.092   0  30   0   0    G
## 530  55 1.010   1  50   1   1   VP
## 531  47 1.121   0  40   0   0    A
## 532  74 1.173   0   8   0   1    G
## 533  50 1.200   0  12   0   0    P
## 534  32 0.911   0 126   1   1    G
## 535  54 0.992   0   9   0   0   VG
## 536  31 0.996   0   7   0   0    G
## 537  52 1.041   1  26   1   1    P
## 538  46 1.115   1  54   0   0   VP
## 539  46 0.986   0 141   1   1    A
## 540  48 1.119   0  25   0   0    A
## 541  46 1.045   1  64   1   1    P
## 542  47 0.963   0   6   1   1   VG
## 543  49 1.078   1  53   1   1    A
## 544  56 0.974   0  15   1   1    P
## 545  44 0.912   1  44   1   1    P
## 546  49 1.052   0  79   0   0    A
## 547  44 0.973   1  11   1   1    G
## 548  49 1.101   1  16   0   0    A
## 549  42 1.119   0  18   1   1   VP
## 550  55 0.764   0  65   1   1   VP
## 551  38 0.910   1  23   0   0    G
## 552  55 1.159   0  42   0   0    G
## 553  39 1.126   0  20   0   0    P
## 554  36 1.127   0   9   0   0    P
## 555  53 1.112   0  54   0   0   VP
## 556  57 1.049   1  23   1   1    A
## 557  49 0.993   0  12   1   1    A
## 558  41 1.164   0  38   0   0    G
## 559  49 1.011   0  23   0   0    G
## 560  65 0.932   0  20   1   1   VP
## 561  61 1.037   0  19   1   1    P
## 562  50 0.909   0  32   1   1    A
## 563  31 0.941   0  25   1   1   VP
## 564  61 0.985   0  85   1   1    A
## 565  42 1.274   0   9   0   0    A
## 566  50 1.050   0  23   1   1    A
## 567  37 0.809   1  13   1   1   VG
## 568  46 1.089   0  20   1   1   VP
## 569  52 1.140   0  31   0   0    P
## 570  59 1.059   1  51   0   0    P
## 571  36 1.021   1  28   1   1   VG
## 572  46 1.219   0   3   0   0    G
## 573  52 1.033   0  21   1   1   VG
## 574  55 0.871   0  22   1   1   VG
## 575  61 0.997   1   5   1   1   VG
## 576  40 1.068   0  17   1   1    G
## 577  42 1.017   0  71   1   1    A
## 578  58 0.923   0  63   1   1    G
## 579  53 1.099   1  13   1   1    A
## 580  36 1.058   0  12   0   0    G
## 581  46 1.087   0  24   1   1    P
## 582  46 1.164   0  31   0   0   VG
## 583  51 0.836   0  32   1   0    G
## 584  58 1.061   0  21   1   1    A
## 585  65 0.952   1  12   1   1    G
## 586  50 1.228   0  35   0   0    P
## 587  58 0.906   1  45   1   1   VP
## 588  45 0.889   1  29   0   0   VP
## 589  32 0.970   0   5   1   1    A
## 590  38 1.104   0  49   0   0   VP
## 591  31 1.150   0  22   0   0    A
## 592  34 1.231   1  55   1   1    G
## 593  47 0.918   1  25   1   1    A
## 594  62 1.031   1  35   0   0    P
## 595  51 0.994   0  23   1   1   VP
## 596  56 1.092   1  38   0   1   VP
## 597  53 0.869   1  13   1   1    A
## 598  50 1.203   0  13   0   0   VP
## 599  53 1.249   0  71   0   0    P
## 600  47 0.871   0  47   1   1   VG
## 601  58 1.021   1  85   1   1    A
## 602  40 1.157   1  15   1   1    P
## 603  46 0.974   0  17   1   1   VG
## 604  38 1.000   1  73   0   0   VG
## 605  58 1.015   1  20   1   1    A
## 606  60 1.034   1  23   0   0    A
## 607  42 1.002   0  16   0   0   VG
## 608  52 1.127   0   9   0   0    G
## 609  53 0.968   1   8   1   1    G
## 610  43 1.045   0   6   0   0   VP
## 611  51 1.054   0  40   1   1    P
## 612  54 0.890   0   3   1   1   VG
## 613  44 1.130   0  41   1   1    G
## 614  38 0.843   0  10   0   0    P
## 615  55 0.887   0  40   1   1    A
## 616  54 1.162   0  52   1   1    A
## 617  67 1.062   1  29   0   0   VP
## 618  28 1.067   0  43   0   0    A
## 619  64 1.020   1  23   0   0    P
## 620  62 0.790   1  25   0   0   VG
## 621  49 0.951   0  62   1   1    P
## 622  44 1.093   0  27   0   1   VG
## 623  41 1.038   0  16   0   0    G
## 624  44 0.696   1  31   0   0    P
## 625  45 1.108   0  99   0   0    P
## 626  55 0.862   0 130   1   1    G
## 627  45 1.007   0  57   0   0    A
## 628  44 1.090   0  52   0   0    P
## 629  35 0.932   0  21   0   0   VP
## 630  52 1.027   0  33   1   0    A
## 631  52 0.845   1  40   1   1    A
## 632  38 1.045   1  24   1   1    P
## 633  43 0.904   1   4   1   1    A
## 634  40 1.239   0  45   0   0    P
## 635  54 1.079   0   9   0   0    A
## 636  43 1.116   0   9   0   0   VG
## 637  57 0.979   1  19   1   1   VG
## 638  53 0.970   1  11   1   1    G
## 639  46 1.085   0  21   0   0    G
## 640  66 0.876   1  73   1   1    P
## 641  35 0.834   0  14   1   1    P
## 642  52 1.161   0  42   0   0    G
## 643  40 1.236   0  43   1   1    P
## 644  47 0.816   0  10   1   1   VG
## 645  61 0.988   0  48   0   0    A
## 646  55 1.013   1   7   1   1   VG
## 647  49 1.025   0  22   1   1   VG
## 648  38 1.094   0  34   0   0    A
## 649  43 1.166   1  38   0   0    A
## 650  52 1.190   1   6   0   0    A
## 651  54 1.158   0  17   1   1    P
## 652  47 1.023   1  31   1   1   VG
## 653  36 0.880   0  61   0   0    G
## 654  46 0.978   0  15   0   0   VG
## 655  34 0.976   1   1   1   1    G
## 656  45 1.346   0  27   1   1    A
## 657  43 0.768   1   8   1   1    G
## 658  58 0.900   0  16   0   0    G
## 659  39 0.808   0   6   0   0   VP
## 660  39 0.950   0  75   1   1    G
## 661  28 0.861   1  34   1   1    P
## 662  46 1.083   0  18   1   1    G
## 663  49 0.856   0   3   1   1    P
## 664  44 0.978   0   7   0   0    A
## 665  26 1.234   0  47   0   0    A
## 666  45 0.926   0  11   0   1    A
## 667  44 1.056   1  17   0   0   VG
## 668  36 1.153   0  26   0   0   VG
## 669  50 0.999   1  20   0   0    G
## 670  41 0.952   0  49   1   1    G
## 671  50 1.173   0  32   0   0   VG
## 672  39 1.014   0  56   0   0    G
## 673  58 0.972   0  85   0   0    A
## 674  52 0.903   0  26   1   1   VG
## 675  54 0.980   1  74   1   1    A
## 676  61 0.979   1   4   0   0    P
## 677  39 1.062   1  22   0   0    G
## 678  51 0.886   1  53   1   1    G
## 679  51 0.895   0  23   0   0    A
## 680  38 0.903   0  34   0   0    A
## 681  55 0.965   0  24   1   1   VP
## 682  47 0.907   1  42   1   1   VG
## 683  48 1.104   0  53   1   1   VG
## 684  39 0.881   0  17   0   0    G
## 685  46 1.083   0  80   0   0    G
## 686  57 1.031   0  23   0   0    A
## 687  40 1.077   0  38   0   0    A
## 688  46 0.802   0  50   1   1    P
## 689  33 1.042   1   4   1   1    G
## 690  50 0.813   1 116   0   0   VG
## 691  45 1.079   0   4   0   0    P
## 692  62 0.810   1  63   1   1    P
## 693  44 1.080   0  21   0   0    P
## 694  40 1.011   0   6   0   0    P
## 695  51 1.096   1  42   1   0    P
## 696  43 1.155   0  23   0   0    G
## 697  47 1.116   0  31   1   1   VG
## 698  50 1.049   0  26   0   0    A
## 699  43 1.142   0   7   0   0    P
## 700  46 0.909   0  17   1   1   VG
## 701  49 1.018   1  23   1   1    P
## 702  52 0.868   1  59   1   0    A
## 703  40 0.976   1  27   1   1    P
## 704  58 0.844   1  40   1   1   VP
## 705  57 0.858   1  77   1   1    A
```

```r
Test$Hst <- as.numeric(Test$Hst)
Test$Smk <- as.numeric(Test$Smk)

Test1 <-as.data.frame(unclass(Test),stringsAsFactors=TRUE)

# Predicting a cancer values for new data using backward model that I have created previously. 
Cancer <- predict(back.model, newdata=Test1)
head(Cancer)
```

```
##          1          2          3          4          5          6 
## -1.5189399  3.3072872  4.7336655  0.4015572 -0.2702163 -0.4853899
```

```r
Test1 <- cbind(Test,Cancer)

write.xlsx(Test1,"PROG8430_Final_22F_Pred.xlsx")

head(Test1)
```

```
##   Age    HW Hst Exe Smk Drk Hlth     Cancer
## 1  43 0.990   0  19   0   0   VG -1.5189399
## 2  50 0.847   0  29   1   1    A  3.3072872
## 3  62 0.792   0  20   1   1    G  4.7336655
## 4  44 0.857   0   5   0   0   VG  0.4015572
## 5  52 1.065   1 149   0   0    P -0.2702163
## 6  55 0.963   0  10   0   0    P -0.4853899
```

