---
title: "Practical Machine Learning Assigment"
---

1.Import library and data
The first thing to do is to import the library and data from the csv file.
```{r}
library(caret)
library(randomForest)
library(ggplot2)
library(dplyr)

train_data <- read.csv("pml-training.csv", na.strings = c("NA", "", "#DIV/0!"))
test_data <- read.csv("pml-testing.csv", na.strings = c("NA", "", "#DIV/0!"))
```

2.Clean the data
The next step is to clean the data with NA values and non-predictors.
```{r}
train_data <- train_data[, colSums(is.na(train_data)) == 0]
test_data <- test_data[, colSums(is.na(test_data)) == 0]

train_data <- train_data[, -c(1:7)]
test_data <- test_data[, -c(1:7)]
```

3.Split data and validation
The next step is Split data and validation. Split the data into training set (70%) and validation set (30%) then do cross validation.
```{r}
set.seed(1234)
trainIndex <- createDataPartition(train_data$classe, p = 0.7, list = FALSE)
trainSet <- train_data[trainIndex, ]
validSet <- train_data[-trainIndex, ]
```

4. Make Random Forest Model
In this case I use a Random Forest Model with 100 branches because it has high accuracy, is resistant to overfitting, and is easy to use. Before creating a Random Forest Model, the target variable will be checked as a factor value.

```{r}
trainSet$classe <- as.factor(trainSet$classe)
validSet$classe <- as.factor(validSet$classe)

model_rf <- randomForest(classe ~ ., data = trainSet, importance = TRUE, ntree = 100)
```

5. Model Evaluation
After the model is trained on the training set, test the evaluation model by calculating the confusion matrix.
```{r}
pred_valid <- predict(model_rf, validSet)
confusionMatrix(pred_valid, validSet$classe)
```

6. Data Prediction
prediction using the model on the test data and the results will be displayed.
```{r}
pred_test <- predict(model_rf, test_data)
print(pred_test)
```
