---
title: K Nearest Neighbors 演算法
date: 2018-10-11 17:07:46
tags:
- Machine Learning
- KNN
---




## 基本概念

如圖將Node分成A、B類，根據X1、X2兩種特徵標出待測物(星號標示)，重點在於**k值選擇**，若k = 3則相鄰B類Node數量較多，故預測其為B類，k = 6則相鄰A類Node較多，所以預測為A類

![]()
<img height = '300' src = 'https://i.imgur.com/nXHMFUL.png'/>

> dataset來源: 隨機產生，KNN演算法的實行上不必知道各種feature是什麼 
> [Download](https://drive.google.com/file/d/1QLAM-oArV21zE11WBGzt4H1sxswSHg_p/view?usp=sharing)

## 引入函式庫&載入資料

```python=
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns


df = pd.read_csv('KNN_Dataset')
df.head()
```
<!-- more -->
![](https://i.imgur.com/GvXXIwL.png)


## 初步資料分析

```python=
sns.pairplot(data = df, hue = 'TARGET CLASS')
```

![](https://i.imgur.com/63z7Jxj.jpg)

## 標準化變數

數值型變數標準化，避免ＫＮＮ距離計算時因為單位不同造成失真

可以使用Scikit learn內建標準化功能


```python=
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(df.drop('TARGET CLASS', axis=1)) 
# 用.transform()方法回傳標準化後的features
scaler_features = scaler.transform(df.drop('TARGET CLASS', axis=1))

# 重新整理dataFrame
df_feat = pd.DataFrame(scaler_features, columns=df.columns[:-1]) #columns和原本一樣
df_feat.head()
```

![](https://i.imgur.com/4UadedD.png)


## 拆分train data與test data

```python=
from sklearn.model_selection import train_test_split

X = df_feat
y = df['TARGET CLASS']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3) # 隨機取30%的資料為test data
```

## 訓練KNN模型


```python=
from sklearn.neighbors import KNeighborsClassifier

#一開始先定k值為1，之後會再測試準確率最高的k值
knn = KNeighborsClassifier(n_neighbors = 1)
knn.fit(X_train, y_train)
```

## 預測與評估

```python=
pred = knn.predict(X_test)

from sklearn.metrics import classification_report, confusion_matrix
print(confusion_matrix(y_test, pred))
```
confusion matrix:
>  [[109  43]
>  [ 41 107]]
 
```python=
print(classification_report(y_test, pred))
```

             precision    recall  f1-score   support

          0       0.73      0.72      0.72       152
          1       0.71      0.72      0.72       148

avg / total       0.72      0.72      0.72       300

## 選用適合的k值

```python=
error_rate = []


for i in range(1, 40): # range夠大即可，一般選1~40 
    
    knn = KNeighborsClassifier(n_neighbors=i)
    knn.fit(X_train, y_train)
    pred_i = knn.predict(X_test)
    #np.mean(pred_i != y_test)為預測失敗率
    error_rate.append(np.mean(pred_i != y_test))
```

圖表化k值對其預測失敗機率

```python=
plt.figure(figsize=(10, 6))
plt.plot(range(1, 40), error_rate, color = 'blue', linestyle= '--', marker = 'o', markerfacecolor = 'red', markersize = 10)
plt.title('Error Rate vs. K Value')
plt.xlabel('K')
plt.ylabel('Error Rate')
```
![](https://i.imgur.com/SGDIlWa.png)

可看出k=30時失敗率最低(預測精確度最高)
