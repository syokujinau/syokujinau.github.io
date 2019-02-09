---
title: Logistic Regrassion - 廣告點擊資料分析&預測
date: 2018-10-11 17:09:28
tags:
- Machine Learning
- Logistic Regrassion
---



> Dataset來源: 隨機產生的虛擬資料 [Download](https://drive.google.com/file/d/1Yc9EhJb00es-M18veTIwzG4JTPpILzlJ/view?usp=sharing)
> 目標是預測一個使用者是否有點擊廣告
 
## 引入函式庫

```python=
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```
<!-- more -->
## 讀取資料

```python=
ad_data = pd.read_csv('advertising.csv')
ad_data.head()
```

![](https://i.imgur.com/n4RRkAz.png)

### 欄位說明
* 'Daily Time Spent on Site': consumer time on site in minutes
* 'Age': cutomer age in years
* 'Area Income': Avg. Income of geographical area of consumer
* 'Daily Internet Usage': Avg. minutes a day consumer is on the internet
* 'Ad Topic Line': Headline of the advertisement
* 'City': City of consumer
* 'Male': Whether or not consumer was male
* 'Country': Country of consumer
* 'Timestamp': Time at which consumer clicked on Ad or closed window
* 'Clicked on Ad': 0 or 1 indicated clicking on Ad


## 初步資料分析

### 產生年齡的histgram

```python=
ad_data['Age'].plot.hist(bins = 30)
plt.xlabel('Age')
```

![](https://i.imgur.com/XmMCj0o.png)


### 產生Area Income對Age的joint plot

```python=
sns.jointplot(x = 'Age', y = 'Area Income', data = ad_data)
```

![](https://i.imgur.com/87k2pkH.png)




### 產生 Daily Time spent on site對Age的joint plot(以kde分布表示)

```python=
sns.jointplot(x = 'Age', y= 'Daily Time Spent on Site', data = ad_data, kind = 'kde')
```

![](https://i.imgur.com/LyqOGbP.png)

### 產生hue設為'Clicked on Ad'的pairplot

```python=
sns.pairplot(ad_data, hue = 'Clicked on Ad')
```

![](https://i.imgur.com/WXawj9A.png)




## 訓練Logistic Regression 模型

### 從dataset中切分出train data、test data

> 採用數值資料、'Ad Topic Line'這種字串無法學習，需要data clean將之去除，或者是想辦法轉換成dummy variable

```python=
from sklearn.model_selection import train_test_split

X = ad_data[['Daily Time Spent on Site', 'Age', 'Area Income', 'Daily Internet Usage', 'Male']]
y = ad_data['Clicked on Ad']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3, random_state = 101)
```

### 載入Logistic Regression模型

```python=
from sklearn.linear_model import LogisticRegression
logmodel = LogisticRegression()
logmodel.fit(X_train, y_train)
```

## 預測與評估

### 輸入test set產生一組預測資料
```python=
predictions = logmodel.predict(X_test)
```

### 產生分類報告與confusion matrix

接著拿出test set的y_test，即所謂實際結果，並用上面的predictions比較，計算出預測成功率



```python=
from sklearn.metrics import classification_report, confusion_matrix
print(classification_report(y_test, predictions))
```

![](https://i.imgur.com/XPt3s4G.png)

```python=
print(confusion_matrix(y_test, predictions))
```

![](https://i.imgur.com/6qaRN53.png)


<img height = '180' src = https://i.imgur.com/OYpeupc.png/>