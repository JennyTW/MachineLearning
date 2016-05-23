---
title: 'Machine Learning : Predict Activity'
author: "Jenny Chen"
date: "May 22, 2016"
output: html_document
---
#1.Overview
In this project, we take the dataset from activity sensor device and build up a algoritm to predict the "classes" variable with given dataset.

#2.Load and Clean DataSet

- The training data for this project are available here:

  https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

- The test data are available here:

  https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

- source: http://groupware.les.inf.puc-rio.br/har. 

```{r}
# Load Library
library(caret)
library(randomForest)
library(rpart)
library(rpart.plot)

# Load and Read Dataset
train<-read.csv("~/R/data/ML/Train.csv",na.strings=c("#DIV/0","", "NA"))
test<-read.csv("~/R/data/ML/Test.csv",na.strings=c("#DIV/0","", "NA"))

# Clean data
## Removing Zero Covariates
nzv_train <- nearZeroVar(train, saveMetrics=TRUE)
train <- train[,nzv_train$nzv==FALSE & nzv_train$zeroVar==FALSE]
test <- test[,nzv_train$nzv==FALSE & nzv_train$zeroVar==FALSE]

## Remove any columns with more than 50% NAs.
Good <- lapply(train, function(x) sum(is.na(x)) / length(x)) <= 0.5
train <- train[ Good ]
test  <- test[ Good ]

```


#3.Partition training dataset for cross validation
```{r}
# Split Train into training and testing subset for cross validation
set.seed(888)
inTrain <- createDataPartition(y=train$classe, p=0.6, list=FALSE)
t_train <- train[inTrain, ] 
t_test  <- train[-inTrain, ]
```

#4.Build prediction model:decision tree

```{r}
# Build model
Fit <- rpart(classe ~ ., data=t_train, method="class")

# Cross validation
x <- predict(Fit, t_test, type = "class")
y<-t_test$classe
table(x,y)
confusionMatrix(x,y)
```

#5.Apply to Testing dataset

```{r}
z<-predict(Fit,newdata = test)
```
