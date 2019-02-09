---
title: Support-Vector-Machine演算法 - 紅酒種類預測
date: 2018-10-12 17:05:41
tags:
- Machine Learning
- SVM
---


> scikit-learn 內建dataset : wine dataset


## 函式庫

```python=
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## 載入dataset

```python=
from sklearn.datasets import load_wine
data = load_wine()
data.keys()
# print(data.DESCR)
```
<!-- more -->
```
df = pd.DataFrame(data.data, columns=data.feature_names)
df['target'] = data.target
df.head()
```

![](https://i.imgur.com/udO4dut.png)

```python=
sns.pairplot(df, hue='target')
```
![](https://i.imgur.com/pNpWFj6.jpg)


## 切分訓練、測試資料


```python=
from sklearn.model_selection import train_test_split

X = df.drop('target', axis=1)
y = df['target']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
```

## SVM model

```python=
from sklearn.svm import SVC
model = SVC()
model.fit(X_train, y_train)
```

## 預測與評估

```python=
predictions = model.predict(X_test)
```

```python=
from sklearn.metrics import  classification_report, confusion_matrix
print(confusion_matrix(y_test, predictions))
```

```
[[ 1 18  0]
 [ 0 21  0]
 [ 0 13  1]]
```
```python=
print(classification_report(y_test, predictions))
```

```
             precision    recall  f1-score   support

          0       1.00      0.05      0.10        19
          1       0.40      1.00      0.58        21
          2       1.00      0.07      0.13        14

avg / total       0.77      0.43      0.29        54
```

## Gridsearch

SVM預設參數可能如上面結果效果不佳，scikit-learn提供方法幫助我們找出適合的C、gamma參數的組合，這種方法稱Gridsearch，可以在==GridSearchCV==調用功能，程式如下

```python=
from sklearn.grid_search import GridSearchCV

param_grid = {'C': [0.1, 1, 10, 100, 1000, 10000], 'gamma':[1, 0.1, 0.01, 0.001, 0.0001, 0.00001], 'kernel': ['rbf']}

grid = GridSearchCV(SVC(), param_grid, verbose=3)
grid.fit(X_train, y_train)
```

到此步驟grid已經套用最好的C、gamma組合

```python=
grid_predictions = grid.predict(X_test)
print(confusion_matrix(y_test, grid_predictions))
```

```
[[17  2  0]
 [ 0 21  0]
 [ 0  0 14]]
```

```python=
print(classification_report(y_test, grid_predictions))
```

```
             precision    recall  f1-score   support

          0       1.00      0.89      0.94        19
          1       0.91      1.00      0.95        21
          2       1.00      1.00      1.00        14

avg / total       0.97      0.96      0.96        54
```

預測效果相比預設參數有大幅改善